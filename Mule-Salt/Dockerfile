# Pull base image.
FROM ubuntu:14.04

MAINTAINER  Arun Kumar B "arunkumar.satyam@gmail.com"
ENV REFRESHED_AT 2015-04-03

#Install update , curl , unzip and software-properties-common
RUN apt-get -qq update
RUN apt-get -qq -y install curl
RUN apt-get install unzip
RUN apt-get -y install software-properties-common

# Install Java.
RUN \
  echo oracle-java8-installer shared/accepted-oracle-license-v1-1 select true | debconf-set-selections && \
  add-apt-repository -y ppa:webupd8team/java && \
  apt-get update && \
  apt-get install -y oracle-java8-installer && \
  rm -rf /var/lib/apt/lists/* && \
  rm -rf /var/cache/oracle-jdk8-installer


# Define working directory.
WORKDIR /data

# Define commonly used JAVA_HOME variable
ENV JAVA_HOME /usr/lib/jvm/java-8-oracle

# Define default command.
CMD ["bash"]

#Installing supervison-d
RUN apt-get update && apt-get install -y supervisor
RUN mkdir -p /var/log/supervisor
COPY supervisord.conf /etc/supervisor/conf.d/supervisord.conf


# Update system
RUN apt-get update && \
	apt-get install -y wget curl dnsutils python-pip python-dev python-apt software-properties-common dmidecode

# Setup salt ppa
RUN echo deb http://ppa.launchpad.net/saltstack/salt/ubuntu `lsb_release -sc` main | tee /etc/apt/sources.list.d/saltstack.list && \
	wget -q -O- "http://keyserver.ubuntu.com:11371/pks/lookup?op=get&search=0x4759FA960E27C0A6" | apt-key add -

# Install salt master/minion/cloud/api
#ENV SALT_VERSION 2014.7.4
RUN apt-get update && \
	apt-get install -y salt-master salt-minion  \
	salt-cloud salt-api

# Setup halite
RUN pip install cherrypy docker-py halite

# Add salt config files
ADD etc/master /etc/salt/master
ADD etc/minion /etc/salt/minion
ADD etc/reactor /etc/salt/master.d/reactor

# Expose volumes
VOLUME ["/etc/salt", "/var/cache/salt", "/var/logs/salt", "/srv/salt"]

# Exposing salt master and api ports
EXPOSE 4505 4506 8080 8081

# Add and set start script
ADD start.sh /start.sh
CMD ["bash", "start.sh"]

#Install mule community edition
ENV MULE_HOME /opt/mule
 
ENV MULE_VERSION 3.8.0

# Getting the Mule Executable and Installing
RUN echo 'Trying to connect to Mule for Gateway Edition'
 
RUN set -x \
        && cd /opt \
        && curl -o mule.zip https://repository.mulesoft.org/nexus/content/repositories/releases/org/mule/distributions/mule-standalone/$MULE_VERSION/mule-standalone-$MULE_VERSION.zip \
        && unzip mule.zip \
        && mv mule-standalone-$MULE_VERSION mule \
        && rm mule.zip*
 
WORKDIR $MULE_HOME
 
# Getting the Mule Executable and Installing
RUN echo 'Am I so lucky!!!'
 
VOLUME $MULE_HOME/apps
VOLUME $MULE_HOME/conf
VOLUME $MULE_HOME/domains
VOLUME $MULE_HOME/logs
 
EXPOSE 8077


#Running the supervisord
CMD ["/usr/bin/supervisord"]

