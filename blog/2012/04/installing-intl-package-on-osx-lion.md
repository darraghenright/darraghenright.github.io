2012-04-29

# Installing Intl package on OSX Lion

When you deploy a new instance of Symfony2 [standard edition](https://github.com/symfony/symfony-standard) to your development environment one of the first things you should do is run `app/check.php` to perform the Symfony Requirements Check. It [checks](https://github.com/symfony/symfony-standard/blob/3818947792043bb8559b0943a0b30d6c91961bc3/app/check.php) that your environment meets all the mandatory requirements to run the framework.

In addition, it performs a bunch of optional checks to ascertain if certain recommended functionality and extensions are available, such as APC cache, PDO and iconv.

One of these recommended extensions is the Internationalization a.k.a. intl extension. If available it's used to provide extra locale-based functionality in several [form field types](http://symfony.com/doc/current/reference/forms/types.html) including money and date.

The [intl](http://pecl.php.net/package/intl) package is a bundled PHP extension since PHP 5.3. Of course this doesn't necessarily mean it shipped with OSX Lion. But if you have PECL and [Homebrew](https://brew.sh/) installed it's very easy. So let's install it!

## Step 1. Install ICU Libraries with Homebrew

First we'll need to install any dependencies. To quote the intl package description:

> The Internationalization extension exposes functionality of the ICU library to PHP.

[ICU Libraries](http://site.icu-project.org/) is our only dependency. If you have Homebrew installed you can use that; alternatively, you can download and compile from source.

Using homebrew:

```bash
brew update
brew search icu # returns 'icu4c'
```

Success. Install:

```bash
brew install icu4c
```

A couple of minutes to download and compile, and ICU is installed. PECL will prompt you for the path where the header files and stuff were installed; on my machine this was `/usr/local/Cellar/icu4c/4.8.1.1`.

## Step 2. Install Intl with PECL

Next, let's fire up PECL and run the following commands:

```bash
sudo pecl update-channels
sudo pecl install intl
```

Add the path to the ICU Library when prompted. Installation will take a short while to complete.

## Step 3. Register Extension in php.ini

Once that is done, you need to open your php.ini file:

```bash
sudo cp /etc/php.ini{,.bak} # first, backup php.ini
sudo vim /etc/php.ini
```

Add the following line at the bottom of the file (or wherever you prefer):

```
extension=intl.so
```

Save the file, test syntax and restart Apache:

```bash
sudo apachectl -t
sudo apache restart
```

## Step 4. Check

Finally, check that the module is registered:

```bash
php -m | grep intl # should return 'intl'
```

And you're done! Happy "internationalizationing" :)

<small>Article originally posted [here](http://darraghenright.tumblr.com/post/22027208929/installing-intl-package-on-osx-lion)</small>
