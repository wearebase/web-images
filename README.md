# Caveat: This is not a Craft Plugin
Because Composer doesn't let you install to a folder of your choice, at the current time we are hijacking the [composer/installers](https://github.com/composer/installers) package for 'Craft Plugins'. This means you must add an item to your composer.json to place it in a folder of your choice.

This will only negatively affect users who use Craft CMS. And as this is a package designed for a Twig project or [Timber for Wordpress](https://github.com/jarednova/timber), that shouldn't be a problem... right?

# Requirements
* [Timber for Wordpress](https://github.com/jarednova/timber) or other Twig project
* Sass (SCSS)


# Install
`composer require wearebase/web-images`

# Configure where you want the package to go
In your `composer.json`, add the following:

```
"extra": {
  "installer-paths": {
    "wp-content/themes/timber/packages/{$name}": ["type:craft-plugin"]
  }
}
```

This plugin identifies itself as a `craft-plugin`. The addition above will send all `craft-plugin` to a directory you specify. I recommend to send it to your Timber theme inside of wp-content, so you can easily access files with Sass, Twig, etc. Then .gitignore that packages folder.

If you want to move *just* this package to a folder of your choice, use this:

```
"extra": {
  "installer-paths": {
    "wp-content/themes/timber/packages/{$name}": ["wearebase/web-images"]
  }
}
```

If you do not specify an installer-path in `composer.json` this utility will install to `craft/plugins/{$name}` as that is the default from `composer/installer` package.

# What's included

* [blazy](http://dinbror.dk/blog/blazy/), created by Bjørn Klinggaard with an MIT License.
    * [Docs here](http://dinbror.dk/blog/blazy/).
    * (These files are held in this repo for simplicity as bLazy is not available on Composer and pulling it from npm or Bower is an added dependency.)
    * I have also included a copy of the documentation as it is held on a private website which may be unavailable.
* lazyLoad markup and scss to allow for full size backgrounds, images, and TimberImages
* background-size.min.htc to allow for backwards compatibility of `background-size: cover;`

# Usage

## Enabling Views
First of all, you must add the views folder to your Twig template load path. You can either specify the views folder within this package or specify your whole third-party directory, which will affect the way you access your views. If you're using multiple Base packages, then the latter may be preferable.

### Timber
[Add the loadpath to your `functions.php`](https://github.com/jarednova/timber/wiki/Configure-template-locations#changing-the-default-folder-for-twig-files). You must re-add the default Theme view folder in here too as it may override the Timber view folder.

```
Timber::$dirname = array(
  'views',
  'packages/web-front-end-images/views'
);
```

### Enabling Macros
In your layout file, or in individual macros or components:

`{% import 'macros/base_images.twig' as base_image %}`

With the path to the file and the variable you'd like to access it on.

## Enabling Sass
* Add the Sass to your build path. If you're using Compass, add this to your config.rb:

```
 add_import_path "wp-content/themes/timber/packages"
```

* `@import "autoload"`. to get everything you need.
* In your project variables, you can optionally add the following to override default values:
    * `$base-lazy-transition-speed` (500ms)

## Enabling JS
* Add the JS file(s) to your page.

## Enabling background-size.
* Move the `background-size.min.htc` file to your webroot. A reference to a htc file *must* be absolute, so it is easiest to drop it in the web root directory.

## LazyLoading
* Activate bLazy with `var bLazy = new Blazy();` on your page.
* Revalidate bLazy (on a resize, for example) with `bLazy.revalidate();`.
* Use `b-lazy` as a class on any div or img with a `data-src` to lazyLoad it. Preferably use the macros included in this package to enable the following:
    * Lazy images, with optional support for retina
    * Lazy TimberImages, with optional support for retina
    * Lazy background, with optional support for retina
* All of the included functions supply a `<noscript>` version for non-JS users.

### Macros
#### {{ base_image.lazy(images, alt, classes, width, height) }}
Bog standard lazy img. Give it the following vars:

* Array of 1x and 2x image URLs, OR a string of 1x image
* Alt: String to be used as the alt tag, optional (if empty, uses an empty alt tag, which [forces Screen Readers to not read the image at all](http://osric.com/chris/accidental-developer/2012/01/when-should-alt-text-be-blank/))
* Classes: Any additional classes as a string to add to the image, optional
* Width: Int, optional
* Height: Int, optional

*There is also a standard 'img' macro if you need to switch a lazy image to a normal image tag.*

#### {{ base_image.TimberImages(ids, alt, classes, sizes) }}
This is a preferable function if you know the ID of an image.

This is great for most scenarios, but a specific scenario might be that you have a 'banner' image size set up for your Wordpress images. You can then request the Featured Image of a post in the size 'banner'. Or if you're feeling fancy, add a 'banner-2x' and request ['banner', 'banner-2x'] to get a retina size of that banner.

Pass this the following vars:

* id of image (recommended) OR an array of ids for when you want to use a different 2x image (not recommended)
* Alt: String to be used as the alt tag. Optional (if empty, uses the 'caption' of an image set in Wordpress)
* Classes: Any additional classes to add to the image as a string. Optional
* Sizes: string or array of string sizes. If empty, uses 'full'. Optional

#### {{ base_image.background(images, classes) }}
* Use the included background macro to easily create a full size lazyloaded background. By default these will fade in, but you can disable that by adding `b-no-fade` class to backgrounds when triggering the macro.
* A parent of a full size background **must be position:relative**, or use the `bg-parent` class.

Pass this the following vars:

* URL String of image to use as a background
* Any additional classes you want to use, as a string
