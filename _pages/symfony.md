---
layout: page
title: Symfony framework tutorial with demo app
permalink: /tutorial/symfony
---

Symfony is one of the most famous PHP frameworks with huge community and it's growing fast - really fast. Some may know that Symfony framework has so many files for sometimes so little project, like [Symfony demo](https://github.com/symfony/demo). But is it hard to learn it? Back in the days PHP frameworks were not as popular as of today and so was the challenge to learn it. Documentation was poor, beginners were stucked at the beginning and lack of community was a reason to stick with "traditional" development of pages in PHP - fast development with low modularity and high cost in case of change during development process.

So back to the question whether is it hard to learn such huge framework as Symfony. No :). In the following exercises, you will see that the development in current frameworks is really simple and makes you focus only on one thing - application. You no longer need to bother with development of fundamental things which will be the core before you can actually start developing the application.

Before we start the exercises, we need to have our environment ready. You can decide whether you would like to have it within your Vagrant/Virtual box container, Docker or directly in your system. To make it work, you need to have following things installed:

- Apache2
- PHP 7.4
  - libapache2-mod-php7.4
  - php7.4-curl
  - php7.4-common
  - php7.4-intl
  - php7.4-sqlite
  - php7.4-gd
  - php-pear
  - php-imagick
  - php7.4-mysql
  - php7.4-ps
  - php7.4-xsl
  - libmcrypt-dev
  - php7.4-mbstring
  - php7.4-xdebug
- MySQL 8.x
- [Composer](https://getcomposer.org/)
- [Symfony CLI](https://symfony.com/download)

For Apache2 it's good to set `DocumentRoot` to `/var/www/html/public`. By default on Ubuntu systems the configuration is located in `/etc/apache2/sites-enabled/000-default.conf`:

```
<VirtualHost *:80>
  DocumentRoot "/var/www/html/public"
  ServerName localhost
  <Directory "/var/www/html/public">
    AllowOverride All
  </Directory>
</VirtualHost>
```

Now when all set, you should be able to execute `symfony` commands. Let's simple check whether we have all requirements met to run Symfony apps. We can do it by calling `check:requirements` command:

```bash
symfony check:requirements
```

You should see the message everything is ready. If you get the message something needs to be additionally installed, do so.

Now let's go to the project folder at `/var/www/html/` and create a Symfony demo app by executing:

```bash
symfony new --demo .
```

This will clone and install demo application for our tutorial exercises. Get ready! Once done, follow the first exercise 01 to understand project file structure.
