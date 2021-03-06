# Requirements
* [Timber for Wordpress](https://github.com/jarednova/timber) or other Twig project
* Sass (SCSS)


# Install
`composer require wearebase/web-images`

# Configure where you want the package to go
If you want to install somewhere other than `vendor`, in your `composer.json` add the following:

```
"extra": {
  "installer-paths": {
    "wp-content/themes/timber/packages/{$name}": ["wearebase/web-images"]
  }
}
```

# What's included

* [blazy](http://dinbror.dk/blog/blazy/), created by Bjørn Klinggaard with an MIT License.
    * [Docs here](http://dinbror.dk/blog/blazy/).
    * (These files are held in this repo for simplicity as bLazy is not available on Composer and pulling it from npm or Bower is an added dependency.)
    * I have also included a copy of the documentation as it is held on a private website which may be unavailable.
* lazyLoad markup and scss to allow for full size backgrounds, images, and TimberImages
* background-size.min.htc to allow for backwards compatibility of `background-size: cover;`

# Usage

## Enabling Views
First of all, you must add the views folder to your Twig template load path.

If you're not using Timber, then simply use `lazyImages.twig` as a universal Twig file.

### Timber
[Add the loadpath to your `functions.php`](https://github.com/jarednova/timber/wiki/Configure-template-locations#changing-the-default-folder-for-twig-files). You must re-add the default Theme view folder in here too as it may override the Timber view folder.

If you're using Timber, then use `timberImages.twig`.

### Enabling Macros
In your layout file, or in individual macros or components:

`{% import 'lazyImages.twig' as base_image %}`

or if you're using Timber,

`{% import 'timberImages.twig' as base_image %}`

## Enabling Sass
* Add the Sass to your build path. If you're using Compass, add this to your config.rb:

```
 add_import_path "vendor/wearebase/web-images/scss"
```

* `@import "autoload"`. to get everything you need.
* In your project variables, you can optionally add the following to override default values:
    * `$base-lazy-transition-speed` (500ms)

## Enabling JS
* Add the JS file(s) to your page.

## Enabling background-size.
Background-size is now disabled by default. To use this, drop the `background-size.min.htc` file to your webroot.

You'll also need to add CSS rules to your background images in your own code to use it.

A reference to a htc file *must* be absolute.

## LazyLoading
* Activate bLazy with `var bLazy = new Blazy();` on your page. Trigger it inside `$(window).load()` for the best results.
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
