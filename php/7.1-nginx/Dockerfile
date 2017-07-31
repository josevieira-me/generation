###
# Generation
#
# Repository:    PHP
# Image:         PHP-FPM + Nginx
# Version:       7.1.x
# Strategy:      PHP From PHP-Alpine Repository (CODECASTS) + Official Nginx
#
FROM generation/php:7.1

# Repository/Image Maintainer
MAINTAINER Jose Vieira <hello@josevieira.me>

# Reset user to root to allow software install
USER root

# Copy nginx and entry script
COPY nginx.conf /etc/nginx/nginx.conf
COPY ssl.conf /etc/nginx/ssl.conf
COPY sites /etc/nginx/sites
COPY start.sh  /home/generation/start.sh

# Install nginx from dotdeb (already enabled on base image)
RUN echo "--> Installing Nginx" && \
    apk add --update nginx openssl && \
    rm -rf /tmp/* /var/tmp/* /usr/share/doc/* && \
    echo "--> Fixing permissions" && \
    mkdir /var/tmp/nginx && \
    mkdir /var/run/nginx && \
    mkdir /home/generation/ssl && \
    chown -R generation:generation /var/tmp/nginx && \
    chown -R generation:generation /var/run/nginx && \
    chown -R generation:generation /var/log/nginx && \
    chown -R generation:generation /var/lib/nginx && \
    chmod +x /home/generation/start.sh && \
    chown -R generation:generation /home/generation

# Define the running user
USER generation

# Pre generate some SSL
# YOU SHOULD REPLACE WITH YOUR OWN CERT.
RUN openssl req -x509 -nodes -days 3650 \
   -newkey rsa:2048 -keyout /home/generation/ssl/nginx.key \
   -out /home/generation/ssl/nginx.crt -subj "/C=AM/ST=generation/L=generation/O=generation/CN=*.dev" && \
   openssl dhparam -out /home/generation/ssl/dhparam.pem 2048

# Application directory
WORKDIR "/var/www/app"

# Expose webserver port
EXPOSE 8080

# Starts a single shell script that puts php-fpm as a daemon and nginx on foreground
CMD ["/home/generation/start.sh"]
