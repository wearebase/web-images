# Install
In your `composer.json`, add a new section for repositories:

```
"repositories": [
  { "type": "vcs", "url": "GIT-URL" }
]
```

And add the url of this repository.

Then include it in your composer:

```
"require": {
  "wearebase/web-front-end-images" : "*"
}
```

And specify a version or minimum version.

As this repo is private on BitBucket, your development machine *and* your servers will need authorised keys on bitbucket to pull this repo.

# Configure where you want the package to go
In your `composer.json`, add the following:

```
"extra": {
  "installer-paths": {
    "src/components/{$name}": ["type:craft-plugin"]
  }
}
```

This plugin identifies itself as a `craft-plugin`. The addition above will send all `craft-plugin` to a directory you specify. I recommend to send it to your src directory in a third-party folder you can easily access with Sass, Twig, etc. If using Wordpress, send it to a theme/third-party directory and gitignore that folder.

If you do not specify this item in `composer.json` this utility will install to `craft/plugins/{$name}` as that is the default from `composer/installer` package.

# Enabling

## Enabling Views
First of all, you must add the views folder to your Twig template load path. You can either specify the views folder within this package or specify your whole third-party directory, which will affect the way you access your views. If you're using multiple Base packages, then the latter may be preferable.

### Timber
[Add the loadpath to your `functions.php`](https://github.com/jarednova/timber/wiki/Configure-template-locations). You may need to re-add the default Theme view folder in here too as it may override the Timber view folder.

```
/* functions.php */
Timber::$locations = array(
  '/package-install-dir',
  ABSPATH.'/wp-content/themes/timber/views'
);
```

### Enabling Macros
In your layout file, or in individual macros or components:

`{% import 'macros/base_images.twig' as base_image %}`

With the path to the file and the variable you'd like to access it on.

## Enabling Sass
* Add the Sass to your build path
* `@import "autoload"`. to get everything you need.
* In your project variables, you can optionally add the following to override default values:
    * `$base-lazy-transition-speed` (500ms)

## Enabling JS
* Add the JS file(s) to your page.

## Enabling background-size.
* Move the `background-size.min.htc` file to your webroot. A reference to a htc file *must* be absolute, so it is easiest to leave it just in the root.

# What's included

* blazy, created by Bjørn Klinggaard.
    * [Docs here](http://dinbror.dk/blog/blazy/).
    * These files are held in this repo for simplicity as bLazy is not available on Composer and pulling it from npm or Bower is an added dependency. 
    * I have also included a copy of the documentation as it is held on a private website which may be unavailable.
* lazyLoad markup and scss to allow for full size backgrounds, images, and TimberImages
* background-size.min.htc to allow for backwards compatibility of `background-size: cover;`

## LazyLoading
* Activate bLazy with `var bLazy = new Blazy();`.
* Revalidate bLazy (on a resize, for example) with `bLazy.revalidate();`.
* Use `b-lazy` as a class on any div or img with a `data-src` to lazyLoad it. Preferably use the macros included in this package to enable the following:
    * Lazy images, with optional support for retina
    * Lazy TimberImages, with optional support for retina
    * Lazy background (non-retina for this version)
* All of the included functions include a `<noscript>` version for non-JS users.

### lazy
Bog standard lazy img. Pass it 1x (and 2x optional, or false), plus alt, classes, and the rest and it'll handle everything for you. There is also a standard 'img' macro if you need to use that instead of Lazy, which uses the same arguments as Lazy.

### lazyTimberImage
* Because TimberImage pulls in the width and height of the image, it can be used effectively to set the dimensions of an `<img>` before replacing the img with a 2x TimberImage version.

### background
* Use the included background macro to easily create a full size lazyloaded background. By default these will fade in, but you can disable that by adding `b-no-fade` class to backgrounds when triggering the macro.
* A parent of a full size background must be relative, or use the `bg-parent` class.