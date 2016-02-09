# -*- mode: ruby -*-
# vi: set ft=ruby :

$script_tools = <<SCRIPT
echo "Start installing tools..."
#Allow all trafic trough ipotable
sudo echo "net.ipv6.conf.all.disable_ipv6 = 1" >> /etc/sysctl.conf
sudo sysctl -p
sudo yum -y install net-tools wget zip unzip mc vim git
echo "Install oracle jdk..."
#Install Oracle JDK
wget -nv \
--no-cookies \
--no-check-certificate \
--header "Cookie: oraclelicense=accept-securebackup-cookie" \
"http://download.oracle.com/otn-pub/java/jdk/8u74-b02/jdk-8u74-linux-x64.rpm" \
-O jdk-8u74-linux-x64.rpm
yum -y install jdk-8u74-linux-x64.rpm
\rm -rf jdk-8u74-linux-x64.rpm
echo "Done."
SCRIPT

$script_jenkinsMaster = <<SCRIPT
#Installing jenkins and java openjdk
wget -nv -O /etc/yum.repos.d/jenkins.repo http://pkg.jenkins-ci.org/redhat-stable/jenkins.repo
rpm --import http://pkg.jenkins-ci.org/redhat-stable/jenkins-ci.org.key
yum -y update
yum -y install jenkins
#Installing plugins on jenkins
mkdir -p /var/lib/jenkins/plugins/
wget -nv -O /var/lib/jenkins/plugins/scm-api.hpi https://updates.jenkins-ci.org/download/plugins/scm-api/latest/scm-api.hpi
wget -nv -O /var/lib/jenkins/plugins/git-client.hpi https://updates.jenkins-ci.org/download/plugins/git-client/1.11.1/git-client.hpi
wget -nv -O /var/lib/jenkins/plugins/git.hpi https://updates.jenkins-ci.org/download/plugins/git/2.3/git.hpi
wget -nv -O /var/lib/jenkins/plugins/ws-cleanup.hpi https://updates.jenkins-ci.org/download/plugins/ws-cleanup/0.24/ws-cleanup.hpi
wget -nv -O /var/lib/jenkins/plugins/token-macro.hpi https://updates.jenkins-ci.org/download/plugins/token-macro/1.10/token-macro.hpi
wget -nv -O /var/lib/jenkins/plugins/config-file-provider.hpi http://mirrors.xmission.com/hudson/plugins/config-file-provider/2.7.5/config-file-provider.hpi
wget -nv -O /var/lib/jenkins/plugins/swarm.hpi https://updates.jenkins-ci.org/latest/swarm.hpi
sudo chown -R jenkins:jenkins /var/lib/jenkins/plugins/
#setting up Jenkins as a service&start it
chkconfig jenkins on
service jenkins restart
iptables -P INPUT ACCEPT
iptables -P OUTPUT ACCEPT
SCRIPT

$script_jenkinsSlave = <<SCRIPT
echo "Installing JenkinsSlave..."
mkdir -p /var/lib/jenkins/
cd /var/lib/jenkins
touch /var/lib/jenkins/jenkins.log
wget -nv -O /var/lib/jenkins/swarm-client-2.0-jar-with-dependencies.jar http://maven.jenkins-ci.org/content/repositories/releases/org/jenkins-ci/plugins/swarm-client/2.0/swarm-client-2.0-jar-with-dependencies.jar
echo 'nohup java -jar /var/lib/jenkins/swarm-client-2.0-jar-with-dependencies.jar -master http://172.16.1.2:8080 > /var/lib/jenkins/jenkins.log 2>&1 &' >> /etc/rc.local
nohup java -jar /var/lib/jenkins/swarm-client-2.0-jar-with-dependencies.jar -master http://172.16.1.2:8080 > /var/lib/jenkins/jenkins.log 2>&1 &
iptables -P INPUT ACCEPT
iptables -P OUTPUT ACCEPT
echo "Installing JenkinsSlave Done..."
SCRIPT

$script_nexusInstall = <<SCRIPT
echo "Installing Nexus..."
rm -rf /var/lib/nexus /home/nexus
userdel nexus
wget -nv -O /var/lib/nexus-2.12.0-01-bundle.tar.gz http://download.sonatype.com/nexus/oss/nexus-2.12.0-01-bundle.tar.gz
cd /var/lib/;tar xvzf nexus-2.12.0-01-bundle.tar.gz;rm -rf nexus-2.12.0-01-bundle.tar.gz
mv nexus-2.12.0-01 nexus
cd nexus;chmod -R a+x bin
sudo adduser nexus
chown -R nexus:nexus /var/lib/nexus
chown -R nexus:nexus /var/lib/sonatype-work
ln -s /var/lib/nexus/bin/nexus /etc/init.d/nexus
sed -i 's/#RUN_AS_USER=/RUN_AS_USER=nexus/g' /var/lib/nexus/bin/nexus
chkconfig --add nexus
chkconfig --levels 345 nexus on
service nexus start
iptables -I INPUT -p tcp --dport 8081 -j ACCEPT
SCRIPT

