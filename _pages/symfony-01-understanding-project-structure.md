---
layout: page
title: Understanding project structure
permalink: /tutorial/symfony/01-understanding-project-structure
---

```
/var/www/html/
├─ assets/        - assets to be compiled (SASS, ...)
├─ bin/
│  └─ console     - PHP console binary scripts for database tools, file generating, will be shown in exercise 03
├─ config/        - Symfony configuration files, security, routing, database, ...
├─ migrations/    - scripts for migrating database structure
├─ public/        - content of this folder should be visible to public
│  └─ build/      - built assets
│  └─ index.php   - all HTTP requests are redirected to this file, boots and run the application
├─ src/
│  └─ ...         - the core logic of the application is stored here, split into controllers, entities, forms, etc.
├─ templates/     - application templates in Twig format
├─ tests/         - PHPUnit tests
├─ translations/
├─ var/           - temporary files such as caches, logs or sessions
│  ├─ cache/      - cached translations, templates to improve performance
│  ├─ log/        - application logs per environment
│  └─ ...
├─ vendor/        - auto-generated folder contains all dependencies the application requires
├─ .env           - contains all required variables for current environment (dev, test, prod, ...)
├─ composer.json
├─ ...
└─ symfony.lock
```

TBD

Let's move to [excercise 02](./02-setting-db-to-mysql-and-migrating-database.md), to configure your application to use MySQL database service instead of local SQLite database file.
