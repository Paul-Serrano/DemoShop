# === Base PHP ===
FROM php:8.2-fpm

# === Installer dépendances système ===
RUN apt-get update
RUN apt-get install -y libpq-dev unzip git curl bash
RUN docker-php-ext-install pdo pdo_pgsql

# === Installer Node.js & npm ===
RUN curl -fsSL https://deb.nodesource.com/setup_20.x | bash - \
    && apt-get install -y nodejs \
    && npm install -g npm

# === Installer Composer ===
COPY --from=composer:2 /usr/bin/composer /usr/bin/composer

# === Répertoire de travail ===
WORKDIR /var/www/html

# === Copier fichiers de dépendances ===
COPY composer.json composer.lock* ./
COPY package.json package-lock.json* ./

# === Installer les dépendances PHP & Node ===
RUN composer install --no-dev --optimize-autoloader
RUN npm install

# === Copier tout le code Laravel ===
COPY . .

# === Builder les assets frontend ===
RUN npm run build

# === Exposer un port dynamique pour Railway ===
EXPOSE 8000

# === Entrypoint pour Railway ===
CMD ["sh", "-c", "php artisan serve --host=0.0.0.0 --port=${PORT:-8000}"]
