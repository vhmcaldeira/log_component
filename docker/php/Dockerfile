FROM php:8.2-fpm

# Install system dependencies
RUN apt-get update && apt-get install -y \
    git \
    zip \
    unzip \
    libpng-dev \
    libonig-dev \
    libxml2-dev \
    curl \
    && docker-php-ext-install pdo_mysql mbstring exif pcntl bcmath gd

# Install Composer
COPY --from=composer:latest /usr/bin/composer /usr/bin/composer

# Set working directory
WORKDIR /var/www

# Copy existing application directory contents
COPY . /var/www

# The Laravel application should already be present in the repository.
# Previously the image downloaded Laravel during build time which
# prevented editing the source outside the container. This step was
# removed so the existing project is used as-is.

# Set permissions
RUN chown -R www-data:www-data /var/www

CMD ["php-fpm"]
