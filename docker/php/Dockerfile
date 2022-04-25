FROM php:8.1.0-alpine

RUN apk add --update --no-cache \
    autoconf \
    openssl-dev \
    g++ \
    make \
    pcre-dev \
    icu-dev \
    zlib-dev \
    libzip-dev \
    && docker-php-ext-install \
    bcmath \
    intl \
    opcache \
    zip \
    sockets
#    && apk del --purge autoconf g++ make

RUN pecl install xdebug && docker-php-ext-enable xdebug
COPY ./docker/php/xdebug.ini /usr/local/etc/php/conf.d/xdebug.ini

# Add Composer
COPY --from=composer:latest /usr/bin/composer /usr/bin/composer

# users ids could retrieved by the command `id $USER`
ARG PUID=1000
ARG PGID=1000
ARG UNAME=user

RUN apk add --no-cache shadow \
    && addgroup -g ${PGID} ${UNAME} \
    && adduser -u ${PUID} -G ${UNAME} -D ${UNAME}

USER ${UNAME}

# Copying project files
COPY --chown=${PGID}:${PUID} . /app
WORKDIR /app

ARG APP_ENV=prod
ENV APP_ENV=${APP_ENV}

# Install Composer
RUN composer install --no-interaction \
#    && vendor/bin/rr get --location bin \
    && composer dump-autoload --optimize \
    && composer check-platform-reqs \
    && php bin/console cache:warmup

EXPOSE 8080

CMD ["bin/rr", "serve"]
