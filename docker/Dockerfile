# === Base PHP ===
FROM php:8.2-fpm

# === Installer dépendances système ===
RUN apt-get update && apt-get install -y \
    libpq-dev \
    unzip \
    git \
    curl \
    bash \
    && docker-php-ext-install pdo pdo_pgsql

# === Installer Node.js & npm ===
RUN curl -fsSL https://deb.nodesource.com/setup_20.x | bash - \
    && apt-get install -y nodejs \
    && npm install -g npm

# === Installer Composer ===
COPY --from=composer:2 /usr/bin/composer /usr/bin/composer

# === Créer le répertoire de travail ===
WORKDIR /var/www/html

# === Copier uniquement les fichiers de dépendances en premier (cache) ===
# (comme le context est ./src, ces fichiers existent bien à la racine du context)
COPY composer.json composer.lock ./
COPY package.json package-lock.json* ./

# === Installer dépendances PHP & Node ===
RUN composer install --no-dev --optimize-autoloader
RUN npm install

# === Copier tout le code Laravel (src/) ===
COPY . .

# === Builder les assets ===
RUN npm run build

# === Exposer un port Laravel dynamique ===
EXPOSE 8000

# === Entrypoint dynamique pour Railway et local ===
CMD ["sh", "-c", "php artisan serve --host=0.0.0.0 --port=${PORT:-8000}"]
