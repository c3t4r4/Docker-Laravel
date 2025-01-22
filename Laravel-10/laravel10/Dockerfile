# Imagem base Ubuntu 24.04
FROM ubuntu:24.04

# Evitar interações durante a instalação
ENV DEBIAN_FRONTEND=noninteractive

# Configurar timezone
ENV TZ=America/Sao_Paulo
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone

# Atualizar sistema e instalar dependências básicas
RUN apt update && apt dist-upgrade -y && apt autoremove -y && apt install -y \
    software-properties-common \
    curl \
    git \
    nginx \
    nano \
    zip \
    unzip \
    supervisor \
    acl \
    htop \
    btop \
    ncdu \
    zsh \
    wget \
    make \
    fonts-powerline \
    && apt clean \
    && rm -rf /var/lib/apt/lists/*

# Adicionar repositório PHP e instalar PHP 8.3 com extensões necessárias para Laravel
RUN add-apt-repository ppa:ondrej/php && \
    apt-get update && \
    apt-get install -y --no-install-recommends \
    php8.3 \
    php8.3-fpm \
    php8.3-cli \
    php8.3-common \
    php8.3-mysql \
    php8.3-zip \
    php8.3-gd \
    php8.3-mbstring \
    php8.3-curl \
    php8.3-xml \
    php8.3-bcmath \
    php8.3-intl \
    php8.3-readline \
    php8.3-soap \
    php8.3-gmp \
    php8.3-pgsql \
    php8.3-sqlite3 \
    php8.3-dev \
    php-pear \
    && pecl install redis \
    && echo "extension=redis.so" > /etc/php/8.3/mods-available/redis.ini \
    && phpenmod redis \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

# Instalar Composer
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer

#OMZ
RUN sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)" "" --unattended

# Configurar PHP 8.3 como versão principal
RUN update-alternatives --set php /usr/bin/php8.3 && \
    update-alternatives --set phar /usr/bin/phar8.3 && \
    update-alternatives --set phar.phar /usr/bin/phar.phar8.3 && \
    update-alternatives --set phpize /usr/bin/phpize8.3 && \
    update-alternatives --set php-config /usr/bin/php-config8.3

# Instalar Node.js LTS e npm
RUN curl -fsSL https://deb.nodesource.com/setup_22.x | bash - \
    && apt-get install -y nodejs \
    && npm install -g npm@latest

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
RUN sed -i 's/;cgi.fix_pathinfo=1/cgi.fix_pathinfo=0/g' /etc/php/8.3/fpm/php.ini

# Configurar permissões
RUN mkdir -p /var/run/php && \
    mkdir -p /var/www/app && \
    mkdir -p /var/www/app/public && \
    chown -R www-data:www-data /var/www/app/public

# Configurar Supervisor
COPY supervisord.conf /etc/supervisor/conf.d/supervisord.conf

# Expor porta 80
EXPOSE 80

# Comando para iniciar serviços
CMD ["/usr/bin/supervisord", "-n", "-c", "/etc/supervisor/conf.d/supervisord.conf"]