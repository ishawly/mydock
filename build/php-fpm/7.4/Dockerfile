FROM php:7.4.27-fpm
LABEL maintainer="liber@shawly.cn"  license="MIT" version="1.0.0"

ARG TIME_ZONE=UTC
ENV TIME_ZONE ${TIME_ZONE}
RUN ln -snf /usr/share/zoneinfo/${TIME_ZONE} /etc/localtime && echo ${TIME_ZONE} > /etc/timezone

ARG CHANGE_DEBIAN_SOURCE=false
RUN if [ ${CHANGE_DEBIAN_SOURCE} = true ]; then \
    # Change application source from deb.debian.org to aliyun source
    sed -i 's/deb.debian.org/mirrors.aliyun.com/' /etc/apt/sources.list && \
    sed -i 's/security.debian.org/mirrors.aliyun.com/' /etc/apt/sources.list && \
    sed -i 's/security-cdn.debian.org/mirrors.aliyun.com/' /etc/apt/sources.list \
;fi

# always run apt update when start and after add new source list, then clean up at end.
# Install "curl", "libmemcached-dev", "libpq-dev", "libjpeg-dev",
#         "libpng-dev", "libfreetype6-dev", "libssl-dev", "libmcrypt-dev",
RUN set -eux; \
    apt-get update; \
    apt-get upgrade -y; 
    
    #\
    # rm -rf /var/lib/apt/lists/*
RUN set -eux; \
    apt-get install -y --no-install-recommends \
        # "libzip-dev", "zip", "unzip"
        libzip-dev zip unzip

RUN set -eux; \
    apt-get install -y --no-install-recommends \
        curl \
        libmemcached-dev \
        libz-dev \
        libpq-dev \
        libjpeg-dev \
        libpng-dev \
        libfreetype6-dev \
        libssl-dev \
        libwebp-dev \
        libmcrypt-dev \
        libonig-dev;
        # "libzip-dev", "zip", "unzip"
        # libzip-dev zip unzip

RUN set -eux; \
    # Install the bcmath extension
    docker-php-ext-install bcmath; \
    # Install the PHP pdo_mysql extention
    docker-php-ext-install pdo_mysql; \
    # Install the PHP pdo_pgsql extention
    docker-php-ext-install pdo_pgsql; \
    # Install the PHP gd library
    docker-php-ext-configure gd \
            --prefix=/usr \
            --with-jpeg \
            --with-webp \
            --with-freetype; \
    docker-php-ext-install gd; \
    php -r 'var_dump(gd_info());' \
    # Install the zip extension
    docker-php-ext-configure zip; \
    docker-php-ext-install zip && \
      php -m | grep -q 'zip'

# PHP Composer
COPY --from=composer:2.2.1 /usr/bin/composer /usr/local/bin/composer
RUN chmod 755 /usr/local/bin/composer

# Setup Composer Source
ARG CHANGE_COMPOSER_SOURCE=false
RUN if [ $CHANGE_COMPOSER_SOURCE = true ]; then \
	composer config -g repo.packagist composer https://mirrors.aliyun.com/composer/ \
;fi

WORKDIR /var/www

CMD ["php-fpm"]

EXPOSE 9000  