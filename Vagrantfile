# -*- mode: ruby -*-
# vi: set ft=ruby :

$updateCOS7 = <<SCRIPT
echo Updating the VM...
yum -y clean all
yum -y update
echo Done.
SCRIPT

$install_tools = <<SCRIPT
echo "Start installing tools..."
sudo echo "net.ipv6.conf.all.disable_ipv6 = 1" >> /etc/sysctl.conf
sudo sysctl -p
sudo yum -y install net-tools wget zip unzip mc
echo "Done."
SCRIPT

$install_nginx = <<SCRIPT
wget http://nginx.org/packages/rhel/7/x86_64/RPMS/nginx-1.8.1-1.el7.ngx.x86_64.rpm
chown vagrant:vagrant nginx-1.8.1-1.el7.ngx.x86_64.rpm
chmod 755 nginx-1.8.1-1.el7.ngx.x86_64.rpm
yum -y install nginx-1.8.1-1.el7.ngx.x86_64.rpm
systemctl start nginx
systemctl enable nginx
sudo rm -rf nginx-1.8.1-1.el7.ngx.x86_64.rpm
SCRIPT

$install_jenkinsMaster = <<SCRIPT

#Installing jenkins and java openjdk
wget -O /etc/yum.repos.d/jenkins.repo http://pkg.jenkins-ci.org/redhat-stable/jenkins.repo
rpm --import http://pkg.jenkins-ci.org/redhat-stable/jenkins-ci.org.key
yum -y install jenkins java-1.8.0-openjdk
yum -y install jenkins

#Installing plugins on jenkins
#setting up Jenkins as a service&start it
sudo /etc/init.d/jenkins restart
systemctl restart jenkins.service
SCRIPT


#Provisioning the VM's
Vagrant.configure("2") do |config|
    #Version of OS
	config.vm.box = "centos/7"
	
	config.vm.define "jenkinsMaster" do |jenkinsMaster|
		jenkinsMaster.vm.hostname = "jenkinsMaster"
    jenkinsMaster.vm.provision :shell, :inline => $install_tools
    jenkinsMaster.vm.provision :shell, :inline => $install_jenkinsMaster
    jenkinsMaster.vm.provision :shell, :inline => $install_nginx
		jenkinsMaster.vm.synced_folder ".", "/vagrant", disabled: true
		jenkinsMaster.vm.network "private_network", ip: "172.16.1.2", virtualbox__intnet: true
		jenkinsMaster.vm.network "forwarded_port", guest: 22, host: 2022, id: "ssh", auto_correct: true
		jenkinsMaster.vm.network "forwarded_port", guest: 80, host: 8181
		jenkinsMaster.vm.network "forwarded_port", guest: 8080, host: 8080
		jenkinsMaster.vm.provider "virtualbox" do |vm|
			vm.customize [
							'modifyvm', :id,
							'--memory', '768'
							
						]
		end
	end
end
=begin	
	config.vm.define "jenkinsSlave" do |jenkinsSlave|
		jenkinsSlave.vm.hostname = "jenkinsSlave"
		jenkinsSlave.vm.synced_folder ".", "/vagrant", disabled: true
		jenkinsSlave.vm.provision :shell, :inline => $install_tools
		jenkinsSlave.vm.network "private_network", ip: "172.16.1.3", virtualbox__intnet: true
		jenkinsSlave.vm.network "forwarded_port", guest: 22, host: 3022, id: "ssh", auto_correct: true
		jenkinsSlave.vm.provider "virtualbox" do |vm|
			vm.customize [
							'modifyvm', :id,
							'--memory', '768'
							
						]
		end
	end

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
