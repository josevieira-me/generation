#####
# Generation
#
# Mailcatcher from schickling image
#####
FROM schickling/mailcatcher:latest

MAINTAINER Jose Vieira <hello@josevieira.me>

EXPOSE 1080 1025

ENTRYPOINT ["mailcatcher", "--smtp-ip=0.0.0.0", "--http-ip=0.0.0.0", "--foreground"]
