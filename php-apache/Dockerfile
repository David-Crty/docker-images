FROM debian:jessie
ENV DEBIAN_FRONTEND noninteractive

# Configure timezone and locale
RUN apt-get update && apt-get install -y locales
RUN echo "Europe/Paris" > /etc/timezone && \
    dpkg-reconfigure -f noninteractive tzdata && \
    sed -i -e 's/# en_US.UTF-8 UTF-8/en_US.UTF-8 UTF-8/' /etc/locale.gen && \
    sed -i -e 's/# nb_NO.UTF-8 UTF-8/nb_NO.UTF-8 UTF-8/' /etc/locale.gen && \
    echo 'LANG="nb_NO.UTF-8"'>/etc/default/locale && \
    dpkg-reconfigure --frontend=noninteractive locales && \
    update-locale LANG=nb_NO.UTF-8

RUN apt-get update && apt-get install -y apache2 git curl wget ssmtp
ADD ssmtp.conf /etc/ssmtp/ssmtp.conf

RUN apt-get update && apt-get install -y \
        php5 \
        php5-common \
        php5-cli \
        php5-mcrypt \
        php5-mysql \
        php5-apcu \
        php5-gd \
        php5-imagick \
        php5-curl \
        php5-intl

ADD php.ini /etc/php5/apache2/conf.d/php_custome.ini
ADD apache.conf /etc/apache2/sites-available/apache.conf

RUN ln -s /etc/apache2/sites-available/apache.conf /etc/apache2/sites-enabled/apache.conf
RUN rm /etc/apache2/sites-enabled/000-default.conf
RUN rm /etc/apache2/sites-available/000-default.conf && rm /etc/apache2/sites-available/default-ssl.conf

#wkhtmltopdf
RUN sed -i "s/jessie main/jessie main contrib/" /etc/apt/sources.list
RUN apt-get update && apt-get install -y build-essential xorg libssl-dev libxrender-dev fontconfig ttf-mscorefonts-installer xfonts-base xfonts-75dpi gdebi
RUN wget http://download.gna.org/wkhtmltopdf/0.12/0.12.2.1/wkhtmltox-0.12.2.1_linux-jessie-amd64.deb
RUN gdebi --non-interactive wkhtmltox-0.12.2.1_linux-jessie-amd64.deb

#NPM
RUN apt-get update && apt-get install -y build-essential
RUN curl -sL https://deb.nodesource.com/setup_6.x | bash -
RUN apt-get install -y nodejs


#GULP / BOWER 
RUN npm install -g bower 
RUN npm install -g gulp 


RUN curl -sSL https://getcomposer.org/installer | \
    php -- --install-dir=/usr/local/bin --filename=composer

RUN a2enmod rewrite
RUN a2enmod headers
RUN service apache2 stop

RUN useradd -ms /bin/bash awstudio
RUN usermod -u 1001 awstudio
COPY envvars /etc/apache2/envvars


WORKDIR /var/www
EXPOSE 80
CMD ["/usr/sbin/apache2ctl", "-D", "FOREGROUND"]

