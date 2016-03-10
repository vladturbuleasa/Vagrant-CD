FROM centos

MAINTAINER Vlad Turbuleasa <vlad.turbuleasa@gmail.com>

RUN rm -f /var/lib/rpm/__*
RUN rpm --rebuilddb -v -v

RUN rm -f /var/cache/yum/timedhosts.txt

RUN yum clean all


RUN yum -y install \
        wget \
        vim \
        initscripts \
        epel-release \
        java-1.8.0-openjdk.x86_64

RUN wget -O /etc/yum.repos.d/jenkins.repo http://pkg.jenkins-ci.org/redhat/jenkins.repo
RUN rpm --import http://pkg.jenkins-ci.org/redhat/jenkins-ci.org.key
RUN yum -y install jenkins

EXPOSE 8080

CMD /etc/alternatives/java -Dcom.sun.akuma.Daemon=daemonized -Djava.awt.headless=true -DJENKINS_HOME=/var/lib/jenkins -jar /usr/lib/jenkins/jenkins.war --logfile=/var/log/jenkins/jenkins.log --webroot=/var/cache/jenkins/war --httpPort=8080 --ajp13Port=8009 --debug=5 --handlerCountMax=100 --handlerCountMaxIdle=20
