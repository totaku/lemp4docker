# Базовый образ — официальный PHP 8.3 FPM (FastCGI Process Manager)
# FPM умеет обрабатывать запросы от nginx через FastCGI протокол
FROM php:8.3-fpm

# Устанавливаем системные зависимости и PHP расширения.
# Всё в одном RUN чтобы не плодить лишние слои в образе — каждая инструкция RUN создаёт новый слой.
# В конце чистим кеш apt чтобы уменьшить размер образа.
RUN apt-get update && apt-get install -y \
        # утилиты
        curl \
        wget \
        git \
        # системные библиотеки, необходимые для сборки PHP расширений
        libcurl4-openssl-dev \
        libexif-dev \           # exif — метаданные изображений
        libfreetype6-dev \      # шрифты для gd
        libicu-dev \            # intl — интернационализация
        libjpeg-dev \           # jpeg для gd
        libonig-dev \           # mbstring — многобайтовые строки
        libpng-dev \            # png для gd
        libxml2-dev \           # xml, soap
        libzip-dev \            # zip
    # docker-php-ext-install — хелпер для установки расширений из исходников PHP
    # -j$(nproc) — параллельная сборка на всех доступных ядрах
    && docker-php-ext-install -j$(nproc) \
        bcmath \    # арифметика высокой точности
        curl \      # HTTP клиент
        exif \      # чтение метаданных изображений (нужен WordPress)
        fileinfo \  # определение типа файла
        iconv \     # перекодировка строк
        intl \      # интернационализация и локализация
        mbstring \  # работа с многобайтовыми строками
        mysqli \    # драйвер MySQL (процедурный стиль)
        opcache \   # кеширование скомпилированного PHP кода
        pdo_mysql \ # драйвер MySQL через PDO
        xml \       # работа с XML
        zip \       # работа с zip архивами
    # gd требует отдельной конфигурации перед установкой — указываем поддержку freetype и jpeg
    && docker-php-ext-configure gd --with-freetype --with-jpeg \
    && docker-php-ext-install -j$(nproc) gd \
    && apt-get clean && rm -rf /var/lib/apt/lists/*

# Composer устанавливается копированием бинарника из официального образа.
# Это надёжнее и быстрее чем скачивать инсталлятор через curl.
COPY --from=composer:latest /usr/bin/composer /usr/local/bin/composer

# Подключаем кастомный php.ini с нашими настройками
ADD php.ini /usr/local/etc/php/conf.d/40-custom.ini

# Рабочая директория внутри контейнера
WORKDIR /var/www

# Запускаем PHP-FPM процесс
CMD ["php-fpm"]
