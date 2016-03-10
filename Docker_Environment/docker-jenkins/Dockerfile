FROM centos

MAINTAINER Vlad Turbuleasa <vturbuleasa@endava.com>

RUN yum -y update \
	&& yum -y upgrade

RUN yum -y install java-1.6.0-openjdk.x86_64
RUN yum -y install wget 
RUN wget -O /etc/yum.repos.d/jenkins.repo http://pkg.jenkins-ci.org/redhat-stable/jenkins.repo
RUN rpm --import https://jenkins-ci.org/redhat/jenkins-ci.org.key
RUN yum clean all && yum -y update
RUN yum -y install jenkins

VOLUME /var/lib/jenkins
ENV JENKINS_HOME /var/lib/jenkins

EXPOSE 8080

ADD run /usr/local/bin/run
RUN chmod +x /usr/local/bin/run

CMD /usr/loca/bin/run
