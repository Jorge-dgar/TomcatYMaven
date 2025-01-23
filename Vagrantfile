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

    echo "=== Comprobando estado de Tomcat ==="
    sudo systemctl status tomcat9 || true

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