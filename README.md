# laravel-php-setup
This is personal setup of my laravel projects on LEMP stack.
I created this docs for personal use, but feel free to use it.
For a note, this docs is the improvised version from many sources on the internet.
In this docs, we will not discuss about firewall & other security stuffs. Ask

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
sudo apt-get install php7.1 php7.1-fpm php7.1-cli php7.1-common php7.1-mysql php7.1-zip php7.1-gd php7.1-mcrypt php7.1-curl php7.1-json php7.1-xml php7.1-opcache php7.1-mbstring
sudo apt-get install nginx

```
Otherwise, you can search any package you need with this command:
```
sudo apt-cache search php7.1-* (or remove * with your package name)
```

## Step 3: Installing Nginx & PHP
### Enable NGINX & PHP-FPM
```
sudo systemctl enable nginx
sudo systemctl start nginx
sudo systemctl enable php7.1-fpm
sudo systemctl start php7.1-fpm
```
then check whether both service is already started without error(s)
```
sudo systemctl status nginx # or enter http://<domain/localhost> to see if nginx page is showing
sudo systemctl status php-7.1-fpm
```

## Step 4: Installing MySQL
```
sudo apt-get install mysql-server-5.7
# then
mysql_secure_installation
```

## Step 5: Configure PHP
check your `php.ini` location with this command
```
php --ini | grep Loaded
```
in my case, my loaded configuration located in: `/etc/php/7.1/cli/php.ini`. 

Let's start edit the `php.ini` file, and change this following line to:
```
...
cgi.fix_pathinfo=0 #default value should be 1, and we changed it to 0
...
```
then restart php-fpm service since we changed the configuration file.
```
sudo systemctl restart php7.1-fpm
```

## Step 6: Setup NGINX to use PHP Preprocessor
For starter, let's open this directory -> `cd /etc/nginx`

Check whether folders `sites-available` & `sites-enabled` is created, if not then create with this command:
```
sudo mkdir sites-available sites-enabled
```
Then edit the nginx global configuration `/etc/nginx/nginx.conf`
Add this line in the bottom of `http` block:
```
...
include /etc/nginx/sites-enabled/*;
```
under `sites-available` folder, create new file. For example, let's create `example.com.conf`
alternatively, if the folders are already created by default. simply copy the `default` file under `sites-available` folder

And add these codes to the configuration file:
```
server {
    listen 80 default_server;
    listen [::]:80 default_server;
    server_name example.com;
    # We will do this later, don't worry!
    access_log /home/username/logs/access.log;
    error_log /home/username/logs/error.log;
    root /home/username/public_html/public;

    location ~ /\.ht {
         deny all;
    }
	
	# Requirement for laravel index query
    location / {
        index index.html index.htm index.php;
        try_files $uri $uri/ /index.php?$query_string;
    }

    location ~ \.php$ {
        try_files $uri =404;
        fastcgi_split_path_info ^(.+\.php)(/.+)$;
        include fastcgi_params;
        fastcgi_pass  127.0.0.1:9000;
        fastcgi_index index.php;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
    }
}
```
And run this command in your terminal to create a symbolic link to your configuration file
```
cd /etc/nginx/sites-enabled
sudo ln -s /etc/nginx/sites-available/example.com.conf
```
Don't forget to save the file you've edited, and try the configuration with this command.
We will not restart the server now, since we haven't set up our project folder.

We need to change the php-fpm config, since the default fpm pool is using socket, we need to change
it using tcp instead of socket. But again, *this is clearly optional*, you can use socket if you want to.
You can adjust the configuration according to your fpm config.

Under the `/etc/php/7.1/fpm/pool.d/www.conf`.
Look for this line, comment the listen to socket line, and change it to this:

```
...
;listen = /run/php/php7.1-fpm.sock
listen = 127.0.0.1:9000

```

And restart the php-fpm service.

### Step 7: Setting up Laravel Project

Let's make a nice environment on our server, first of all, let's add new user and create new home folder.
Use this command on your terminal:

```
adduser username # Be sure to replace the username with the user you want to create
```

Set and confirm the new user's password at the prompt, well, just follow through the prompt until you're finish with it.

Use the usermod command to add the user to the `sudo` group:
```
usermod -aG sudo username
``

From here, let's login to the newly created user:

```
su - username
```

Then, be sure to clone your project, and use this format if you want to.
```
mkdir /home/username/public_html # << this one for our project folder
mkdir /home/username/logs # << this one for nginx logs
```

