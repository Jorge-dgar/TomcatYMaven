# README: Cómo configurar y desplegar aplicaciones con Vagrant, Tomcat y Maven

Este archivo explica paso a paso cómo configurar un entorno de desarrollo utilizando **Vagrant**, **Tomcat**, y **Maven**. Es un tutorial detallado que incluye desde la instalación de programas necesarios hasta el despliegue de aplicaciones en un servidor web.

---

## **1. Programas necesarios**
Antes de comenzar, asegúrate de instalar los siguientes programas en tu computadora:

1. **Vagrant**: Para gestionar máquinas virtuales de forma sencilla.
   - Puedes descargarlo desde [vagrantup.com](https://www.vagrantup.com/).

2. **VirtualBox**: Proveedor de máquinas virtuales.
   - Descárgalo desde [virtualbox.org](https://www.virtualbox.org/).

3. **Git**: Para clonar repositorios.
   - Descárgalo desde [git-scm.com](https://git-scm.com/).

Verifica que están instalados correctamente ejecutando los siguientes comandos en la terminal:
```bash
vagrant --version
virtualbox --help
git --version
```
---

## **2. Configuración del proyecto**

### Paso 1: Preparar el archivo `Vagrantfile`
El archivo `Vagrantfile` configura una máquina virtual con Debian y realiza la instalación automática de Java, Maven, y Tomcat. Asegúrate de tener el siguiente contenido en tu `Vagrantfile`:

```ruby
Vagrant.configure("2") do |config|
  config.vm.box = "debian/bullseye64"
  config.vm.network "forwarded_port", guest:8080, host:8080
  config.vm.synced_folder "./", "/vagrant"

  config.vm.provision "shell", inline: <<-SHELL
    sudo apt-get update
    sudo apt install -y openjdk-11-jdk tomcat9-admin tomcat9 maven

    sudo groupadd tomcat9
    sudo useradd -s /bin/false -g tomcat9 -d /etc/tomcat9 tomcat9

    sudo cp /vagrant/tomcat-users.xml /etc/tomcat9/tomcat-users.xml
    sudo cp /vagrant/context.xml /etc/tomcat9/context.xml

    sudo systemctl restart tomcat9
  SHELL
end
```

Guarda este archivo en el directorio raíz de tu proyecto.

---

### Paso 2: Iniciar la máquina virtual
1. Abre una terminal y navega al directorio donde se encuentra el `Vagrantfile`.
2. Ejecuta el comando:
   ```bash
   vagrant up
   ```
3. Este comando descargará la imagen de Debian, configurará el entorno e instalará todas las herramientas necesarias.

---

## **3. Despliegue de aplicaciones**

### Paso 1: Clonar y desplegar el proyecto "Rock-Paper-Scissors"
1. Accede a la máquina virtual:
   ```bash
   vagrant ssh
   ```
2. Clona el repositorio del proyecto:
   ```bash
   git clone https://github.com/cameronmcnz/rock-paper-scissors.git /home/vagrant/rock-paper-scissors
   ```
3. Navega al directorio del proyecto:
   ```bash
   cd /home/vagrant/rock-paper-scissors
   ```
4. Construye el proyecto con Maven:
   ```bash
   mvn clean install
   ```
5. Despliega la aplicación en Tomcat:
   ```bash
   mvn tomcat7:deploy
   ```

### Paso 2: Generar y desplegar un proyecto con Maven
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
1. Abre un navegador y accede a:
   - **Rock-Paper-Scissors**: `http://localhost:8080/rock-paper-scissors`
   - **tomcat-war**: `http://localhost:8080/tomcat-war`

2. Las aplicaciones deberían estar funcionando correctamente.

---

## **5. Comandos útiles**
- **Volver a desplegar una aplicación**:
  ```bash
  mvn tomcat7:redeploy
  ```
- **Eliminar una aplicación desplegada**:
  ```bash
  mvn tomcat7:undeploy
  ```

---

## **6. Capturas de pantalla**
### Rock-Paper-Scissors desplegado:
![codigo añadido al pow piedra papel tijera](https://github.com/user-attachments/assets/eaae38ca-e1a4-4bea-9d0f-f082fd4f7c48)


### tomcat-war desplegado:
![comprobacion-funciona](https://github.com/user-attachments/assets/9e8ebe8f-a856-4dcd-8558-d0b8efd1d762)


---
