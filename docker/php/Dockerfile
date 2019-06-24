# PHP7.2
FROM debian:stretch-slim

ARG APCU_VERSION
ARG PHP_VERSION
ARG NODEJS_VERSION

ENV PHP_VERSION=$PHP_VERSION
ENV APCU_VERSION=$APCU_VERSION
ENV NODEJS_VERSION=$NODEJS_VERSION

ENV PHP_FPM_PID="/run/php/php${PHP_VERSION}-fpm.pid"
ENV PHP_FPM_INCLUDE_CONF="/etc/php/${PHP_VERSION}/fpm/pool.d/*.conf"

ENV TERM xterm
ENV ZSH_THEME muse

ARG USER_ID
ARG GROUP_ID

RUN echo "**** USER_ID : $USER_ID ****"
RUN echo "**** GROUP_ID : $GROUP_ID ****"

RUN set -xe

RUN apt-get update -y && apt-get upgrade -y

RUN apt-get install -y \
  build-essential \
  apt-utils \
  ca-certificates \
  apt-transport-https \
  gnupg \
  wget \
  curl \
  libcurl4-openssl-dev \
  nano \
  git \
  zip \
  iputils-ping \
  vim \
  dnsutils \
  net-tools \
  htop \
  zsh

RUN echo "**** Version de PHP utilisée : $PHP_VERSION ****"
RUN echo "**** Version de PHP_FPM utilisée : $PHP_FPM_PID ****"
RUN echo "**** Version de FPM_INCLUDE_CONF utilisée : $PHP_INCLUDE_CONF ****"

RUN curl -sS https://packages.sury.org/php/apt.gpg | apt-key add -
RUN echo "deb https://packages.sury.org/php/ stretch main" | tee /etc/apt/sources.list.d/php.list

# Yarn
RUN curl -sS https://dl.yarnpkg.com/debian/pubkey.gpg | apt-key add -
RUN echo "deb https://dl.yarnpkg.com/debian/ stable main" | tee /etc/apt/sources.list.d/yarn.list

RUN apt-get update

# Install PHP package system
# Attention certains packages ne fournissent pas toutes les versions PHP
RUN echo "**** Installation des packages PHP $PHP_VERSION ****"

RUN apt-get install -y \
  php${PHP_VERSION}-fpm \
  php${PHP_VERSION}-cli \
  php${PHP_VERSION}-common \
  php${PHP_VERSION}-opcache \
  php${PHP_VERSION}-curl \
  php${PHP_VERSION}-mbstring \
  php${PHP_VERSION}-zip \
  php${PHP_VERSION}-xml \
  php${PHP_VERSION}-curl \
  php${PHP_VERSION}-gd \
  php${PHP_VERSION}-xsl \
  php${PHP_VERSION}-intl \
  php${PHP_VERSION}-bz2 \
  php${PHP_VERSION}-zip \
  php${PHP_VERSION}-apcu \
  php${PHP_VERSION}-sqlite \
  php${PHP_VERSION}-opcache \
  php${PHP_VERSION}-xml \
  php${PHP_VERSION}-bcmath \
  php${PHP_VERSION}-pgsql \
  php${PHP_VERSION}-soap \
  php${PHP_VERSION}-mysql \
  php-redis \
  php-amqp \
  php-yaml \
  php-xdebug \
  libpq-dev \
  libpng-dev \
  libjpeg-dev \
  libpq-dev \
  libfreetype6-dev \
  openssl \
  htmldoc \
  libmcrypt-dev

RUN mkdir /run/php

RUN php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"
RUN php composer-setup.php
RUN php -r "unlink('composer-setup.php');"
RUN mv composer.phar /usr/local/bin/composer
RUN chmod +x /usr/local/bin/composer

RUN mv /etc/php/${PHP_VERSION}/fpm/pool.d/www.conf /etc/php/${PHP_VERSION}/fpm/pool.d/www.conf.bak
ADD ./docker/php/conf/www.conf /etc/php/${PHP_VERSION}/fpm/pool.d/www.conf

RUN mv /etc/php/${PHP_VERSION}/fpm/php-fpm.conf /etc/php/${PHP_VERSION}/fpm/php-fpm.conf.bak
ADD ./docker/php/conf/php-fpm.conf /etc/php/${PHP_VERSION}/fpm/php-fpm.conf

RUN mv /etc/php/${PHP_VERSION}/fpm/php.ini /etc/php/${PHP_VERSION}/fpm/php.ini.bak
ADD ./docker/php/conf/php.ini /etc/php/${PHP_VERSION}/fpm/php.ini

RUN mv /etc/php/${PHP_VERSION}/cli/php.ini /etc/php/${PHP_VERSION}/cli/php.ini.bak
ADD ./docker/php/conf/php-cli.dev.ini /etc/php/${PHP_VERSION}/cli/php.ini



# Installation NodeJS
#RUN curl -sL https://deb.nodesource.com/setup_${NODEJS_VERSION} | bash -
#RUN apt-get install -y nodejs

# Installation LESS et BOWER (utile ?)
#RUN npm install less -g
#RUN ln -s /usr/lib/node_modules/ ~/.node_libraries
#RUN ln -s /usr/bin/node /usr/local/bin/node
#RUN npm install bower -g
#RUN echo '{ "allow_root": true }' > /root/.bowerrc

# Install YARN
#RUN apt-get install -y yarn

# Install ZSH
RUN wget https://github.com/robbyrussell/oh-my-zsh/raw/master/tools/install.sh -O - | zsh || true
RUN chsh -s /bin/zsh

#ADD ./docker/php/docker-entrypoint.sh /usr/local/bin/docker-entrypoint
#RUN chmod +x /usr/local/bin/docker-entrypoint

#Alias SF
RUN echo 'alias sf="php bin/console"' >> ~/.zshrc
RUN echo 'alias ll="ls -als"' >> ~/.zshrc

WORKDIR /var/www/symfony

EXPOSE 9000

#ENTRYPOINT ["/usr/local/bin/docker-entrypoint"]

CMD php-fpm${PHP_VERSION}

RUN if [ ${USER_ID:-0} -ne 0 ] && [ ${GROUP_ID:-0} -ne 0 ]; then \
    userdel -f www-data &&\
    if getent group www-data ; then groupdel www-data; fi &&\
    groupadd -g ${GROUP_ID} www-data &&\
    useradd -l -u ${USER_ID} -g www-data www-data &&\
    install -d -m 0755 -o www-data -g www-data /home/www-data &&\
    chown --changes --silent --no-dereference --recursive \
              --from=33:33 ${USER_ID}:${GROUP_ID} \
            /home/www-data \
            /var/run/php \
            /var/lib/php/sessions \
;fi

#ENV COMPOSER_ALLOW_SUPERUSER 1
#RUN composer global require "hirak/prestissimo:^0.3" --prefer-dist --no-progress --no-suggest --classmap-authoritative
#RUN composer global require fxp/composer-asset-plugin
# Prevent the reinstallation of vendors at every changes in the source code
#COPY composer.json composer.lock /srv/redpill/
#RUN composer dump-autoload --classmap-authoritative

# Création dossier php pour ses logs
# Actuellement slowlog = /proc/self/fd/2 est configuré dans www.config
#RUN mkdir /var/log/php
#RUN chmod -R 777 /var/log/php/
#RUN touch /var/log/php/fpm-slow.log

