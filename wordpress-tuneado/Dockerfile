FROM alpine:latest 
LABEL Maintainer="Juan Miguel Garcia Perez" Description="WordPress connected to MariaDB"
RUN apk --no-cache add php7 php7-fpm php7-mysqli php7-json php7-openssl php7-curl php7-zlib php7-xml php7-phar php7-intl php7-dom php7-xmlreader php7-ctype php7-mbstring php7-gd nginx --repository http://dl-cdn.alpinelinux.org/alpine/edge/main/ --repository http://dl-cdn.alpinelinux.org/alpine/edge/testing/
 
RUN apk --no-cache add --update supervisor curl bash pwgen mysql mysql-client
COPY config/nginx.conf /etc/nginx/nginx.conf

COPY config/fpm-pool.conf /etc/php7/php-fpm.d/zzz_custom.conf
COPY config/php.ini /etc/php7/conf.d/zzz_custom.ini
COPY config/supervisord.conf /etc/supervisor/conf.d/supervisord.conf
VOLUME /var/www/wp-content
WORKDIR /var/www/wp-content
RUN chown -R nobody.nobody /var/www

RUN /usr/bin/mysql_install_db --user=mysql && cp /usr/share/mysql/mysql.server /etc/init.d/mysqld
ENV WORDPRESS_VERSION 4.7.4 
ENV WORDPRESS_SHA1 153592ccbb838cafa1220de9174ec965df2e9e1a 

RUN mkdir -p /usr/src && curl -o wordpress.tar.gz -SL https://wordpress.org/wordpress-${WORDPRESS_VERSION}.tar.gz && echo "$WORDPRESS_SHA1 *wordpress.tar.gz" | sha1sum -c - && tar -xzf wordpress.tar.gz -C /usr/src/ && rm wordpress.tar.gz && chown -R nobody.nobody /usr/src/wordpress

COPY config/wp-config.php /usr/src/wordpress

RUN chown nobody.nobody /usr/src/wordpress/wp-config.php && chmod 640 /usr/src/wordpress/wp-config.php

COPY config/wp-secrets.php /usr/src/wordpress

RUN chown nobody.nobody /usr/src/wordpress/wp-secrets.php && chmod 640 /usr/src/wordpress/wp-secrets.php

COPY entrypoint.sh /entrypoint.sh

ENTRYPOINT [ "/entrypoint.sh" ]

EXPOSE 80 

CMD ["/usr/bin/supervisord", "-c", "/etc/supervisor/conf.d/supervisord.conf"]
