# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  config.vm.box = "debian/bullseye64"

  config.vm.define "tomcat_machine" do |tomcat_machine| 
    tomcat_machine.vm.network "forwarded_port", guest: 8080, host: 8080
    tomcat_machine.vm.network "private_network", ip: "192.168.56.10"
    tomcat_machine.vm.provision "shell", name: "tomcat_installation", inline: <<-SHELL
      sudo apt -y update
      sudo apt install -y openjdk-11-jdk
      sudo apt install -y tomcat9
      sudo groupadd tomcat9
      sudo useradd -s /bin/false -g tomcat9 -d /etc/tomcat9 tomcat9
      sudo systemctl start tomcat9
      sudo apt install -y tomcat9-admin
      sudo cp -vr /vagrant/tomcat-users.xml /etc/tomcat9/tomcat-users.xml
      sudo cp -vr /vagrant/context.xml /usr/share/tomcat9-admin/host-manager/META-INF/context.xml
      sudo systemctl restart tomcat9
    SHELL

    tomcat_machine.vm.provision "shell", name: "maven_configuration", inline: <<-SHELL
        sudo apt-get update && sudo apt-get -y install maven
        sudo cp -vr /vagrant/settings.xml /etc/maven/settings.xml
        mvn archetype:generate -DgroupId=org.zaidinvergeles \
                               -DartifactId=tomcat-war-deployment \
                               -DarchetypeArtifactId=maven-archetype-webapp \
                               -DinteractiveMode=fa
        sudo cp -vr /vagrant/pom.xml /home/vagrant/tomcat-war-deployment/pom.xml
        cd /home/vagrant/tomcat-war-deployment
        sudo mvn tomcat7:deploy
    SHELL

    tomcat_machine.vm.provision "shell", name: "page_deploy", inline: <<-SHELL
        sudo apt-get install -y git
        sudo git clone https://github.com/cameronmcnz/rock-paper-scissors.git
        cd rock-paper-scissors
        git checkout patch-1
        sudo cp -vr /vagrant/pageProof/pom.xml /home/vagrant/rock-paper-scissors/pom.xml        
    SHELL

  end
end
