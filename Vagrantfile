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
		jenkinsMaster.vm.synced_folder ".", "/vagrant", disabled: true
		jenkinsMaster.vm.provision :shell, :inline => $install_tools
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
