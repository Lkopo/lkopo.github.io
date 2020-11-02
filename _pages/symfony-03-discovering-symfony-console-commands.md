---
layout: page
title: Discovering Symfony console commands
permalink: /tutorial/symfony/03-discovering-symfony-console-commands
---

Symfony console commands are helpful set of instructions in order to ease
development process such as creating new controllers, entities, database migration
or so. We already know some commands from previous exercises such as `make:migration`
or `doctrine:migrations:execute`.

In the previous exercise we used to write commands starting as `php bin/console xyz`.
This can be simplified as `symfony console xyz` using Symfony CLI. We will use this approach
for the rest of the tutorial. If you would like to stick with the previous format, feel free
to do so.

The whole list of commands you have currently registered in your
project can be viewed by the following command:

```bash
symfony console list
```

### Creating a custom controller, `RandomController`

This controller would represent simple number generator and display it
when requested.

Let's create a controller using symfony commands:

```
symfony console make:controller RandomController
```

We should see the message:

```
 created: src/Controller/RandomController.php
 created: templates/random/index.html.twig


  Success!


 Next: Open your new controller class and add some pages!
```

As we can see, this tool created two files for us with already some basic content.

Let's test it - open http://localhost/en/random in the URL if it works. If you can
see a _Hello RandomController_ page, it's working! Let's continue
to [exercise 04](/tutorial/symfony/04-working-with-controllers) to see how to
work with controllers.

PS: Don't worry. We will see more command usages in next exercises.
