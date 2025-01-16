# -*- mode: ruby -*-
# vi: set ft=ruby :

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure("2") do |config|
  # Define la caja base
  config.vm.box = "debian/bullseye64"

  # Configura el reenvío de puertos
  config.vm.network "forwarded_port", guest:8080, host:8080

  # Sincroniza una carpeta local con la máquina virtual
  config.vm.synced_folder "C:/Users/34637/Desktop/Tomcat y Maven", "/vagrant"

  # Provisión de la VM con un script de shell
  config.vm.provision "shell", inline: <<-SCRIPT

    # Actualizar paquetes e instalar servicios requeridos
    sudo apt-get update
    sudo apt install -y openjdk-11-jdk tomcat9-admin tomcat9 maven

    # Crear grupo y usuario para Tomcat
    sudo groupadd tomcat9
    sudo useradd -s /bin/false -g tomcat9 -d /etc/tomcat9 tomcat9

    # Copiar archivos de configuración y despliegue
    sudo cp /vagrant/tomcat-users.xml /etc/tomcat9/tomcat-users.xml
    sudo cp /vagrant/context.xml /etc/tomcat9/context.xml

    # Ajustar permisos
    sudo chown -R tomcat9:tomcat9 /var/lib/tomcat9/webapps/
    sudo chmod +x /usr/share/tomcat9/bin/*.sh

    # Reiniciar el servicio de Tomcat
    sudo systemctl restart tomcat9

    # Verificar el estado del servicio
    sudo systemctl status tomcat9

    # Configurar y ejecutar el proyecto Rock-Paper-Scissors
    # Clonar el repositorio del juego
    git clone https://github.com/cameronmcnz/rock-paper-scissors.git /home/vagrant/rock-paper-scissors

    # Navegar al directorio del proyecto
    cd /home/vagrant/rock-paper-scissors

    # Construir el proyecto con Maven
    mvn clean install

    # Desplegar el proyecto en Tomcat utilizando Maven
    mvn tomcat7:deploy

    # Crear un nuevo proyecto Maven utilizando el arquetipo webapp
    cd /home/vagrant
    mvn archetype:generate -DgroupId=org.zaidinvergeles \
      -DartifactId=tomcat-war \
      -DarchetypeArtifactId=maven-archetype-webapp \
      -DinteractiveMode=false

    # Navegar al nuevo proyecto y construirlo
    cd tomcat-war
    mvn clean package

    # Desplegar el proyecto tomcat-war utilizando Maven
    mvn tomcat7:deploy

    # Comandos adicionales que se pueden utilizar según la necesidad:
    # mvn tomcat7:redeploy  # Para volver a desplegar una aplicación
    # mvn tomcat7:undeploy  # Para retirar una aplicación desplegada
  SCRIPT
end