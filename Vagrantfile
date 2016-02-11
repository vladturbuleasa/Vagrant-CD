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
echo "Install Jenkins Master on machine..."
#Installing jenkins
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
service iptables stop
chkconfig iptables off
echo "All Done, jenkins master is installed."
SCRIPT

$script_jenkinsSlave = <<SCRIPT
echo "Installing JenkinsSlave..."
mkdir -p /var/lib/jenkins/
cd /var/lib/jenkins
touch /var/lib/jenkins/jenkins.log
wget -nv -O /var/lib/jenkins/swarm-client-2.0-jar-with-dependencies.jar http://maven.jenkins-ci.org/content/repositories/releases/org/jenkins-ci/plugins/swarm-client/2.0/swarm-client-2.0-jar-with-dependencies.jar
echo 'nohup java -jar /var/lib/jenkins/swarm-client-2.0-jar-with-dependencies.jar -master http://172.16.1.2:8080 > /var/lib/jenkins/jenkins.log 2>&1 &' >> /etc/rc.local
nohup java -jar /var/lib/jenkins/swarm-client-2.0-jar-with-dependencies.jar -master http://172.16.1.2:8080 > /var/lib/jenkins/jenkins.log 2>&1 &
service iptables stop
chkconfig iptables off
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
iptables -P INPUT ACCEPT
iptables -I INPUT -p tcp --dport 8081 -j ACCEPT
echo "Installation of Nexus Sonatype is done..."
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
iptables -P INPUT ACCEPT
iptables -I INPUT -p tcp --dport 80 -j ACCEPT
SCRIPT

