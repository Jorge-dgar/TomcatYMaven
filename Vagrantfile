# -*- mode: ruby -*-
# vi: set ft=ruby :

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure("2") do |config|
  config.vm.box = "debian/bullseye64"
  config.vm.network "forwarded_port", guest:8080, host:8080
  config.vm.synced_folder "C:/Users/34637/Desktop/Tomcat y Maven/maven", "/maven"

  config.vm.provision "shell", inline: <<-SCRIPT



  # sudo apt install -y openjdk-11-jdk
  # sudo apt install -y tomcat9
  # sudo groupadd tomcat9

  # sudo useradd -s /bin/false -g tomcat9 -d /etc/tomcat9 tomcat9
  # sudo systemctl start tomcat9
  # sudo systemctl status tomcat9




  # USUARIOS Y PERMISOS
  # sudo apt install -y tomcat9-admin


  #DEBIAN
  sudo apt-get update && sudo apt-get -y install maven




SCRIPT
end
