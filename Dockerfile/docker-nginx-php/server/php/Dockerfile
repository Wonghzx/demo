FROM php:7.1-fpm

MAINTAINER Wongzx <Wongzx@wong.com>

# set timezome
ENV TZ=Asia/Shanghai
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone

#安装加速
RUN cp /etc/apt/sources.list  /etc/apt/sources.list.bak
ADD ./sources.list /etc/apt/

# Install package and PHP Core extensions
RUN apt-get update && apt-get install -y \
        curl \
        wget \
        git \
        zip \
        libz-dev \
        libssl-dev \
        libnghttp2-dev \
        libpcre3-dev \
        libfreetype6-dev \
        libjpeg62-turbo-dev \
        libpng-dev \
	&& docker-php-ext-configure gd --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/include/ \
	&& docker-php-ext-install -j$(nproc) gd \
        && docker-php-ext-install zip \
        && docker-php-ext-install pdo_mysql \
        && docker-php-ext-install opcache \
        && docker-php-ext-install mysqli \
        && docker-php-ext-install bcmath \
        && docker-php-ext-install sockets \
        && rm -r /var/lib/apt/lists/*


# Install PECL extensions (Redis)
RUN pecl install redis && echo "extension=redis.so" > /usr/local/etc/php/conf.d/redis.ini

# Install Swoole extension
COPY ./pkg/swoole.tar.gz /home/swoole.tar.gz
RUN cd /home \
        && mkdir -p swoole \
        && tar -xf swoole.tar.gz -C swoole --strip-components=1 \
        && ( \
        cd swoole \
        && phpize \
        && ./configure --enable-async-redis --enable-mysqlnd --enable-openssl --enable-http2 \
        && make -j$(nproc) \
        && make install \
    ) \
    && rm -r /home/swoole \
    && docker-php-ext-enable swoole


# composer 安装文件
COPY ./pkg/composer.phar /home/composer.phar
ENV COMPOSER_HOME /root/composer
RUN mv /home/composer.phar /usr/local/bin/composer
RUN chmod +x /usr/local/bin/composer
ENV PATH $COMPOSER_HOME/vendor/bin:$PATH


RUN rm -f /home/swoole.tar.gz \
        rm -f /home/composer.phar

WORKDIR /data

# composer 切换国内源
RUN composer config -g repo.packagist composer https://packagist.phpcomposer.com

RUN usermod -u 1000 www-data
