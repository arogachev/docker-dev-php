ARG PHP_VERSION
FROM php:$PHP_VERSION

ARG XDEBUG_VERSION

# Upgrade packages
RUN apt -y update && apt -y upgrade

# Required tools
# - Composer: git, unzip, bash-completion
# - PHP intl extension: libicu-dev
# - SSH agent: openssh-client
RUN apt -y install \
    sudo \
    git \
    gnupg \
    unzip \
    libicu-dev \
    bash-completion \
    locales \
    openssh-client

# PHP extensions
RUN docker-php-ext-install \
    intl \
    gettext \
    sockets

# Xdebug
RUN if [ -z $XDEBUG_VERSION ] ; then pecl install xdebug ; else pecl install "xdebug-$XDEBUG_VERSION" ; fi
COPY xdebug.ini /usr/local/etc/php/conf.d/xdebug.ini

# PHP configuration
COPY php.ini /usr/local/etc/php/conf.d/php.ini

# Composer
COPY --from=composer /usr/bin/composer /usr/bin/composer
RUN composer completion bash | tee /etc/bash_completion.d/composer

# Node.js
RUN curl -sL https://deb.nodesource.com/setup_19.x | bash - && \
    apt-get install -y nodejs

# Locales
RUN sed -i '/en_US\.UTF-8/s/^# //g' /etc/locale.gen
RUN locale-gen
ENV LC_ALL en_US.UTF-8

# Starship shell prompt
RUN sh -c "$(curl -fsSL https://starship.rs/install.sh)" -- --yes

# Initial script
COPY docker-entrypoint.sh /usr/bin

ENTRYPOINT ["/usr/bin/docker-entrypoint.sh"]
