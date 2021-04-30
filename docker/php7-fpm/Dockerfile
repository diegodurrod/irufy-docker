FROM php:7.3-fpm

LABEL maintainer="Diego Durán Rodríguez <diego.duran.rguez@gmail.com>"

# Add Debian repository mirrors
RUN echo \
   'deb http://ftp.be.debian.org/debian/ buster main\n \
    deb http://ftp.be.debian.org/debian/ buster main contrib non-free\n \
    deb http://ftp.be.debian.org/debian-security/ buster/updates main\n \
    deb http://ftp.be.debian.org/debian/ buster-updates main\n' \
    > /etc/apt/sources.list

RUN apt update

#Some dependencies
RUN apt install --no-install-recommends -y \
curl \
unzip \
nano \
libfreetype6-dev \
libjpeg62-turbo-dev \
libjpeg-turbo-progs \
libmcrypt-dev \
libpng-dev \
zlib1g-dev \
libicu-dev \
g++ \
unixodbc-dev \
libxml2-dev \
libaio-dev \
libssl-dev \
openssl \
git \
ssh \
acl \
libcurl4-openssl-dev \
pngquant \
gifsicle \
zip \
libzip-dev


# Install Composer
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer \
    && composer --version

# Install Symfony
RUN curl -LsS https://symfony.com/installer -o /usr/local/bin/symfony \
    && chmod a+x /usr/local/bin/symfony

# Install PHPUnit
RUN cd /tmp && curl -L https://phar.phpunit.de/phpunit.phar > phpunit.phar \
    && chmod +x phpunit.phar \
    && mv /tmp/phpunit.phar /usr/local/bin/phpunit

# Set timezone
RUN rm /etc/localtime \
    && ln -s /usr/share/zoneinfo/Europe/Madrid /etc/localtime \
    && "date"

# install xdebug
RUN docker-php-source extract
RUN pecl install xdebug
RUN docker-php-ext-enable xdebug
RUN docker-php-source delete
RUN echo "error_reporting = E_ALL" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini \
    && echo "display_startup_errors = On" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini \
    && echo "display_errors = On" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini \
    && echo "xdebug.remote_enable=1" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini \
    && echo "xdebug.remote_connect_back=1" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini \
    && echo "xdebug.idekey=\"PHPSTORM\"" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini \
    && echo "xdebug.remote_port=9001" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini

# gd
#RUN docker-php-ext-configure gd --with-gd --with-webp-dir --with-jpeg-dir \
#    --with-png-dir --with-zlib-dir --with-xpm-dir --with-freetype-dir \
#    --enable-gd-native-ttf
RUN docker-php-ext-configure gd --with-gd --with-jpeg-dir --with-png-dir --with-zlib-dir --with-freetype-dir # --enable-gd-native-ttf
RUN docker-php-ext-install gd

# zip
RUN docker-php-ext-install zip
RUN docker-php-ext-configure zip --with-libzip

# cURL
RUN docker-php-ext-install curl

# cURL
RUN docker-php-ext-install json

# MySQL
RUN docker-php-ext-install pdo pdo_mysql

# Soap
RUN docker-php-ext-install soap

#Imagemagick
RUN apt install libmagickwand-dev imagemagick -y && pecl install imagick && docker-php-ext-enable imagick

#wkhtmltopdf
RUN apt-get install -y \
            curl \
            libxrender1 \
            libfontconfig \
            libxtst6 \
            xz-utils \
            libssl-dev \
        && curl "https://github.com/wkhtmltopdf/wkhtmltopdf/releases/download/0.12.4/wkhtmltox-0.12.4_linux-generic-amd64.tar.xz" -L -o "wkhtmltopdf.tar.xz" \
        && tar Jxvf wkhtmltopdf.tar.xz \
        && mv wkhtmltox/bin/wkhtmltopdf /usr/local/bin/wkhtmltopdf

#Alias
RUN echo 'alias sf="php /var/www/irufy/app/console"' >> ~/.bashrc \
    && echo 'alias cacheFix="sf cache:clear ; chmod 777 -R /var/www/irufy/app/cache/"' >> ~/.bashrc

#Configure
COPY custom-php.ini /usr/local/etc/php/conf.d/custom-php.ini
COPY custom-php-fpm.ini /usr/local/etc/php-fpm.d/www.conf
COPY id_rsa_root /root/.ssh/id_rsa
COPY id_rsa_root.pub /root/.ssh/id_rsa.pub

WORKDIR /var/www/irufy
