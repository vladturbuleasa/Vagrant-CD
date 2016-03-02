
## CI_Environment using CentOS 6.7:

1. Install a JenkinsMaster on VM with maven,gradel and all plugins needed
2. Install a JenkinsSlave on VM with swarm plugin 
3. Install a toolsVM where is installed: Gitlab server , Nexus Sonatype server and Sonar Qube server

## Chef_Environment using Ubuntu 14.04 LTS/CentOS 6.7

1. Install Chef Server 12 open source with GUI and chefDK on a Ubuntu 14.04 LTS/CentOS
2. Setup 2 nodes for aplication - nodeApp1/nodeApp2

## Docker/Shadow
Not started.


## System Requrements 


This training requires the below hardware:

- CPU - 1.7 GHz ( Core i5 higher or equivalent ) 64-bit 
- Memory - minimum 8GB of RAM
- HDD - minimum 50 GB of disk space (prefered SSD)
- Network card


## Software Requrements


- OS: Windows/MAC/Linux (prefered Windows)
- Rights to perform admin/sudo commands
- VirtuaBox at least 5.0 or higher - https://www.virtualbox.org/wiki/Downloads
- Vagrant  1.8 or higher - http://www.vagrantup.com/downloads.html
- NetBeans IDE (or any other that has ruby support) or notepad++ https://notepad-plus-plus.org/download/v6.8.8.html
- Cygwin (if using windows) - https://cygwin.com/install.html
- JDK 8 ( please setup environment variable JAVE_HOME to point to correct location)
- git-bash client (if using windows) - https://git-scm.com/downloads
- mRemoteNG tool or putty (as you wish , we recommand mRemoteNG and provide configuration for it - )


## How to check software


- Vagrant installation could be check by starting the bash command window and type: 
	<pre><code>vagrant --version</code></pre>
	should return the version , if this is not the case please check if it is installed correctly.
- JDK 8 - check if this command is returning the path of JAVA_HOME:
	<pre><code>echo %JAVA_HOME%</code></pre>
- git-bash - open the window and type:
	<pre><code>git --version</code></pre>
	it should return the version installed of git client.
	


## How to proceed with starting the projects


Open the git-bash window and type the commands below:
<pre><code>
	cd C:/<your>/<workspace>/<path>  example: cd C:/Users/Admin/vagrant-project/ 
	git clone https://github.com/vladturbuleasa/POC.git
	cd POC
	ls -lt 
</code></pre>

you will see a structure of directories like below:
<pre><code>
drwxr-xr-x 1 vturbuleasa 1049089    0 Feb 16 17:19 Chef_Environment/
drwxr-xr-x 1 vturbuleasa 1049089    0 Feb 16 17:19 CI_Environment/
</code></pre>

We will start with the CI_Environment , use the below steps to start the CI environment:
<pre><code>
	cd CI_Environment
	vagrant up
</code></pre>

for stopping the environment just:
<pre><code>
	vagrant halt
</code></pre>
	
for removing the environment (just if it is requested)
<pre><code>
	vagrant destroy
</code></pre>
	
To start the Chef Environment , please cd into the Chef_Environment folder and use the below steps:
<pre><code>
	cd Chef_Environment
	vagrant up
</code></pre>

for stopping the environment just:

<pre><code>
	vagrant halt
</code></pre>
	
for removing the environment (just if it is requested)
<pre><code>
	vagrant destroy
</pre></code>
	
(!!!) **NOTE** : the CI_Environment would need up to 4 GB of your RAM memory and as well the Chef_Environment will need the same amount,
Please don't start both environments at the same time.
(!!!) NOTE: after installation of all things , you will need to setup : "ssl_verify_mode :verify_none" in the client.rb and knife.rb,
as well don't forget to copy the devops-validator.pem (organization) and vagrant.pem (user) to /etc/chef in chef server.
	

## How to get some of the cookbooks on the WorkStation 

**NOTE** 
- Keep in mind that the Chef WorkStation is setup on the Chef Server VM in order to reduce resources

**Steps**

- Please iave the entire folder .chef from /home/vagrant/chef-repo/.chef under /home/vagrant
- Remove the chef-repo folder 
- Under the folder /home/vagrant type this command:

	<pre><code>git clone https://github.com/vladturbuleasa/chef-repo.git </code></pre>

- after the recipes are in place , copy back the .chef folder using the below command:

        <pre><code>cp /home/vagrant/.chef /home/vagrant/chef-repo/.chef</code></pre>

**NOTE** - Please run all commands like knife cookbooks upload --all from /home/vagrant/chef-repo