$script_gradleInstall = <<SCRIPT
echo "Install Gradle to machine..."
gradle_version=2.11
wget -nv -O /opt/gradle-${gradle_version}-all.zip https://services.gradle.org/distributions/gradle-${gradle_version}-all.zip
unzip /opt/gradle-${gradle_version}-all.zip -d /opt/gradle
ln -s /opt/gradle/gradle-${gradle_version} /opt/gradle/latest
touch /etc/profile.d/gradle.sh
echo 'export GRADLE_HOME=/opt/gradle/latest' >> /etc/profile.d/gradle.sh
echo 'export PATH=$PATH:/opt/gradle/latest/bin' >> /etc/profile.d/gradle.sh
chmod +x /etc/profile.d/gradle.sh
. /etc/profile.d/gradle.sh
# check installation
gradle -v
rm -rf /opt/*.zip
echo "Gradle install finish..."
SCRIPT

$script_mvnInstall = <<SCRIPT
echo "Install Maven 3 to machine..."
cd /opt/
mkdir -p /opt/maven/
wget -nv -O /opt/apache-maven-3.3.3-bin.tar.gz http://apache-mirror.rbc.ru/pub/apache/maven/maven-3/3.3.3/binaries/apache-maven-3.3.3-bin.tar.gz
tar -xf apache-maven-3.3.3-bin.tar.gz -C /opt/maven/
ln -s /opt/maven/apache-maven-3.3.3 /opt/maven/latest
touch /etc/profile.d/maven.sh
echo 'export MAVEN_HOME=/opt/gradle/latest' >> /etc/profile.d/maven.sh
echo 'export PATH=$PATH:/opt/maven/latest/bin' >> /etc/profile.d/maven.sh
chmod +x /etc/profile.d/maven.sh
. /etc/profile.d/maven.sh
# check installation
mvn -v
echo "Install done..."
SCRIPT

$script_installChef12 = <<SCRIPT
wget -nv -O /opt/chef-server-core_12.3.0-1_amd64.deb https://web-dl.packagecloud.io/chef/stable/packages/ubuntu/trusty/chef-server-core_12.3.0-1_amd64.deb
cd /opt/
dpkg -i chef-server*
chef-server-ctl reconfigure
mkdir -p /opt/chef-server
sudo chef-server-ctl user-create --filename /opt/chef-server/vagrant.pem vagrant Vlad ENDAVA vlad.turbuleasa@endava.com vagrant
sudo chef-server-ctl org-create ssh DevOps --association_user vagrant --filename /opt/chef-server/ssh.pem
SCRIPT

$script_installSonar = <<SCRIPT
echo "Installing SonarQube server..."
wget -O /etc/yum.repos.d/sonar.repo http://downloads.sourceforge.net/project/sonar-pkg/rpm/sonar.repo
yum -y install sonar
service sonar start
iptables -I INPUT -p tcp --dport 9000 -j ACCEPT
echo "Installation of SonarQube is done..."
SCRIPT

#Provisioning the VM's
Vagrant.configure("2") do |config|
  #Version of OS
	#config.vm.box = "nrel/CentOS-6.7-x86_64"
	
	config.vm.define "jenkinsMaster" do |jenkinsMaster|
    jenkinsMaster.vm.box = "nrel/CentOS-6.7-x86_64"
		jenkinsMaster.vm.hostname = "jenkinsMaster"
    jenkinsMaster.vm.provision :shell, :inline => $script_tools
    jenkinsMaster.vm.provision :shell, :inline => $script_jenkinsMaster
    jenkinsMaster.vm.provision :shell, :inline => $script_gradleInstall
    jenkinsMaster.vm.provision :shell, :inline => $script_mvnInstall
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
    jenkinsSlave.vm.box = "nrel/CentOS-6.7-x86_64"
		jenkinsSlave.vm.hostname = "jenkinsSlave"
		jenkinsSlave.vm.synced_folder ".", "/vagrant", type: "virtualbox", disabled: true
		jenkinsSlave.vm.provision :shell, :inline => $script_tools
		jenkinsSlave.vm.provision :shell, :inline => $script_jenkinsSlave
    jenkinsSlave.vm.provision :shell, :inline => $script_gradleInstall
    jenkinsSlave.vm.provision :shell, :inline => $script_mvnInstall
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
    toolsVM.vm.box = "nrel/CentOS-6.7-x86_64"
		toolsVM.vm.hostname = "toolsVM"
    toolsVM.vm.synced_folder ".", "/vagrant", type: "virtualbox", disabled: true
		toolsVM.vm.provision :shell, :inline => $script_tools
    toolsVM.vm.provision :shell, :inline => $script_nexusInstall
    toolsVM.vm.provision :shell, :inline => $script_gitInstall
    toolsVM.vm.provision :shell, :inline => $script_installSonar
		toolsVM.vm.network "private_network", ip: "172.16.1.4", virtualbox__intnet: true
		toolsVM.vm.network "forwarded_port", guest: 22, host: 4022, id: "ssh", auto_correct: true
    toolsVM.vm.network "forwarded_port", guest: 8081, host: 8081, id: "Nexus"
    toolsVM.vm.network "forwarded_port", guest: 80, host: 8181, id: "gitlabweb"
		toolsVM.vm.provider "virtualbox" do |vm|
      vm.name = "toolsVM"
			vm.customize [
							'modifyvm', :id,
							'--memory', '1280'
							
						]
		end
	end
  
  
  config.vm.define "chefServer" do |chefServer|
     chefServer.vm.box = "ubuntu/trusty64"
     chefServer.vm.hostname = "chef-server"
     chefServer.vm.synced_folder ".", "/vagrant", type: "virtualbox", disabled: true
     chefServer.vm.provision :shell, :inline => $script_installChef12
     chefServer.vm.network "private_network", ip: "172.16.1.5", virtualbox__intnet: true
     chefServer.vm.network "forwarded_port", guest: 22, host: 5022, id: "ssh", auto_correct: true
     chefServer.vm.provider "virtualbox" do |vm|
      vm.name = "chef-server"
			vm.customize [
							'modifyvm', :id,
							'--memory', '512'
							
						]
		end
  end
  
end
