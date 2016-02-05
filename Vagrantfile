# -*- mode: ruby -*-
# vi: set ft=ruby :

$script_updateCOS7 = <<SCRIPT
echo Updating the VM...
yum -y clean all
yum -y update
echo Done.
SCRIPT

$script_tools = <<SCRIPT
echo "Start installing tools..."
sudo echo "net.ipv6.conf.all.disable_ipv6 = 1" >> /etc/sysctl.conf
sudo sysctl -p
sudo yum -y install net-tools wget zip unzip mc vim git java-1.8.0-openjdk
echo "Done."
SCRIPT

$script_jenkinsMaster = <<SCRIPT

#Installing jenkins and java openjdk
wget -O /etc/yum.repos.d/jenkins.repo http://pkg.jenkins-ci.org/redhat-stable/jenkins.repo
rpm --import http://pkg.jenkins-ci.org/redhat-stable/jenkins-ci.org.key
yum -y install jenkins

#Installing plugins on jenkins
mkdir -p /var/lib/jenkins/plugins/
wget -O /var/lib/jenkins/plugins/scm-api.hpi https://updates.jenkins-ci.org/download/plugins/scm-api/latest/scm-api.hpi
wget -O /var/lib/jenkins/plugins/git-client.hpi https://updates.jenkins-ci.org/download/plugins/git-client/1.11.1/git-client.hpi
wget -O /var/lib/jenkins/plugins/git.hpi https://updates.jenkins-ci.org/download/plugins/git/2.3/git.hpi
wget -O /var/lib/jenkins/plugins/ws-cleanup.hpi https://updates.jenkins-ci.org/download/plugins/ws-cleanup/0.24/ws-cleanup.hpi
wget -O /var/lib/jenkins/plugins/token-macro.hpi https://updates.jenkins-ci.org/download/plugins/token-macro/1.10/token-macro.hpi
wget -O /var/lib/jenkins/plugins/config-file-provider.hpi http://mirrors.xmission.com/hudson/plugins/config-file-provider/2.7.5/config-file-provider.hpi
wget -O /var/lib/jenkins/plugins/swarm.hpi https://updates.jenkins-ci.org/latest/swarm.hpi
sudo chown -R jenkins:jenkins /var/lib/jenkins/plugins/

#setting up Jenkins as a service&start it
sudo /etc/init.d/jenkins restart
systemctl restart jenkins.service
SCRIPT

$script_jenkinsSlave = <<SCRIPT
mkdir -p /var/lib/jenkins/slave-agent
mkdir -p /var/log/jenkins/
cd /var/lib/jenkins/slave-agent/
wget -O /var/lib/jenkins/slave-agent/swarm-client-2.0-jar-with-dependencies.jar wget http://maven.jenkins-ci.org/content/repositories/releases/org/jenkins-ci/plugins/swarm-client/2.0/swarm-client-2.0-jar-with-dependencies.jar
nohup java -jar swarm-client-2.0-jar-with-dependencies.jar -master http://172.16.1.2:8080 > /var/log/jenkins/jenkins.log 2>&1 &
SCRIPT

#Provisioning the VM's
Vagrant.configure("2") do |config|
  #Version of OS
	config.vm.box = "centos/7"
	
	config.vm.define "jenkinsMaster" do |jenkinsMaster|
		jenkinsMaster.vm.hostname = "jenkinsMaster"
    jenkinsMaster.vm.provision :shell, :inline => $script_tools
    jenkinsMaster.vm.provision :shell, :inline => $script_jenkinsMaster
		jenkinsMaster.vm.synced_folder ".", "/vagrant", disabled: true
		jenkinsMaster.vm.network "private_network", ip: "172.16.1.2", virtualbox__intnet: true
		jenkinsMaster.vm.network "forwarded_port", guest: 22, host: 2022, id: "ssh", auto_correct: true
		jenkinsMaster.vm.network "forwarded_port", guest: 8080, host: 8080
		jenkinsMaster.vm.provider "virtualbox" do |vm|
			vm.customize [
        'modifyvm', :id,
        '--memory', '768'
							
      ]
		end
	end
	
	config.vm.define "jenkinsSlave" do |jenkinsSlave|
		jenkinsSlave.vm.hostname = "jenkinsSlave"
		jenkinsSlave.vm.provision :shell, :inline => $script_tools
    jenkinsSlave.vm.synced_folder ".", "/vagrant", disabled: true
		jenkinsSlave.vm.network "private_network", ip: "172.16.1.3", virtualbox__intnet: true
		jenkinsSlave.vm.network "forwarded_port", guest: 22, host: 3022, id: "ssh", auto_correct: true
		jenkinsSlave.vm.provider "virtualbox" do |vm|
			vm.customize [
							'modifyvm', :id,
							'--memory', '768'
							
						]
		end
	end
  
end

=begin
	config.vm.define "nexusServer" do |nexus|
		nexus.vm.hostname = "nexus"
		nexus.vm.synced_folder ".", "/vagrant", disabled: true
		nexus.vm.provision :shell, :inline => $install_tools
		nexus.vm.network "private_network", ip: "172.16.1.4", virtualbox__intnet: true
		nexus.vm.network "forwarded_port", guest: 22, host: 4022, id: "ssh", auto_correct: true
		nexus.vm.provider "virtualbox" do |vm|
			vm.customize [
							'modifyvm', :id,
							'--memory', '768'
							
						]
		end
	end	
	config.vm.define "gitServer" do |gitSrv|
		gitSrv.vm.hostname = "gitSrv"
		gitSrv.vm.synced_folder ".", "/vagrant", disabled: true
		gitSrv.vm.provision :shell, :inline => $install_tools
		gitSrv.vm.network "private_network", ip: "172.16.1.5", virtualbox__intnet: true
		gitSrv.vm.network "forwarded_port", guest: 22, host: 5022, id: "ssh", auto_correct: true
		gitSrv.vm.provider "virtualbox" do |vm|
			vm.customize [
							'modifyvm', :id,
							'--memory', '768'
							
						]
		end
	end	
	
	config.vm.define "chefServer" do |chef|
		chef.vm.hostname = "chef"
		chef.vm.synced_folder ".", "/vagrant", disabled: true
		chef.vm.provision :shell, :inline => $install_tools
		chef.vm.network "private_network", ip: "172.16.1.6", virtualbox__intnet: true
		chef.vm.network "forwarded_port", guest: 22, host: 6022, id: "ssh", auto_correct: true
		chef.vm.provider "virtualbox" do |vm|
			vm.customize [
							'modifyvm', :id,
							'--memory', '768'
							
						]
		end
	end
	
	config.vm.define "Docker" do |docker|
		docker.vm.hostname = "docker"
		docker.vm.synced_folder ".", "/vagrant", disabled: true
		docker.vm.provision :shell, :inline => $install_tools
		docker.vm.network "private_network", ip: "172.16.1.7", virtualbox__intnet: true
		docker.vm.network "forwarded_port", guest: 22, host: 7022, id: "ssh", auto_correct: true
		docker.vm.provider "virtualbox" do |vm|
			vm.customize [
							'modifyvm', :id,
							'--memory', '768'
							
						]
		end
	end
	
=end
