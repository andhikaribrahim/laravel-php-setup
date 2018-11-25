# laravel-php-setup
This is personal setup of my laravel projects on LEMP stack.
I created this docs for personal use, but feel free to use it.
For a note, this docs is the improvised version from many sources on the internet.

# Requirements
- NGINX
- PHP 7.1
- Laravel 5.4
- MySQL 5.*

# Server Installation: Ubuntu 16.04
## Step 1: Install general packages & Enable Ondrej's PPA
```
sudo apt-get install software-properties-common
sudo add-apt-repository ppa:ondrej/php
sudo apt-get update
sudo apt-get install zip unzip mcrypt git
```

## Step 2: Install NGINX/PHP and its dependencies
```
sudo apt-get install php7.1 php7.1-fpm php7.1-mysql php7.1-zip php7.1-gd php7.1-mcrypt php7.1-curl php7.1-json php7.1-xml
sudo apt-get install nginx

```