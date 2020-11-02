---
layout: page
title: Working with forms
permalink: /tutorial/symfony/07-working-with-forms
---

[_Symfony Forms docs_](https://symfony.com/doc/current/forms.html)

Forms are essential part of the system in order to validate group of user inputs. Even when working with just backend
API system, we shall create forms as they have validation rules and ensure the sanity of the data.

In Symfony, to create forms we use
a [Form Builder](https://symfony.com/doc/current/components/form.html#creating-a-simple-form), a tool to easily create
and set forms.

### Building a simple form

Based on our previous exercise, we would like to change the _Regenerate_ link to _Edit_ where we will be
able to set our new value by ourselves. To do that, first we create a new form class, which can be done via `make:form`
command:

```bash
symfony console make:form

 The name of the form class (e.g. BraveJellybeanType):
 > RandomNumberType

 The name of Entity or fully qualified model class name that the new form will be bound to (empty for none):
 > RandomNumber

 created: src/Form/RandomNumberType.php


  Success!


 Next: Add fields to your form and start using it.
 Find the documentation at https://symfony.com/doc/current/forms.html
```

In Symfony, forms represent [Form Types](https://symfony.com/doc/current/forms.html#form-types) which can represent
a single field (which is Form Type) as well as group of fields (which is in Symfony also as Form Type). So we create
a `RandomNumberType` which is based on `RandomNumber` entity as specified in creation wizard.
Now, let's have a look at newly created `RandomNumberType`.

The newly created class is extending of `AbstractType` which is not necessary to extend of, more importantly is that our
form class should implement `FormTypeInterface` interface. However, `AbstractType` has some helpful methods we will use
later.

There are two generated methods - `buildForm()` and `configureOptions()`. The second one is already prepared for us. By
setting the default configuration with `data_class` configuration key we achieve that this form is bound to `RandomNumber`
entity and object of that entity will have it's data automatically mapped to this form.

The first method `buildForm()` has builder passed on which we build our fields - types. We see it adds to fields `max`
and `value`. These fields were automatically detected from our `RandomNumber` entity during generation process. For now
we see it's pretty simple and maybe too simple - we don't see how does it know we want fields `max` and `value`
as integers. The full signature of `add()` method is as follows:

```php
public function add($child, string $type = null, array $options = []);
```

We omitted parameters `$type` and `$options`. For `$type`, Symfony implements
[Form Type Guessing](https://symfony.com/doc/current/forms.html#form-type-guessing) which guesses the form type based
on multiple factors, like the field name or whether the `data_class` was provided. If so, it takes the field name
and tries to match it with the property of the Entity. If successful, it reads the property type and finds the most
suitable field type for that. In our case, it will be the `number` type.

### Creating and handling forms

For now, we would like to show that form on our page. Assume that we want to change the logic behind
`/random/edit/{id}`. Now when user opens such route, instead of generating a new random value, we will display a form
which will have both `max` and `value` fields free to edit. Yes, you may think we need to create a template now, which
is correct. But, let's start with changes in controller first:

```php
<?php
// ...
class RandomController extends AbstractController
{
    // ...
    /**
     * @Route("/edit/{id}", name="random_edit", requirements={"id": "\d+"})
     */
    public function edit(Request $request, int $id)
    {
        // obtain the number from the database
        $number = $this->getDoctrine()
            ->getRepository(RandomNumber::class)
            ->find($id);

        // throw an 404 exception in case the number doesn't exist under specified id
        if (!$number) {
            throw $this->createNotFoundException('The random number under id ' . $id . ' is not in our database!');
        }

        $form = $this->createForm(RandomNumberType::class, $number);
        $form->handleRequest($request);

        if ($form->isSubmitted() && $form->isValid()) {
            // obtain Entity Manager instance
            $entityManager = $this->getDoctrine()->getManager();

            // execute all the changes in transaction
            $entityManager->flush();

            // throw flash message
            $this->addFlash('success', 'Your lucky number was successfully updated.');

            // redirect back to the list of lucky numbers
            return $this->redirectToRoute('random_list');
        }

        return $this->render('random/edit.html.twig', [
            'number' => $number,
            'form' => $form->createView()
        ]);
    }
}
```

Let's see what was done here - the first change you may notice is the new `Request $request` parameter. This parameter
is automatically injected by Symfony and it represents a HTTP Request instance. It is essential for the form to load data
submitted by the user via HTTP Request.

Then we created a form by calling `$this->createForm()`, this method is provided by `AbstractController` and accepts two
parameters: class of the Form Type and object to pre-fill form data. The second parameter is not mandatory, we can leave
it blank in case like a form for creating a new Entity, where we mostly leave the form empty by default.

The next statement `$form->handleRequest($request);` is necessary. Without it, the form will never know whether we
submitted it or not.

Right after, we create a condition to check whether the form was submitted and is valid. If so, we don't need to update
`$number` object manually, it's updated explicitly by the form itself. Thanks to that, we only need to flush the changes
to the database.

The last change is that we are trying to render a new `edit.html.twig` template where we pass two variables:
`number` and `form`. The `number` variable represents the `RandomNumber` object and the `form` variable stands for the
view of the form which will be handled by the template.

Now, let's create a new template `edit.html.twig` based on variables passed by controller:

```twig
{% raw %}{% extends 'base.html.twig' %}

{% block title %}Edit Random Number{% endblock %}

{% block main %}
    <h1>Edit Random Number #{{ number.id }}</h1>

    {{ form(form) }}
{% endblock %}

{% block sidebar %}
    {{ parent() }}

    {{ show_source_code(_self) }}
{% endblock %}{% endraw %}
```

The only new thing here is calling for `form()` function. This function handles everything regarding the form and its
rendering.

You may change the text in `list.html.twig` from `Regenerate` to `Edit`:

```twig
{% raw %}<a href="{{ path('random_edit', {'id': number.id}) }}">Edit</a></td>{% endraw %}
```

Let's try it. Click on the _Edit_ on some of the Random Numbers and you should see the two fields _Max_ and _Value_.
But something is missing there... Yes, submit button! All buttons are part of form as well, not just fields. So, to add
a new button, we need to add it to our `RandomNumberType` builder:

```php
<?php
// ...
class RandomNumberType extends AbstractType
{
    public function buildForm(FormBuilderInterface $builder, array $options)
    {
        $builder
            ->add('max')
            ->add('value')
            ->add('save', SubmitType::class) // we need to set SubmitType here
        ;
    }
    // ...
}
```
For the submit button, the `SubmitType` must be provided as there is no such field `save` in `RandomNumber` entity
and Type Guesser wouldn't know what to do with this field. All supported Form Types can be found
[here](https://symfony.com/doc/current/reference/forms/types.html).

Reload the form again, now you should be able to set and submit values all by yourself.

### Validating the form

Some validations are set by default, like here, for `number` types the it's required to fill the value and the value must
be the number. If we would have some `string` field types, they are optional by default and they can contain every value
of every length.

We can define validation rules / constraints on Entity level or directly in the form as well. In case we want to have
basic constraint for Entity, we use `@Assert` annotation. More about it can be read
[here](https://symfony.com/doc/current/forms.html#validating-forms). Then we don't need to do anything within the form,
all constraints will be processed automatically.

Let's assume we want something more complex - the value cannot be higher than maximum provided. So our validation rule
is dependent on another field and that's how we need to specify it. This rule can be applied on Entity level as it is
something common on each case we would like to validate `RandomNumber`. In case we want just some specific scenario
of validation which doesn't apply on Entity by default, then we add these constraints just to the form itself.

First thing we do is to add a usage of `Symfony\Component\Validator\Constraints` under `Assert` alias. This is a common
practice in Symfony as it's more readable in `@Assert\NotBlank()` format for property as `@Constraints\NotBlank()`.
Of course, it's optional but it's recommended to do so. Then we just add annotation
`@Assert\LessThanOrEqual(propertyPath="max")` on `$value` property where `propertyPath` is the path to property which we
are comparing to:

```php
<?php
// ...
use Symfony\Component\Validator\Constraints as Assert;
// ...
class RandomNumber
{
    // ...
    /**
     * @Assert\LessThanOrEqual(propertyPath="max")
     * @ORM\Column(type="integer")
     */
    private $value;
    // ...
}
```

And that's it. Now enter the value higher then maximum value and try save it and you should see the validation error
message below _Value_ field. Working great right?

Just for your information, if you would like to omit validation rule from Entity and have this constraint set only in
`RandomNumberType` form, it would look like following:

```php
<?php
// ...
use Symfony\Component\Validator\Constraints\LessThanOrEqual;

class RandomNumberType extends AbstractType
{
    public function buildForm(FormBuilderInterface $builder, array $options)
    {
        $builder
            ->add('max')
            ->add('value', null, [
                'constraints' => new LessThanOrEqual([
                    'propertyPath' => '[max].data'
                ])
            ])
            ->add('save', SubmitType::class)
        ;
    }

    // ...
}
```

Here we have a little more complicated path like it was on Entity level. It's because form has more properties
and to access our field properties we recognize them as array of objects where the value of that object is under `data`
property. More about property path syntax can be learned
[here](https://symfony.com/doc/current/components/property_access.html).

**Congratulations**! You have finished all 7 exercises. Now you have some really basic knowledge how Symfony looks alike
and where to find more information about this framework as this is not the end of our learning journey.

![May the force be with you](https://i.kym-cdn.com/photos/images/newsfeed/000/747/556/27a.jpg)
