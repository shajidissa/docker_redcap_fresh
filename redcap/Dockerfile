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

# Manually set up the apache environment variables
RUN mkdir -p /var/lock/apache2 /var/run/apache2 /etc/supervisor/conf.d/
ENV APACHE_RUN_USER www-data
ENV APACHE_RUN_GROUP www-data
ENV APACHE_LOG_DIR /var/log/apache2
ENV APACHE_LOCK_DIR /var/lock/apache2
ENV APACHE_PID_FILE /var/run/apache2.pid

# Expose apache.
EXPOSE 80

# Make a directory
RUN mkdir -p /var/www/site
RUN mkdir -p /share/edocs
RUN chown www-data:www-data /share/edocs

# Copy this repo into place.
ADD index.php /var/www/site

# Copy this repo into place.
ADD info.php /var/www/site

# Copy this repo into place.
ADD index.php /var/www/html

# Copy latest version and unzip
ADD redcap$redcap_version.zip /home
RUN unzip /home/redcap$redcap_version.zip -d /home

# Move latest version
RUN mv /home/redcap/ /var/www/site/
ADD database.php /var/www/site/redcap

# Add Redcap cronjob
RUN echo "* * * * * /usr/bin/php /var/www/site/redcap/cron.php >> /var/log/cron.log 2>&1" | crontab -

# supervisord config file
COPY ./supervisord.conf /etc/supervisor/conf.d/supervisord.conf

# Add test images
ADD redcaplogo_test.gif /var/www/site/redcap/redcap_v$redcap_version/Resources/images/redcaplogo.gif
ADD redcaplogo_small_test.gif /var/www/site/redcap/redcap_v$redcap_version/Resources/images/redcaplogo_small.gif

# Add optimised for chrome image
ADD chrome.png /var/www/site/redcap/
RUN sed -i.bkp '/print "<\/form>";/a print "<img src='\''/redcap/chrome.png'\''>";' /var/www/site/redcap/redcap_v$redcap_version/Config/init_functions.php

# house keeping
RUN chmod 777 -R /var/www/site/redcap/temp
RUN mkdir /usr/local/redcap-edocs
RUN chmod 777 -R /usr/local/redcap-edocs

# php changes
RUN sed -i.bak 's/upload_max_filesize = 2M/upload_max_filesize = 32M/g' /etc/php/7.2/apache2/php.ini
RUN sed -i.bak 's/post_max_size = 8M/post_max_size = 32M/g' /etc/php/7.2/apache2/php.ini
RUN sed -i.bak 's/; max_input_vars = 1000/max_input_vars = 10000/g' /etc/php/7.2/apache2/php.ini
RUN sed -i.bak 's/;SMTP = localhost/SMTP=127.0.0.1/g' /etc/php/7.2/apache2/php.ini
RUN sed -i.bak 's/;smtp_port = 25/smtp_port=25/g' /etc/php/7.2/apache2/php.ini
RUN sed -i.bak ';sendmail_from = me@example.com/sendmail_path=/usr/sbin/sendmail -t -i/g' /etc/php/7.2/apache2/php.ini

# Update the default apache site with the config we created.
ADD apache-config.conf /etc/apache2/sites-enabled/000-default.conf

# Add crontab file in the cron directory
ADD crontab /etc/cron.d/hello-cron

# Give execution rights on the cron job
RUN chmod 0644 /etc/cron.d/hello-cron

# Apply cron job
RUN crontab /etc/cron.d/hello-cron

# Create the log file to be able to run tail
RUN touch /var/log/cron.log

# Run the command on container startup
CMD cron && tail -f /var/log/cron.log

# Setup supervisord
CMD /usr/bin/supervisord -c /etc/supervisor/conf.d/supervisord.conf
