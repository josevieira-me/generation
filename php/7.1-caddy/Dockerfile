#####
# Generation
#
# Repository:    PHP
# Image:         PHP-FPM + Caddy
# Version:       7.1.x
# Strategy:      PHP From PHP-Alpine Repository (CODECASTS) + Official Caddy
#
FROM generation/php:7.1

# Repository/Image Maintainer
MAINTAINER Jose Vieira <hello@josevieira.me>

# Reset user to root to allow software install
USER root

# Copy Caddyfile and entry script
COPY Caddyfile /home/generation/Caddyfile
COPY start.sh  /home/generation/start.sh

# Installs Caddy
RUN curl https://getcaddy.com | bash && \
    chmod +x /home/generation/start.sh && \
    chown -R generation:generation /home/generation

# Define the running user
USER generation

# Application directory
WORKDIR "/var/www/app"

# Expose webserver port
EXPOSE 8080

# Starts a single shell script that puts php-fpm as a daemon and caddy on foreground
CMD ["/home/generation/start.sh"]
