<img src="docs/camya_TitleAndSlugField_github-header.jpg" />

# TitleWithSlugInput - Easy Permalink Slugs for the Filament Form Builder

This [FilamentPHP](https://filamentphp.com/docs/admin/installation) Form Builder package adds a form field to easily add and edit titles with slugs.

This plugin is inspired by the classic WordPress title & slug implementation.

```php
TitleWithSlugInput::make(
    titleField: 'title', // The name of the field in your model that stores the title.
    slugField: 'slug', // The name of the field in your model that will store the slug
),
```

**Features**

- The slug is automatically generated from the title if it has not already been manually updated.
- "View" link to visit the generated URL.
- Undo the edited slug.
- Fully configurable, see [all available parameters](#all-available-parameters).

Tall Stack: FilamentPHP is based on 
[Laravel](https://laravel.com/),
[Livewire](https://laravel-livewire.com/), 
[AlpineJS](https://alpinejs.dev/), 
and 
[TailwindCSS](https://tailwindcss.com/).

Watch **[&raquo; Demo Video &laquo;](https://www.youtube.com/watch?v=v-AxZv6M1xs)**

[![Video](docs/camya_TitleAndSlugField_v1.0.0_video_placeholder.png)](https://www.youtube.com/watch?v=v-AxZv6M1xs)

## Support us

You can support my work with [donations](https://www.camya.com).

## Installation

You can install the package via composer:

```bash
composer require camya/filament-title-with-slug
```

If needed, you can publish the config file with:

```bash
php artisan vendor:publish --tag="filament-title-with-slug-config"
```

If needed, you can publish the translation files with:

```bash
php artisan vendor:publish --tag="filament-title-with-slug-translations"
```

## Usage & Examples

- [**All available parameters**](#all-available-parameters)



- [Change model fields names](#change-model-fields-names)
- [Base path and title placeholder](#base-path-and-title-placeholder)
- [Title above text field & custom slug label](#title-above-text-field--custom-slug-label)
- [Add extra validation rules for title or slug](#add-extra-validation-rules-for-title-or-slug)
- [Generate route for "Visit" link](#generate-route-for-visit-link)
- [Custom slugifier](#custom-slugifier)
- [Add additional validation rules](#add-additional-validation-rules)
- [Custom error messages](#custom-error-messages)
- [Custom unique validation rules for title (and slug)](#custom-unique-validation-rules-for-title-and-slug)
  


### Change model fields names

The package assumes, that you model fields are named `title` and `slug`.

You can easily change them according to your needs.

```php
TitleWithSlugInput::make(
    titleField: 'title',
    slugField: 'slug',
),
```

The output looks like this:

<img src="docs/camya_TitleAndSlugField_v1.0.0_usage_case01-01.png" width="600" />
<img src="docs/camya_TitleAndSlugField_v1.0.0_usage_case01-02.png" width="600" />
<img src="docs/camya_TitleAndSlugField_v1.0.0_usage_case01-03.png" width="600" />

### Base labels and basePath

It's possible to change all labels on the fly. 

In this example, we also add the base path `/books/`.

```php
TitleWithSlugInput::make(
    basePath: '/book/',
    visitLinkLabel: 'Visit Book',
    titleLabel: 'Title',
    titlePlaceholder: 'Insert the title...',
    slugLabel: 'Link:',
),
```

The output looks like this:

<img src="docs/camya-filament-title-with-slug-docs-case02-labels-01.png" width="600" />
<img src="docs/camya-filament-title-with-slug-docs-case02-labels-02.png" width="600" />

### Title above text field & custom slug label

The package automatically inserts a placeholder for the title. If you want to display the regular caption above the text box instead, you can configure it.

You can also specify the caption for the slug.

```php
TitleWithSlugInput::make(
    titleLabel: 'Product title',
    titlePlaceholder: '',
    slugLabel: 'Slug:'
),
```

The output looks like this:

<img src="docs/camya_TitleAndSlugField_v1.0.0_usage_case03-01.png" width="600" />

### Add extra validation rules for title or slug

You can add additional validation rules by passing in the variables `titleRules` or `slugRules`. 

```php
TitleWithSlugInput::make(
    titleRules: [
        'required',
        'string',
        'min:3',
        'max:12',
    ],
)
```

You can also [customize the error messages](#custom-error-messages).

### Generate route for "Visit" link

This package displays a "view" link for persisted slugs. By default, it simply concatenates the strings host + path + slug.

If you want to use a "route()" instead, you can configure it as shown below.

```php
TitleWithSlugInput::make(
    visitLinkRoute: fn(?Model $record) => $record?->slug
        ? route('post.show', ['slug' => $record->slug])
        : null,
),
```

### Custom slugifier

This package uses Laravel's slugifier, `Str::slug()`, but it is possible to replace it with one of your own.

The following generates a slug with only the characters a-z and validates them with a regex.

```php
TitleWithSlugInput::make(
    slugSlugifier: fn($string) => preg_replace( '/[^a-z]/', '', $string),
    slugRuleRegex: '/^[a-z]*$/',
),
```

Note: You can customize the validation error, see [Custom error messages](#custom-error-messages).

### Add additional validation rules

By default, this package applies the `['required','string']` validation rules to both title and slug.

In addition, a unique validation rule is applied to the slug field automatically. (See note below)

```php
TitleWithSlugInput::make(
    titleRules: [
        'required',
        'string',
    ],
),
```

### Custom unique validation rules for title (and slug)

Unique validation rules can be modified only by using the parameters `titleRuleUniqueParameters` and the `slugRuleUniqueParameters` counterpart.

This is needed in order to set  Filament's "ignorable" parameter correctly.

```php
TitleWithSlugInput::make(
    titleRuleUniqueParameters: [
        'callback' => fn(Unique $rule) => $rule->where('is_published', 1),
        'ignorable' => fn(?Model $record) => $record,
    ],
),
```

This array is inserted into the input field's `->unique(...[$slugRuleUniqueParameters])` method.

Read Filament's documentation for the [Unique](https://filamentphp.com/docs/2.x/forms/validation#unique) method.

Available array keys:

```php 
'ignorable' (Model | Closure)
'callback' (?Closure)
'ignoreRecord' (bool)
'table' (string | Closure | null)  
'column' (string | Closure | null) 
```


### Custom error messages

You can customize the error messages in your EditModel and CreateModel filament resources by adding the $messages member variable.

```php
protected $messages = [
  'data.slug.regex' => 'Invalid Slug. Use only chars (a-z), numbers (0-9), and the dash (-).',
];
```

If you 

```php
TitleWithSlugInput::make(
    titleRules: [
        'required',
        'string',
        'min:3',
        'max:12',
    ],
)
```


### All available parameters

You can call TitleWithSlugInput without parameters, and it will work and use its default values.

In order to set parameters, you use [PHP8's Named Arguments](https://laravel-news.com/modern-php-features-explained) syntax.

```php
TitleWithSlugInput::make(
    titleField: 'title',
    slugField: 'slug',
);
```

Below is an example with some defaults overridden.

```php
TitleWithSlugInput::make(

    titleField: 'title',
    slugField: 'slug',

    // Url
    basePath: '/blog/',
    baseHost: 'https://www.camya.com',
    showHost: true,
    visitLinkLabel: 'View',
    visitLinkRoute: fn(?Model $record) => $record?->slug
        ? route('post.show', ['slug' => $record->slug])
        : null,

    // Title
    titleLabel: 'The Title',
    titlePlaceholder: 'Post Title',
    titleClass: '',
    titleRules: [
        'required',
        'string',
    ],
    titleRuleUniqueParameters: [
        'callback' => fn(Unique $rule) => $rule->where('is_published', 1),
        'ignorable' => fn(?Model $record) => $record,
    ],
    titleReadonly: fn($context, Closure $get) => $context === 'edit' && $get('is_published'),

    // Slug
    slugLabel: 'The Slug: ',
    slugRules: [
        'required',
        'string',
    ],
    slugRuleUniqueParameters: [
        'callback' => fn(Unique $rule) => $rule->where('is_published', 1),
        'ignorable' => fn(?Model $record) => $record,
    ],
    slugReadonly: fn($context, Closure $get) => $context === 'edit' && $get('is_published'),
    slugSlugifier: fn($string) => Str::slug($string),
    slugRuleRegex: '/^[a-z0-9\-\_]*$/',

)->columnSpan('full'),
```

## Changelog

Please see [CHANGELOG](CHANGELOG.md) for more information on what has changed recently.

## Security Vulnerabilities

Please review [our security policy](../../security/policy) on how to report security vulnerabilities.

## Credits

- [camya - Andreas Scheibel](https://github.com/camya)

This package was inspired and based on the filament-addons package by [awcodes](https://github.com/awcodes/filament-addons) .

## License

The MIT License (MIT). Please see [License File](LICENSE.md) for more information.
