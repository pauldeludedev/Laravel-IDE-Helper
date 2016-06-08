## Laravel 5 IDE Helper Generator
[![Latest Stable Version](https://poser.pugx.org/barryvdh/laravel-ide-helper/version.png)](https://packagist.org/packages/barryvdh/laravel-ide-helper) [![Total Downloads](https://poser.pugx.org/barryvdh/laravel-ide-helper/d/total.png)](https://packagist.org/packages/barryvdh/laravel-ide-helper)

[![Latest Version on Packagist][ico-version]][link-packagist]
[![Software License][ico-license]](LICENSE.md)
[![Build Status][ico-travis]][link-travis]
[![Total Downloads][ico-downloads]][link-downloads]

__For Laravel 4.x, check [version 1.11](https://github.com/barryvdh/laravel-ide-helper/tree/1.11)__

### Complete phpDocs, directly from the source

_Checkout [this Laracasts video](https://laracasts.com/series/how-to-be-awesome-in-phpstorm/episodes/15) for a quick introduction/explanation!_

 * [`php artisan ide-helper:generate` - phpDoc generation for Laravel Facades ](#automatic-phpdoc-generation-for-laravel-facades)
 * [`php artisan ide-helper:models` - phpDocs for models](#automatic-phpdocs-for-models)
 * [`php artisan ide-helper:meta` - PhpStorm Meta file](#phpstorm-meta-for-container-instances)

This package generates a file that your IDE understands, so it can provide accurate autocompletion. Generation is done based on the files in your project, so they are always up-to-date.
If you don't want to generate it, you can add a pre-generated file to the root folder of your Laravel project (but this isn't as up-to-date as self generated files).

* Generated version for L5: https://gist.github.com/barryvdh/5227822
* Generated version for Lumen: https://gist.github.com/barryvdh/be17164b0ad51f832f20
* Generated Phpstorm Meta file: https://gist.github.com/barryvdh/bb6ffc5d11e0a75dba67

Note: You do need CodeIntel for Sublime Text: https://github.com/SublimeCodeIntel/SublimeCodeIntel

### Install

Require this package with composer using the following command:

```bash
composer require barryvdh/laravel-ide-helper
```

After updating composer, add the service provider to the `providers` array in `config/app.php`

```php
Barryvdh\LaravelIdeHelper\IdeHelperServiceProvider::class,
```

### Automatic phpDoc generation for Laravel Facades


You can now re-generate the docs yourself (for future updates)

```bash
php artisan ide-helper:generate
```

Note: `bootstrap/compiled.php` has to be cleared first, so run `php artisan clear-compiled` before generating (and `php artisan optimize` after).

You can configure your composer.json to do this after each commit:

```js
"scripts":{
    "post-update-cmd": [
        "php artisan clear-compiled",
        "php artisan ide-helper:generate",
        "php artisan optimize"
    ]
},
```

You can also publish the config file to change implementations (ie. interface to specific class) or set defaults for `--helpers` or `--sublime`.

```bash
php artisan vendor:publish --provider="Barryvdh\LaravelIdeHelper\IdeHelperServiceProvider" --tag=config
```

The generator tries to identify the real class, but if it cannot be found, you can define it in the config file.

Some classes need a working database connection. If you do not have a default working connection, some facades will not be included.
You can use an in-memory SQLite driver, using the -M option.

You can choose to include helper files. This is not enabled by default, but you can override it with the `--helpers (-H)` option.
The `Illuminate/Support/helpers.php` is already set-up, but you can add/remove your own files in the config file.

### Automatic phpDocs for models

> You need to require `doctrine/dbal: ~2.3` in your own composer.json to get database columns.


```bash
composer require doctrine/dbal
```

If you don't want to write your properties yourself, you can use the command `php artisan ide-helper:models` to generate
phpDocs, based on table columns, relations and getters/setters. You can write the comments directly to your Model file, using the `--write (-W)` option. By default, you are asked to overwrite or write to a separate file (`_ide_helper_models.php`). You can force No with `--nowrite (-N)`.
Please make sure to backup your models, before writing the info.
It should keep the existing comments and only append new properties/methods. The existing phpdoc is replaced, or added if not found.
With the `--reset (-R)` option, the existing phpdocs are ignored, and only the newly found columns/relations are saved as phpdocs.

```bash
php artisan ide-helper:models Post
```

```php
/**
 * An Eloquent Model: 'Post'
 *
 * @property integer $id
 * @property integer $author_id
 * @property string $title
 * @property string $text
 * @property \Carbon\Carbon $created_at
 * @property \Carbon\Carbon $updated_at
 * @property-read \User $author
 * @property-read \Illuminate\Database\Eloquent\Collection|\Comment[] $comments
 */
```

By default, models in `app/models` are scanned. The optional argument tells what models to use (also outside app/models).

```bash
php artisan ide-helper:models Post User
```

You can also scan a different directory, using the `--dir` option (relative from the base path):

```bash
php artisan ide-helper:models --dir="path/to/models" --dir="app/src/Model"
```

You can publish the config file (`php artisan vendor:publish`) and set the default directories.

Models can be ignored using the `--ignore (-I)` option

```bash
php artisan ide-helper:models --ignore="Post,User"
```

Note: With namespaces, wrap your model name in " signs: `php artisan ide-helper:models "API\User"`, or escape the slashes (`Api\\User`)

## PhpStorm Meta for Container instances

It's possible to generate a PhpStorm meta file, to [add support for factory design pattern](https://confluence.jetbrains.com/display/PhpStorm/PhpStorm+Advanced+Metadata). For Laravel, this means we can make PhpStorm understand what kind of object we are resolving from the IoC Container. For example, `events` will return an `Illuminate\Events\Dispatcher` object, so with the meta file you can call `app('events')` and it will autocomplete the Dispatcher methods.

``` bash
php artisan ide-helper:meta
```

```php
app('events')->fire();
\App::make('events')->fire();

/** @var \Illuminate\Foundation\Application $app */
$app->make('events')->fire();

// When the key is not found, it uses the argument as class name
app('App\SomeClass');
```

Pre-generated example: https://gist.github.com/barryvdh/bb6ffc5d11e0a75dba67

> Note: You might need to restart PhpStorm and make sure `.phpstorm.meta.php` is indexed.
> Note: When you receive a FatalException about a class that is not found, check your config (for example, remove S3 as cloud driver when you don't have S3 configured. Remove Redis ServiceProvider when you don't use it).

### License

The Laravel IDE Helper Generator is open-sourced software licensed under the [MIT license](http://opensource.org/licenses/MIT)


[ico-version]: https://img.shields.io/packagist/v/barryvdh/laravel-ide-helper.svg?style=flat-square
[ico-license]: https://img.shields.io/badge/license-MIT-brightgreen.svg?style=flat-square
[ico-travis]: https://img.shields.io/travis/barryvdh/laravel-ide-helper/master.svg?style=flat-square
[ico-scrutinizer]: https://img.shields.io/scrutinizer/coverage/g/barryvdh/laravel-ide-helper.svg?style=flat-square
[ico-code-quality]: https://img.shields.io/scrutinizer/g/barryvdh/laravel-ide-helper.svg?style=flat-square
[ico-downloads]: https://img.shields.io/packagist/dt/barryvdh/laravel-ide-helper.svg?style=flat-square

[link-packagist]: https://packagist.org/packages/barryvdh/laravel-ide-helper
[link-travis]: https://travis-ci.org/barryvdh/laravel-ide-helper
[link-scrutinizer]: https://scrutinizer-ci.com/g/barryvdh/laravel-ide-helper/code-structure
[link-code-quality]: https://scrutinizer-ci.com/g/barryvdh/laravel-ide-helper
[link-downloads]: https://packagist.org/packages/barryvdh/laravel-ide-helper
[link-author]: https://github.com/barryvdh
[link-contributors]: ../../contributors
