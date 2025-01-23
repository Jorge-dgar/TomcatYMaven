---

# README: Cómo configurar y desplegar aplicaciones con Vagrant, Tomcat y Maven

Este archivo explica cómo configurar un entorno de desarrollo utilizando **Vagrant**, **Tomcat**, y **Maven**, siguiendo un flujo automatizado. Abarca desde la instalación inicial de herramientas hasta el despliegue de aplicaciones web en un servidor Tomcat.

---

## **1. Requisitos previos**
Antes de comenzar, asegúrate de tener los siguientes programas instalados en tu sistema:

1. **Vagrant**: Herramienta para gestionar máquinas virtuales.  
   Descárgalo desde [vagrantup.com](https://www.vagrantup.com/).

2. **VirtualBox**: Proveedor de máquinas virtuales.  
   Descárgalo desde [virtualbox.org](https://www.virtualbox.org/).

3. **Git**: Sistema de control de versiones para clonar repositorios.  
   Descárgalo desde [git-scm.com](https://git-scm.com/).

Verifica su instalación ejecutando en la terminal:
```bash
vagrant --version
virtualbox --help
git --version
```

---

## **2. Configuración del entorno**

### Paso 1: Crear el archivo `Vagrantfile`
El `Vagrantfile` define una máquina virtual basada en Debian con configuración automatizada para instalar Java, Maven, y Tomcat. Utiliza el siguiente contenido como tu `Vagrantfile`:

```ruby
# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  # Define la caja base
  config.vm.box = "debian/bullseye64"

  # Configura el reenvío de puertos
  config.vm.network "forwarded_port", guest: 8080, host: 8080

  # Configuración de la máquina virtual
  config.vm.provider "virtualbox" do |vb|
    vb.memory = "1024"  # Asigna memoria a la VM
  end

  # Provisión de la VM con un script de shell
  config.vm.provision "shell", inline: <<-SCRIPT
    set -e  # Detener el script si ocurre un error

    echo "=== Actualizando paquetes e instalando dependencias ==="
    sudo apt-get update
    sudo apt-get install -y openjdk-11-jdk tomcat9 tomcat9-admin maven git

    echo "=== Creando usuario y grupo para Tomcat ==="
    sudo groupadd -f tomcat9 || true
    sudo useradd -s /bin/false -g tomcat9 -d /etc/tomcat9 tomcat9 || true

    echo "=== Configurando Tomcat ==="
    sudo cp /vagrant/tomcat-users.xml /etc/tomcat9/tomcat-users.xml
    sudo cp /vagrant/context.xml /usr/share/tomcat9-admin/host-manager/META-INF/context.xml
    sudo systemctl restart tomcat9
    sudo systemctl enable tomcat9

    echo "=== Ajustando permisos para Tomcat ==="
    sudo chown -R tomcat9:tomcat9 /var/lib/tomcat9/webapps/
    sudo chmod +x /usr/share/tomcat9/bin/*.sh

    echo "=== Clonando y construyendo el proyecto Rock-Paper-Scissors ==="
    git clone https://github.com/cameronmcnz/rock-paper-scissors.git /home/vagrant/rock-paper-scissors || true
    cd /home/vagrant/rock-paper-scissors
    mvn clean install || true
    mvn tomcat7:deploy || true

    echo "=== Creando un nuevo proyecto Maven ==="
    cd /home/vagrant
    mvn archetype:generate -DgroupId=org.zaidinvergeles \
      -DartifactId=tomcat-war \
      -DarchetypeArtifactId=maven-archetype-webapp \
      -DinteractiveMode=false
    cd tomcat-war
    mvn clean package || true
    mvn tomcat7:deploy || true

    echo "=== Provisión completada ==="
  SCRIPT
end
```

Guarda este archivo en el directorio raíz de tu proyecto.

---

### Paso 2: Iniciar la máquina virtual
1. Abre una terminal y navega al directorio que contiene el `Vagrantfile`.
2. Ejecuta el comando:
   ```bash
   vagrant up
   ```
3. Este comando descargará la imagen de Debian, configurará el entorno e instalará todas las herramientas necesarias automáticamente.

4. Para acceder a la máquina virtual, ejecuta:
   ```bash
   vagrant ssh
   ```

---

## **3. Despliegue de aplicaciones**

### Desplegar el proyecto **Rock-Paper-Scissors**
1. Dentro de la máquina virtual, clona el repositorio del proyecto:
   ```bash
   git clone https://github.com/cameronmcnz/rock-paper-scissors.git /home/vagrant/rock-paper-scissors
   ```
2. Navega al directorio del proyecto:
   ```bash
   cd /home/vagrant/rock-paper-scissors
   ```
3. Construye el proyecto con Maven:
   ```bash
   mvn clean install
   ```
4. Despliega la aplicación en Tomcat:
   ```bash
   mvn tomcat7:deploy
   ```

### Generar y desplegar un nuevo proyecto con Maven
1. Crea un nuevo proyecto Maven:
   ```bash
   mvn archetype:generate -DgroupId=org.zaidinvergeles \
     -DartifactId=tomcat-war \
     -DarchetypeArtifactId=maven-archetype-webapp \
     -DinteractiveMode=false
   ```
2. Navega al directorio del proyecto generado:
   ```bash
   cd tomcat-war
   ```
3. Construye el proyecto:
   ```bash
   mvn clean package
   ```
4. Despliega la aplicación:
   ```bash
   mvn tomcat7:deploy
   ```

---

## **4. Verificar el despliegue**
1. Abre un navegador web y accede a las siguientes direcciones para verificar el despliegue:
   - **Rock-Paper-Scissors**: [http://localhost:8080/rock-paper-scissors](http://localhost:8080/rock-paper-scissors)
   - **tomcat-war**: [http://localhost:8080/tomcat-war](http://localhost:8080/tomcat-war)

2. Si todo se configuró correctamente, las aplicaciones deberían estar accesibles y funcionando.

---

## **5. Comandos útiles**
- **Reiniciar Tomcat:**
  ```bash
  sudo systemctl restart tomcat9
  ```
- **Volver a desplegar una aplicación:**
  ```bash
  mvn tomcat7:redeploy
  ```
- **Eliminar una aplicación desplegada:**
  ```bash
  mvn tomcat7:undeploy
  ```

---

### **6. Capturas de pantalla**
1. **Estado de Tomcat activo:**
   ![Tomcat](https://github.com/user-attachments/assets/tomcat-active.png)

2. **Rock-Paper-Scissors desplegado:**
   ![Rock-Paper-Scissors](https://github.com/user-attachments/assets/rock-paper-scissors.png)

3. **Proyecto Maven generado desplegado:**
   ![tomcat-war](https://github.com/user-attachments/assets/tomcat-war.png)

---
