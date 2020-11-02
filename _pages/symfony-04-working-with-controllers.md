---
layout: page
title: Working with controllers
permalink: /tutorial/symfony/04-working-with-controllers
---

[_Symfony Controller docs_](https://symfony.com/doc/current/controller.html)

Have a look at `RandomController` class which we generated in previous exercise,
we can see it's extending `AbstractController` which is not necessary to have a working controller.
This abstract class provides additional helpful methods mostly needed if you are developing
a standalone Symfony web-app - rendering views, redirecting or obtaining user's active session.

The first question is why we are accessing the page via `/en/random` path and not directly via
`/random` if `@Route` is defined as `/random` only? Routes are inherited, so you can define a `@Route`
on class level and each method inherits the basic route plus includes your method's route, so the final
route could be `/random/generate`. Going back to the question, the first route `/en` is defined in
`config/routes/annotations.yaml`:

```yaml
controllers:
    resource: '../../src/Controller/'
    type: annotation
    prefix: /{_locale}
    requirements:
        _locale: '%app_locales%'
    defaults:
        _locale: '%locale%'
```

`type: annotation` says the routes for controllers can be defined via annotation `@Route`, which is
also based on Symfony documentation a recommended way how to define routes at all. The part we look for
is in `prefix` configuration key. It says the routing starts with `{_locale}` which is a predefined
[special parameter](https://symfony.com/doc/current/routing.html#special-parameters).
The default `%locale%` and available locales `%app_locales%` are set in `config/services.yaml` file.
More about Symfony [routing](https://symfony.com/doc/current/routing.html)
and [translations](https://symfony.com/doc/current/translation.html) can be found in their docs.

### Creating a new endpoint/controller action

Image we want to show the user the new generated random number every time the user accesses the
`random/generate` page. To start, we will create a new method/endpoint inside `RandomController`:

```php
<?php
// ...
use Symfony\Component\HttpFoundation\Response;

class RandomController extends AbstractController
{
    // ...

    /**
     * @Route("/random/generate", name="random_generate")
     */
    public function generate()
    {
        $number = random_int(0, 100);
    
        return new Response(
            'Your number is ' . $number
        );
    }
}
```

Try it: [http://localhost/en/random/generate](http://localhost/en/random/generate).

This is a pure example where this method will work without need of `AbstractController` helper.
Each route has its `name` property as later in application, when you would like to make a reference
to this route, instead of its path (which may change later due to development changes) the name is used.

As we talked about routing inheritance, we can see we have both methods `index()` and `generate()`
starting with `/random`. To reduce redundancy, we can set `@Route` on class level and update the inner
routes as following:

```php
/**
 * @Route("/random")
 */
class RandomController extends AbstractController
{
    /**
     * @Route("", name="random")
     */
    public function index()
    {
        // ...
    }

    /**
     * @Route("/generate", name="random_generate")
     */
    public function generate()
    {
        // ...
    }
}
```

Note: you can set multiple routes per method if needed. Take a look at `BlogController#index` method.

Routes can obtain custom parameters as well. Assume we would like to have a user pick up a random value
between 0-maximum where maximum can be specified by user.

### Routes with parameters

To simply define whether the route contains parameters or not, we use brackets `{x}` where `x` is paremeter name.
We had already chance to see it for `{_locale}` parameter supported by Symfony by default.
Let's update our `generate()` method:

```php
<?php
// ...
class RandomController extends AbstractController
{
    // ...
    /**
     * @Route("/generate/{max}", name="random_generate")
     */
    public function generate(int $max)
    {
        $number = random_int(0, $max);

        return new Response(
            'Your number is ' . $number
        );
    }
}
```

That's it. Try it by yourself: [http://localhost/en/random/generate/12](http://localhost/en/random/generate/12).
Accesing this URL should show the random number between 0-12.

Now let's try to pass something non-numeric value:
[http://localhost/en/random/generate/abv](http://localhost/en/random/generate/abc).

Calling this URL will raise an PHP error (500) as we are trying to pass non-numeric value to `int $max`. Not handling
parameters validation can lead to vulnerability of the system itself. We can put requirements, if needed as next
parameter in `@Route` annotation:

```
@Route("/generate/{max}", name="random_generate", requirements={"max": "\d+"})
```

Now calling the same URL without number input will lead to 404 error as there is no such
defined route for `/en/random/generate/abv`. We can also specify default value in order user
didn't set maximum:

```
@Route("/generate/{max}", name="random_generate", requirements={"max": "\d+"}, defaults={"max": 100})
```

Now we are able to access [http://localhost/en/random/generate](http://localhost/en/random/generate)
as well as the URL with maximum number specified.

Great! Well done, again. The following [exercise 05](/tutorial/symfony/05-working-with-templates) will be focused
on displaying our random numbers in application styled layout using Twig templates instead of blank white page. Yay!