$script_gitInstall = <<SCRIPT
echo "Installing Git..."
yum -y install curl openssh-server openssh-clients postfix cronie
service postfix start
chkconfig postfix on
lokkit -s http -s ssh
curl -sS https://packages.gitlab.com/install/repositories/gitlab/gitlab-ce/script.rpm.sh | sudo bash
yum -y install gitlab-ce
gitlab-ctl reconfigure
#Credentials: root/5iveL!fe
echo "Installing Git DONE..."
iptables -I INPUT -p tcp --dport 80 -j ACCEPT
SCRIPT

#Provisioning the VM's
Vagrant.configure("2") do |config|
  #Version of OS
	config.vm.box = "nrel/CentOS-6.7-x86_64"
	
	config.vm.define "jenkinsMaster" do |jenkinsMaster|
		jenkinsMaster.vm.hostname = "jenkinsMaster"
    jenkinsMaster.vm.provision :shell, :inline => $script_tools
    jenkinsMaster.vm.provision :shell, :inline => $script_jenkinsMaster
		jenkinsMaster.vm.synced_folder ".", "/vagrant", disabled: true
		jenkinsMaster.vm.network "private_network", ip: "172.16.1.2", virtualbox__intnet: true
		jenkinsMaster.vm.network "forwarded_port", guest: 22, host: 2022, id: "ssh", auto_correct: true
		jenkinsMaster.vm.network "forwarded_port", guest: 8080, host: 8080
		jenkinsMaster.vm.provider "virtualbox" do |vm|
      vm.name = "JenkinsMaster"
			vm.customize [
        'modifyvm', :id,
        '--memory', '768'
							
      ]
		end
	end
	
	config.vm.define "jenkinsSlave" do |jenkinsSlave|
		jenkinsSlave.vm.hostname = "jenkinsSlave"
		jenkinsSlave.vm.synced_folder ".", "/vagrant", type: "virtualbox", disabled: true
		jenkinsSlave.vm.provision :shell, :inline => $script_tools
		jenkinsSlave.vm.provision :shell, :inline => $script_jenkinsSlave
		jenkinsSlave.vm.network "private_network", ip: "172.16.1.3", virtualbox__intnet: true
		jenkinsSlave.vm.network "forwarded_port", guest: 22, host: 3022, id: "ssh", auto_correct: true
		jenkinsSlave.vm.provider "virtualbox" do |vm|
      vm.name = "JenkinsSlave"
			vm.customize [
							'modifyvm', :id,
							'--memory', '512'
							
						]
		end
	end

	config.vm.define "toolsVM" do |toolsVM|
		toolsVM.vm.hostname = "nexus"
    toolsVM.vm.synced_folder ".", "/vagrant", type: "virtualbox", disabled: true
		toolsVM.vm.provision :shell, :inline => $script_tools
    toolsVM.vm.provision :shell, :inline => $script_nexusInstall
    toolsVM.vm.provision :shell, :inline => $script_gitInstall
		toolsVM.vm.network "private_network", ip: "172.16.1.4", virtualbox__intnet: true
		toolsVM.vm.network "forwarded_port", guest: 22, host: 4022, id: "ssh", auto_correct: true
    toolsVM.vm.network "forwarded_port", guest: 8081, host: 8081, id: "Nexus"
    toolsVM.vm.network "forwarded_port", guest: 80, host: 8181, id: "gitlabweb"
		toolsVM.vm.provider "virtualbox" do |vm|
      vm.name = "toolsVM"
			vm.customize [
							'modifyvm', :id,
							'--memory', '1536'
							
						]
		end
	end
  
  config.vm.define "aplication1" do |aplication1|
		aplication1.vm.hostname = "aplication1"
		aplication1.vm.synced_folder ".", "/vagrant", type: "virtualbox", disabled: true
		aplication1.vm.network "private_network", ip: "172.16.1.5", virtualbox__intnet: true
		aplication1.vm.network "forwarded_port", guest: 22, host: 5022, id: "ssh", auto_correct: true
		aplication1.vm.provider "virtualbox" do |vm|
      vm.name = "App1"
			vm.customize [
							'modifyvm', :id,
							'--memory', '512'
							
						]
		end
	end
  
end
