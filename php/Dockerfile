# Utiliser l'image PHP 8.2 du conteneur php comme base
FROM php:8.2-apache

# Créer le dossier public dans /var/www/html
RUN mkdir -p /var/www/html/public

# Installation des dépendances nécessaires à Symfony
RUN apt-get update \
    && apt-get install -y zlib1g-dev g++ git libicu-dev zip libzip-dev zip \
    && docker-php-ext-install intl opcache pdo pdo_mysql \
    && pecl install apcu \
    && docker-php-ext-enable apcu \
    && docker-php-ext-configure zip \
    && docker-php-ext-install zip

RUN a2enmod rewrite && a2enmod ssl && a2enmod socache_shmcb

# Définition du répertoire de travail dans le conteneur
WORKDIR /var/www

# Installation de Composer
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer

# Installation de Symfony CLI
RUN curl -sS https://get.symfony.com/cli/installer | bash
RUN mv /root/.symfony5/bin/symfony /usr/local/bin/symfony

# Définir le ServerName dans la configuration d'Apache (même nom que dans le certificat)
RUN echo "ServerName localhost" >> /etc/apache2/apache2.conf

# Génération d'un certificat auto-signé pour localhost
RUN openssl req -x509 -newkey rsa:4096 -keyout /etc/ssl/private/key.pem -out /etc/ssl/certs/localhost.crt -days 365 -nodes -subj '/CN=localhost'

# Copier le certificat auto-signé pour Apache
RUN cp /etc/ssl/certs/localhost.crt /etc/ssl/cert.pem

# Copier la clé privée pour Apache
RUN cp /etc/ssl/private/key.pem /etc/ssl/private/localhost.key

# Copier vos clés SSH dans le conteneur (authntification du serveur su github avec mes clés locales)
COPY ./id_ed25519 /root/.ssh/id_ed25519
COPY ./id_ed25519.pub /root/.ssh/id_ed25519.pub

# Créer le répertoire .ssh et définir les permissions de la clé privée SSH
RUN mkdir -p /root/.ssh \
    && chmod 600 /root/.ssh/id_ed25519 \
    && touch /root/.ssh/known_hosts \
    && ssh-keyscan github.com >> /root/.ssh/known_hosts \
    && git config --global core.sshCommand "ssh -i /root/.ssh/id_ed25519 -o StrictHostKeyChecking=no" \
    && git config --global user.email "simonchabrier@yahoo.fr" \
    && git config --global user.name "Simon Chabrier"


# Commande par défaut pour démarrer Apache dans le conteneur
CMD ["apache2-foreground"]
