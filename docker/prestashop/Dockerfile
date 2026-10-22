FROM prestashop/prestashop:8.2.7-8.1-apache@sha256:e4fb5b37f3f3acb013c6c8716f99b6164b0f749e1e0f3a3963a458f5bcbba51d AS base

FROM base AS patched-php

RUN apt-get update \
 && apt-get install -y --no-install-recommends \
      autoconf bison dpkg-dev file g++ gcc libc-dev make pkg-config re2c \
      apache2-dev libargon2-dev libcurl4-openssl-dev libonig-dev libreadline-dev \
      libsodium-dev libsqlite3-dev libssl-dev libxml2-dev zlib1g-dev \
      libfreetype-dev libjpeg62-turbo-dev libpng-dev libwebp-dev libicu-dev libzip-dev

ADD --keep-git-dir=false https://github.com/php/php-src.git#1d7dec178a094e6e75b229ce22754045f8c6070a /usr/src/php-patched

WORKDIR /usr/src/php-patched

RUN ./buildconf --force \
 && ./configure $(php-config --configure-options) \
      --enable-bcmath=shared \
      --enable-gd=shared --with-freetype --with-jpeg --with-webp \
      --enable-intl=shared \
      --with-pdo-mysql=shared,mysqlnd \
      --enable-soap=shared \
      --with-zip=shared \
      --enable-opcache=shared \
 && make -j"$(nproc)" \
 && make install

FROM base

ENV PHP_VERSION=8.1.34-cve-2026-109618-109619

COPY --from=patched-php /usr/local/bin/php /usr/local/bin/php
COPY --from=patched-php /usr/lib/apache2/modules/libphp.so /usr/lib/apache2/modules/libphp.so
COPY --from=patched-php /usr/local/lib/php/extensions/no-debug-non-zts-20210902/ /usr/local/lib/php/extensions/no-debug-non-zts-20210902/
