# lemp4docker

Локальное окружение для разработки на базе Docker: **Linux + Nginx + MariaDB + PHP 8.3 FPM**.

Подходит для запуска Laravel, WordPress, Drupal и других PHP-проектов.

## Стек

| Сервис | Образ | Порт |
|---|---|---|
| Nginx | nginx:latest | 8080 |
| PHP | php:8.3-fpm + Composer | 9000 (внутренний) |
| MariaDB | mariadb:latest | 3306 |
| phpMyAdmin | phpmyadmin:latest | 8183 |

## PHP расширения

`bcmath` `curl` `exif` `fileinfo` `gd` `iconv` `intl` `mbstring` `mysqli` `opcache` `pdo_mysql` `xml` `zip`

## Быстрый старт

```bash
git clone git@github.com:totaku/lemp4docker.git
cd lemp4docker
docker compose up -d
```

После запуска:
- **Nginx** → http://localhost:8080
- **phpMyAdmin** → http://localhost:8183

## Структура

```
lemp4docker/
├── docker-compose.yml
├── hosts/              # Конфиги виртуальных хостов Nginx
├── images/
│   └── php/
│       ├── Dockerfile
│       └── php.ini     # Кастомные настройки PHP
├── logs/               # Логи Nginx
├── mysql/              # Данные БД
└── www/                # Корневая директория проектов
```

## Добавление нового сайта

1. Создать директорию проекта в `www/`:
   ```bash
   mkdir www/mysite.test
   ```

2. Добавить конфиг хоста в `hosts/mysite.conf`:
   ```nginx
   server {
       index index.php index.html;
       server_name mysite.test;
       error_log  /var/log/nginx/error.mysite.log;
       access_log /var/log/nginx/access.mysite.log;
       root /var/www/mysite.test;

       location / {
           try_files $uri $uri/ /index.php?$query_string;
       }

       location ~ \.php$ {
           try_files $uri =404;
           fastcgi_split_path_info ^(.+\.php)(/.+)$;
           fastcgi_pass php:9000;
           fastcgi_index index.php;
           include fastcgi_params;
           fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
           fastcgi_param PATH_INFO $fastcgi_path_info;
       }
   }
   ```

3. Добавить запись в `/etc/hosts` на хост-машине:
   ```
   127.0.0.1 mysite.test
   ```

4. Перезапустить Nginx:
   ```bash
   docker compose restart nginx
   ```

## База данных

- **Хост:** mysql
- **Пользователь:** root
- **Пароль:** secret

## Полезные команды

```bash
# Запустить
docker compose up -d

# Остановить
docker compose down

# Пересобрать образ PHP
docker compose build php

# Логи
docker compose logs -f

# Зайти в контейнер PHP
docker compose exec php bash
```
