FROM debian:jessie
MAINTAINER LethaK
LABEL php.version=7.2

# Base packages
# -----------------------------------------------------------------------------
RUN apt-get update && \
    apt-get install -y apt-transport-https ca-certificates wget

# Adding packages.sury.org repository for PHP 7.2
RUN wget -O /etc/apt/trusted.gpg.d/php.gpg https://packages.sury.org/php/apt.gpg && \
    echo "deb https://packages.sury.org/php/ jessie main" > /etc/apt/sources.list.d/php.list


# PHP packages
# -----------------------------------------------------------------------------
RUN apt-get update && \
    apt-get install -y \
    curl \
    php7.2 \
    php7.2-readline \
    php7.2-bcmath \
    php7.2-mysql \
    php7.2-json \
    php7.2-xsl \
    php7.2-intl \
    php7.2-curl \
    php7.2-imagick \
    php7.2-gd \
    php7.2-xmlrpc \
    php7.2-memcached \
    php7.2-redis \
    php7.2-xdebug \
    php7.2-mbstring;
#    php7.2-mcrypt \

# PHP config
# -----------------------------------------------------------------------------
# Custom PHP variables
RUN curl -L -o /etc/php/7.2/cli/conf.d/custom_vars.ini https://raw.githubusercontent.com/lethak-docker/webplatform/master/build_files/php/custom_vars.ini && \
    cp /etc/php/7.2/cli/conf.d/custom_vars.ini /etc/php/7.2/apache2/conf.d/custom_vars.ini

# remove default xdebug config
RUN rm /etc/php/7.2/cli/conf.d/20-xdebug.ini
RUN rm /etc/php/7.2/apache2/conf.d/20-xdebug.ini


# Clear archives in apt cache folder
RUN apt-get clean && rm -rf /var/lib/apt/lists/*

ENTRYPOINT ["/bin/bash"]
