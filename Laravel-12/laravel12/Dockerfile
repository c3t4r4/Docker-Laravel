# Imagem base Ubuntu 24.04
FROM ubuntu:24.04

# Evitar interações durante a instalação
ENV DEBIAN_FRONTEND=noninteractive
ARG NODE_VERSION=22
ARG MYSQL_CLIENT="mysql-client"
ARG POSTGRES_VERSION=17

# Configurar timezone
ENV TZ=America/Sao_Paulo
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone

# Atualizar sistema e instalar dependências básicas
RUN apt update && apt dist-upgrade -y && apt autoremove -y && apt install -y \
acl \
btop \
ca-certificates \
curl \
dnsutils \
ffmpeg \
fonts-powerline \
fswatch \
git \
gnupg \
gosu \
htop \
imagemagick \
libcap2-bin \
libmagickcore-dev \
libmagickwand-dev \
libpng-dev \
librsvg2-bin \
make \
nano \
ncdu \
nginx \
python3 \
software-properties-common \
sqlite3 \
supervisor \
unzip \
wget \
wormhole \
zip \
zsh

# Instalar Repositorios
RUN  mkdir -p /etc/apt/keyrings \
&& curl -sS 'https://keyserver.ubuntu.com/pks/lookup?op=get&search=0xb8dc7e53946656efbce4c1dd71daeaab4ad4cab6' | gpg --dearmor | tee /etc/apt/keyrings/ppa_ondrej_php.gpg > /dev/null \
&& echo "deb [signed-by=/etc/apt/keyrings/ppa_ondrej_php.gpg] https://ppa.launchpadcontent.net/ondrej/php/ubuntu noble main" > /etc/apt/sources.list.d/ppa_ondrej_php.list \
&& curl -sS https://dl.yarnpkg.com/debian/pubkey.gpg | gpg --dearmor | tee /etc/apt/keyrings/yarn.gpg >/dev/null \
&& echo "deb [signed-by=/etc/apt/keyrings/yarn.gpg] https://dl.yarnpkg.com/debian/ stable main" > /etc/apt/sources.list.d/yarn.list \
&& curl -sS https://www.postgresql.org/media/keys/ACCC4CF8.asc | gpg --dearmor | tee /etc/apt/keyrings/pgdg.gpg >/dev/null \
&& echo "deb [signed-by=/etc/apt/keyrings/pgdg.gpg] http://apt.postgresql.org/pub/repos/apt noble-pgdg main" > /etc/apt/sources.list.d/pgdg.list \
&& apt update

# Instalar PHP 8.4 com extensões necessárias para Laravel
RUN apt install -y --no-install-recommends \
    php8.4 \
    php8.4-bcmath \
    php8.4-cli \
    php8.4-common \
    php8.4-curl \
    php8.4-dev \
    php8.4-fpm \
    php8.4-gd \
    php8.4-gmp \
    php8.4-igbinary \
    php8.4-imagick \
    php8.4-imap \
    php8.4-intl \
    php8.4-ldap \
    php8.4-mbstring \
    php8.4-memcached \
    php8.4-mongodb \
    php8.4-msgpack \
    php8.4-mysql \
    php8.4-pcov \
    php-pear \
    php8.4-pgsql \
    php8.4-readline \
    php8.4-redis \
    php8.4-soap \
    php8.4-sqlite3 \
    php8.4-swoole \
    php8.4-zip \
    php8.4-xml \
    php8.4-xdebug \
    yarn \
    $MYSQL_CLIENT \
    postgresql-client-$POSTGRES_VERSION

# Instalar Composer
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer

#OMZ
RUN sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)" "" --unattended

# Configurar PHP 8.4 como versão principal
RUN update-alternatives --set php /usr/bin/php8.4 && \
    update-alternatives --set phar /usr/bin/phar8.4 && \
    update-alternatives --set phar.phar /usr/bin/phar.phar8.4 && \
    update-alternatives --set phpize /usr/bin/phpize8.4 && \
    update-alternatives --set php-config /usr/bin/php-config8.4

# Instalar Node.js LTS e npm
RUN curl -fsSL https://deb.nodesource.com/setup_$NODE_VERSION.x | bash - \
    && apt install -y nodejs \
    && npm install -g npm@latest \
    && npm install -g pnpm \
    && npm install -g bun

# Limpar cache
RUN apt clean \
&& rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

# Configurar ZSH como shell padrão
RUN chsh -s $(which zsh)

# Customizar Oh My Zsh (opcional)
RUN sed -i 's/ZSH_THEME="robbyrussell"/ZSH_THEME="gentoo"/' ~/.zshrc

# Instalar plugins populares do Oh My Zsh (opcional)
RUN git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions && \
    git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting

# Adicionar plugins ao .zshrc
RUN sed -i 's/plugins=(git)/plugins=(git zsh-autosuggestions zsh-syntax-highlighting composer laravel)/' ~/.zshrc

# Configurar ambiente PHP
RUN echo "export PATH=\"\$PATH:/usr/local/bin\"" >> ~/.zshrc && \
    echo "export COMPOSER_ALLOW_SUPERUSER=1" >> ~/.zshrc

# Criar diretório de trabalho
WORKDIR /var/www/html

# Definir ZSH como shell padrão para o container
SHELL ["/bin/zsh", "-c"]

# Configurar nginx
COPY nginx.conf /etc/nginx/sites-available/default

# Configurar PHP-FPM
COPY php.ini /etc/php/8.4/fpm/php.ini


# Configurar permissões
RUN mkdir -p /var/run/php && \
    mkdir -p /var/www/app && \
    mkdir -p /var/www/app/public && \
    chown -R www-data:www-data /var/www/app/public

# Configurar Supervisor
COPY supervisord.conf /etc/supervisor/conf.d/supervisord.conf

# Expor porta 80
EXPOSE 80/tcp

# Comando para iniciar serviços
CMD ["/usr/bin/supervisord", "-n", "-c", "/etc/supervisor/conf.d/supervisord.conf"]