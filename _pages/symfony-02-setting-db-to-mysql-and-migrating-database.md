---
layout: page
title: Understanding project structure
permalink: /tutorial/symfony/01-setting-db-to-mysql-and-migrating-database
---

Current implementation uses own SQLite database which is located in
`data` folder as `database.sqlite`. We would like to change it to our installed
MySQL server.

### Changing connection details in `.env` file
 This file is used to define the value of environment variables and it’s explained in
 detail [here](https://symfony.com/doc/current/configuration.html#config-dot-env).
 
 We need to update `DATABASE_URL` key for MySQL connection URL, the pattern for database URL is described
 in [Doctrine documentation](https://www.doctrine-project.org/projects/doctrine-dbal/en/latest/reference/configuration.html#connecting-using-a-url):
 
```
DATABASE_URL=mysql://symfony:pass@127.0.0.1:3306/symfony?serverVersion=8
```

Save the file and reload the application. What did you get? An exception?

![good](https://i.imgflip.com/1300qk.jpg)

Read further, the exception says there are no tables.

### Migrating database tables
When we switch the database, the Symfony will not by itself auto-generate tables.
It relies on us to do the job. Symfony has powerful console scripts which will be explained
in another exercise, but for now we need to know to generate tables there
is a command:

```bash
php bin/console make:migration
```

You should see a message:

```
SUCCESS!

Next: Review the new migration “migrations/VersionXXXXXXXXXXXX.php” Then: Run the migration with php bin/console doctrine:migrations:migrate
```

As the message says, we need to run additional command to execute the migration script. The first command
just prepared the script for database migration based on entity structure definition, the second one will execute
the script. So let's proceed:

```bash
php bin/console doctrine:migrations:migrate
```

The script should finish successfully and if so, reload the page. Now it works, right?
But still, there are no data.

Migration scripts focus only on database structure, not the data itself. Data migrations
can be done via [Doctrine Fixtures Bundle](https://symfony.com/doc/master/bundles/DoctrineFixturesBundle/index.html).
This demo provides the fixtures to migrate the data and they are stored in `src/DataFixtures/AppFixtures.php`.

To load the fixtures, execute the following command:

```bash
php bin/console doctrine:fixtures:load
```

That's it. Data should be migrated and application ready to use again. The next
[exercise 03](/tutorial/symfony/03-discovering-symfony-console-commands) will show the console commands in more detail and how are they used
in order to improve development.
