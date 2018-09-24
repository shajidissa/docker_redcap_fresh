############################################################
# Dockerfile to build Redcap
# Based on Ubuntu
############################################################

# Set the base image to Ubuntu
FROM ubuntu:latest

ARG redcap_version
ENV redcap_version $redcap_version

RUN echo $redcap_version

#    Potential Tags : See https://github.com/docker-library/docs/blob/master/ubuntu/tag-details.md
#    ubuntu:12.04.5
#    ubuntu:12.04
#    ubuntu:precise-20160707
#    ubuntu:precise
#    ubuntu:14.04.4
#    ubuntu:14.04
#    ubuntu:trusty-20160711
#    ubuntu:trusty
#    ubuntu:15.10
#    ubuntu:wily-20160706
#    ubuntu:wily
#    ubuntu:16.04
#    ubuntu:xenial-20160713
#    ubuntu:xenial
#    ubuntu:latest
#    ubuntu:16.10
#    ubuntu:yakkety-20160717
#    ubuntu:yakkety
#    ubuntu:devel

MAINTAINER Saj Issa <saj.issa@gmail.com>

################## BEGIN INSTALLATION ######################


RUN apt-get update && \
    apt-get -y upgrade && \
    DEBIAN_FRONTEND=noninteractive apt-get -y install apache2 php7.0  php7.2-mysql  php7.2-curl  php7.2-json libapache2-mod-php7.2  php7.2-zip php7.2-gd php-pear curl phpmyadmin libpcre3-dev wget git vim unzip mysql-server sendmail-bin sendmail ntp cron supervisor && \
    apt-get clean && \
    update-rc.d apache2 defaults 

RUN pear install DB    

# php5    php5-mysql    php5-curl    php5-mcrypt    php5-json    libapache2-mod-php5    php-pear php-auth curl phpmyadmin
# php7.0  php7.0-mysql  php7.0-curl  php7.0-mcrypt  php7.0-json  libapache2-mod-php7.0  php-pear php-auth curl phpmyadmin

##################### INSTALLATION END #####################