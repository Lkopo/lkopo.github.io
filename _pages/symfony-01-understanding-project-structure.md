---
layout: page
title: Understanding project structure
permalink: /tutorial/symfony/01-understanding-project-structure
---

```
/var/www/html/
├─ assets/        - assets to be compiled (SASS, ...)
├─ bin/
│  └─ console     - PHP console binary script which aggregates other scripts for database tools, file generating... will be shown in exercise 03
├─ config/        - Symfony configuration files, security, routing, database, ...
├─ migrations/    - generated scripts for migrating database structure
├─ public/        - content of this folder should be visible to public
│  └─ build/      - built assets
│  └─ index.php   - all HTTP requests are redirected to this file, boots and run the application
├─ src/
│  └─ ...         - the core logic of the application is stored here, split into controllers, entities, forms, etc.
├─ templates/     - application templates in Twig format
├─ tests/         - PHPUnit tests
├─ translations/  - translation files
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

During these exercises we will mostly touch the content only of `src` and `templates` folder. 

Let's move to [excercise 02](/tutorial/symfony/02-setting-db-to-mysql-and-migrating-database), to configure your application to use MySQL database service instead of local SQLite database file.
