FROM ubuntu:18.04
## install apache
RUN apt update
RUN apt install apache2 -y
## create a web page
RUN echo "Docker training" > /var/www/html/index.html

## create a script to start the apache in foreground
RUN echo ". /etc/apache2/envvars" > /run.sh
RUN echo "mkdir -p /var/run/apache2" >> /run.sh
RUN echo "mkdir -p /var/lock/apache2" >> /run.sh
RUN echo "/usr/sbin/apache2 -D FOREGROUND" >> /run.sh

## provide the execute permission for the script
RUN chmod +x /run.sh

## define the port number
EXPOSE 80

## define running process for container
CMD /run.sh
