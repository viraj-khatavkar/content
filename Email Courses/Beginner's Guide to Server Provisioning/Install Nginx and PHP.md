#Install Nginx and PHP-FPM

Provisioning a new LEMP (**L**inux, **(E)**Nginx, **M**ySQL, **P**HP) server is a daunting task. We have to install proper packages, setup virtual hosts, make sure that our server is secure and many other such tasks. 

Today, we will install Nginx and PHP-FPM on a brand new Ubuntu 16.04 server.

##Why Nginx and PHP-FPM

Nginx (pronounced "Engine X") is not better or worse than Apache in serving static or PHP files. It is pretty much the same. The main difference is Nginx can serve a far more amount of users than Apache without the need of additional resources. You can read more about the difference between Nginx and Apache [on this link](https://www.digitalocean.com/community/tutorials/apache-vs-nginx-practical-considerations).

We will install PHP-FPM (FastCGI Process Manager) rather than native PHP FastCGI implementation. Without going into many technical details, I can say that PHP-FPM is much more efficient in serving sites with heavy traffic. It has some improvements and features which enable it to be so efficient and high performant.

##Step 1: Update Server and Packages

We will use a brand new Ubuntu 16.04 VPS. It has some default packages pre-installed. But, they might be of older versions. To stay updated, we will first update the default packages:

```bash
apt-get update
apt-get upgrade -y
```

1. `apt-get update`: It will update the list of available packages and versions on our Ubuntu server. But, it won't actually download and update the packages.
2. `apt-get upgrade`: This will download the newer versions of the packages and install them on the server. After running `apt-get update` our system knows the list of available updates. `apt-get upgrade` installs these new updates.

##Step 2: Add PPAs to Stay Updated

`.deb` files are the Ubuntu software package files. Whenever we perform `apt-get install` or `apt-get update`, package files are first downloaded and then the respective package is installed.

The default Ubuntu distro is not updated so often. They have a slightly older version of package repositories. Active developers often upload the latest version of software package repositories to Launchpad or similar sites. This enables us to install the latest version of this software directly through `apt-get`.

These software packages uploaded by individual developers or organizations are called as **Personal Package Archives**. We will use them to get the latest versions of Nginx and PHP.

>PPA (Personal Package Archive) is a repository collection of software source packages that can be installed as an apt-repository. It is maintained and hosted by Launchpad.

###Install software-properties-common

To avail the functionality of installing source packages from PPAs, we need to install `software-properties-common`:

```bash
apt-get install -y software-properties-common
```

###Add Nginx and PHP Repositories

```bash
apt-add-repository ppa:nginx/development -y
apt-add-repository ppa:ondrej/php -y
```

We install the Personal Package Archive of **Ondrej** to be able to install the latest versions of PHP. The `nginx/development` PPA contains security and issues patches to the latest stable version without any breaking changes. This makes it more *stable* and *current*.

###Update Package Lists

Now, we need to again update our package lists. This will fetch the list of repositories as mentioned in the above PPAs:

```bash
# Update Package Lists

apt-get update
```

##Step 3: Install Nginx and PHP

Next, we will install Nginx and PHP:

```bash
# Install Nginx & PHP-FPM

apt-get install -y --allow-unauthenticated nginx php7.0-fpm
```

`--allow-unauthenticated`: We are installing the latest versions through personal packages. These packages may not be signed by the developers. `apt-get` does not install the packages which are unsigned. `--allow-unauthenticated` allows the unsigned packages to be installed without any prompt. 

In below demonstrations, we can see that the latest versions of PHP and Nginx are installed:

![Nginx Version](https://virajkhatavkar.com/wp-content/uploads/2017/02/Nginx-Version.gif)

![PHP Version](https://virajkhatavkar.com/wp-content/uploads/2017/02/PHP-version.gif)

##Step 4: Install PHP Extensions

Our applications have many inherent needs with respect to its security and features. We use PHP frameworks or CMS to create web applications. These frameworks and CMS requires some PHP packages to be pre-installed in out VPS. Let us install the necessary PHP packages which are required by most of the frameworks and CMS out there:

```bash
# Install PHP Packages


apt-get install -y --force-yes php7.0-cli php7.0-dev \
php7.0-pgsql php7.0-sqlite3 php7.0-gd php7.0-curl \
php7.0-dev php7.0-intl php7.0-readline php7.0-imap \
php7.0-mysql php7.0-memcached php7.0-mcrypt php7.0-mbstring \
php7.0-xml php7.0-imagick php7.0-zip php7.0-bcmath php7.0-soap
```

##Step 5 (Optional): phpinfo() Page

Next, we will set up a `phpinfo()` page to check if Nginx is serving php files properly. This is an optional step. But, it is always better to be sure that everything is setup properly. 

By default, Nginx is not configured to serve PHP files. Let us configure the default site to serve PHP files:

![Serve PHP on default site](https://virajkhatavkar.com/wp-content/uploads/2017/02/serve-php-on-default-site.gif)

Next, we will restart the Nginx server:

```bash
service nginx restart
```

Now create an `index.php` file in `/var/www/html`:

```php
<?php

echo phpinfo();
```

![phpinfo](https://virajkhatavkar.com/wp-content/uploads/2017/02/phpinfo.gif)

##Conclusion

We have successfully installed Nginx and PHP-FPM on our brand new Ubuntu server. Tomorrow, we will see how we can secure our server, block bot attacks, configure firewall and schedule automatic security updates.