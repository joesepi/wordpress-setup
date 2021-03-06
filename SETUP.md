#A Wordpress Website and Dev Setup

This is primarily for Mac users at the moment but I'm sure it can also be used on a Windows or Linux machine with some adjustments.

## Requirements

This setup assumes that you are familiar the following:
* [Git](https://git-scm.com/)
* [Composer](https://getcomposer.org/)
* [Wpackagist](http://wpackagist.org/)
* [NPM](https://www.npmjs.com/)
* [Gulp](http://gulpjs.com/)
* [Less](http://lesscss.org/)
* [rsync](http://linux.die.net/man/1/rsync)

Though you can customize the build process of your template as long as you leave a 'prod' entry in the 'scripts' key of the package.json file of your new theme; this is what the wordpress wrapper runs before rsycning the files up to your server.

I'm also assuming you have ssh access to the server you will be deploying this website to. However, I'm sure there are gulp plugins for using FTP instead.

## Setup

!Important! Make sure you read the following:
* [Setting up Apache, MySQL, and PHP locally](http://jason.pureconcepts.net/2012/10/install-apache-php-mysql-mac-os-x/)
* [Installing wordpress](https://codex.wordpress.org/Installing_WordPress)

!Note! You may have to re-run the apache setup above if you've recently upgraded your OS.

#### Clone this Repo or Fork this Repo

```bash
git clone https://github.com/jordalgo/wordpress-setup.git my-new-site
cd my-new-site
```

#### Copy Example and Edit Configs

```bash
cp build-config-example.json build-config.json
cp composer-example.json composer.json
```

build-config.json is used by the rsync tasks in the deploy script and
by the default template to proxy your domain during local development.
*Replace the 'domain' and 'remote' key values with your own.*

composer.json is used to specify the wordpress and wordpress plugin versions
that you want to install for your setup. Don't remove the main wordpress key
but add or remove plugins as needed. The current ones in this file are just
a few favorites.

#### Install Composer

If not already installed globally. [Instructions](https://getcomposer.org/doc/00-intro.md#installation-linux-unix-osx).

#### Download Wordpress and Plugins

```bash
php composer.phar install
```

OR

```bash
php composer.phar update
```

#### Setup your Theme (if using the default theme)

* Copy and rename the 'default' folder in the 'wp-content/themes' directory
* Change the 'default' theme reference in "activeThemes" in your build-config.json
* Download NPM Dependencies

```bash
(cd wp-content/themes/default && npm install)
```

#### Set up a MySQL Database

You can do this on the server or through the many helpful hosting control panel tools.

#### Deploy to Production

This will deploy all the above code to your remote server.

```bash
node deploy.js
```

#### Setting up Environment Vars

*!Important!*

There are two examples of htaccess files in this repo (local & remote).
DO NOT make any changes to htaccess-remote and commit these changes, as it's
very insecure to have a potentially public record of your environmental variables.
Instead when you add all these files to your server, ssh into your server
and copy the htaccess-remote file to a .htaccess file.

```bash
cp htaccess-remote .htaccess
```

Then you want to do the following for this remote .htaccess file:

* [Generate wordpress secret keys](https://api.wordpress.org/secret-key/1.1/salt/) and add them to .htaccess
* Add your database information to this .htaccess

When you are developing locally do the same as above but use the 'htaccess-local' file
as your base and generate new secret keys and use different database variables.

#### Configure the DB

In your web browser go to 'www.domain.com/wordpress/wp-admin/install.php'.

#### Congrats! You Should hopefully have a working wordpress setup!

___

## Local Development and Setup

#### Set up Domain and Virtual Host

Add an entry in '/etc/apache2/extra/httpd-vhosts.conf'

Example:
```
<VirtualHost *:80>
    ServerName domain.com
    ServerAlias www.domain.com
    DocumentRoot "/Users/me/Sites/my-new-site"
    ErrorLog "/private/var/log/apache2/apple.com-error_log"
    CustomLog "/private/var/log/apache2/apple.com-access_log" common
    ServerAdmin web@coolestguidesontheplanet.com
    <Directory "/Users/jordalgo/Sites/eci">
      AllowOverride All
      Require all granted
    </Directory>
</VirtualHost>
```

#### Add entry or un-comment entry in '/private/etc/hosts'

```bash
127.0.0.1 www.domain.com
```

#### Setup a Local DB

* Start your MySQL server
* Create a local db (I use Sequel Pro but there are many other tools to do this.)
* Configure your local .htaccess (see above)

#### Start MySQL and Apache

```bash
sudo apachectl start
```

#### Set up your theme for development

```bash
cd wp-content/themes/default && npm run dev
```

#### Configure the DB

In your web browser go to 'www.domain.com/wordpress/wp-admin/install.php'.
This should point to your local instance of your wordpress setup and DB.

#### Congrats! You should have a local working wordpress setup.

___

## Updating Wordpress Core and Plugins

```bash
php composer.phar update
```

You can add new plugins and update particular version of wordpress or plugins
by editing the 'composer.json' file.

## Todos
* Explore using Timber/Tig
* Add SEO Plugin
* Use Eslint in the default template instead of jshint
* Fix the development workflow

## Extra Information


Developed by Jordan Rome
'www.jordanrome.com'
jordan@jordanrome.com

Theme Based on the Bones (Responsive Edition) Wordpress Theme
Developed by Eddie Machado
'http://themble.com/bones'
eddie@themble.com

And the wordpress install based on
David Winter's Article
'http://davidwinter.me/articles/2012/04/09/install-and-manage-wordpress-with-git/'

