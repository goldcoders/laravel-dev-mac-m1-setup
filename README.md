# Setting Up PHP and Laravel on Mac M1

> M1 comes with php7.4 by default, laravel now used  PHP 8.0

## Upgrade with `shivammathur/homebrew-php`

```
brew tap shivammathur/php

brew install shivammathur/php/php@8.0

brew link --overwrite --force php@8.0
```

## To Uninstall

```
brew unlink php && brew link php
brew uninstall shivammathur/php/php@8.0
sudo rm -rf /opt/homebrew/Cellar/php/8.0.6
```



## Install Composer

```
brew install composer
```



- add to path on `.zprofile` so we can use `laravel` and `vale` commands and other composer commands

```
export PATH="$PATH":"$HOME/.composer/vendor/bin"
```

## Install Laravel and Laravel Valet

```
composer global require laravel/valet

composer global require laravel/installer
```

You can use `valet use` to switch between PHP versions:

```bash
valet use php@8.0
valet use php@7.4
```



## NOTE: PECL is install by Default with new PHP 8.0

-------------------------

>  This Set Up is OPTIONAL

> Install Pear

```
cd /tmp
curl -s -O https://pear.php.net/install-pear-nozlib.phar
sudo php install-pear-nozlib.phar -d /usr/local/lib/php -b /usr/local/bin
```

> If that fails

`autoconf` may be missing. To install it just use [Homebrew](http://brew.sh/). Once Homebrew is installed, installing `autoconf` is as simple as:

```
brew install autoconf
```

After `autoconf` is installed, try running the pear installer above again.

## Upgrade PEAR

```
sudo pear channel-update pear.php.net
sudo pecl channel-update pecl.php.net
sudo pear upgrade-all --ignore-errors
```

## Xcode Command Line Developer Tools

Optional , Assuming you already have xcode install on your mac m1

```
xcode-select --install
```

> END HERE THE OPTIONAL CONFIGURATIONS OF PECL AND PEAR

-----------------------------------------------------------

## Configuring PHP

The php.ini and php-fpm.ini file can be found in:
    /opt/homebrew/etc/php/8.0/

Open `/opt/homebrew/etc/php/8.0/php.ini` and add `.:/opt/homebrew/bin/pear` to `include_path

to know the `path` just type which pear

```
include_path = ".:/opt/homebrew/bin/pear"
```

## Extensions

Directory in which the loadable extensions (modules) reside 

```
extension_dir = "/opt/homebrew/lib/php/pecl/20200930"
```

## Note: when you cant download with pecl and causing issue 

try to own the whole folder

check pear config

```
pear config-get download_dir
```

Take ownership if there is an error on file permission

```
sudo chown -R $(whoami) /private/tmp/pear/download
```

## Imagick

```
brew install imagemagick
```

```
git clone https://github.com/Imagick/imagick
cd imagick
phpize && ./configure
make
sudo make install
```

When you run `make`, if you got the following error:

```
/opt/homebrew/Cellar/php/8.0.0_1/include/php/ext/pcre/php_pcre.h:23:10: fatal error: 'pcre2.h' file not found
```

copy the file inside imagick folder

```
// inside imagick folder
cp /opt/homebrew/Cellar/pcre2/10.36/include/pcre2.h .
```

open `/opt/homebrew/etc/php/8.0/php.ini`

```
extension="imagick.so"
```

## PHP redis

- make sure redis is running

```
pecl install redis
```

select options default are no

- Check if php redis is working

```
php -r "if (new Redis() == true){ echo \"\r\n OK \r\n\"; }"
```

## Xdebug

```
pecl install xdebug
```



## Reloading Extensions

Make sure to restart your web server after installing new packages:

```bash
sudo nginx -s reload
sudo apachectl restart
// or use valet restart
```

If you're using Laravel Valet, you should restart it as well.

```bash
valet restart
```

## Checking Extensions That are Installed

Make sure all extensions are correctly installed and loaded by checking both your PHP webserver and CLI installs:

```bash
php -m | grep redis
```

If extensions aren't properly loaded, there are two easy fixes.

Now check the ini file:

```ini
extension="redis.so"
extension="imagick.so"
zend_extension="xdebug.so"
```

