#####
# Generation
#
# Uses the default/official MySQl 5.7 image with some little tricks
# on the configuration to work with Laravel/Lumen
#####
FROM mysql:5.7

# Repository/Image Maintainer
MAINTAINER Jose Vieira <hello@josevieira.me>

# Laravel requirement for preventing migration timestamp incompatibility
COPY mode.cnf /etc/mysql/mysql.conf.d/mode.cnf
