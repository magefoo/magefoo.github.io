---
layout: post
title: "Setup Development Environment for Magento 2"
date: 2017-04-16 14:00:10
image: '/assets/img/'
description: 'My notes for setting up a local development environment for Magento 2'
main-class: 'Magento 2'
color: '#000000'
tags:
- Magento 2
- Development
categories:
- Magento 2
- Development
twitter_text: 'Some notes on setting up a local development environment for Magento 2'
introduction: "Just some notes for setting up a local development environment for Magento 2"
---

*This is only for a Linux Environment, because Windows sucks*

Although Magento does a good job in describing how to setup a development environment, and even provides a Vagrant Image, I have my own method to the madness, and it is always good to write these methods down, lest I forget a few steps.

*Important! Never ever run any bin/magento commands as root user! You will screw up the permissions of the generated files and Magento 2 will do a whole lot os screwy stuff!*

#### Workflow
1.  Decide which flavor of Linux to use.
2.  Nginx or Apache?
3.  Install the prerequisites.
4.  Configure Nginx.
5.  Create Database.
6.  Set permissions of directory. (Never under any circumstances run bin/magento commands as root. Ever!)
7.  Install Magento Package via Composer..  
8.  Install Magento via `bin/magento setup:install`.
9.  Install Nodejs, Npm, and Grunt.
11.  Setup Grunt for livereload.
12.  Shoutout to Inchoo for [this article](http://inchoo.net/magento-2/css-preprocessing-in-magento-2/)
13.  Configure Git Version Control.
14.  Profit!

#### One -- Decide which flavor of Linux to use.
This is an easy one, my personal favorite flavour of Linux is [Arch Linux](https://www.archlinux.org/), but most use some flavour of [CentOS](https://www.centos.org/).  If you are a beginner at Linux, then some flavour of [Debian](https://www.debian.org/) or [Ubuntu](https://www.ubuntu.com/).

#### Two -- Nginx or Apache?
Magento 2 requires a [LAMP](https://www.digitalocean.com/community/tutorials/how-to-install-linux-apache-mysql-php-lamp-stack-on-ubuntu) server or a [LEMP](https://www.digitalocean.com/community/tutorials/how-to-install-linux-nginx-mysql-php-lemp-stack-on-ubuntu-12-04).

So which one should you use?

Personally, I prefer Nginx over Apache, so does mostly everyone else.  However, there are pros and cons of both.  I will not go into detail too much here, because there are so many other people way more knowledgable and eloquent who have written about the differences of the two. The one I am linking to will be from Digital Ocean, as they have some very good howto blogs and explain things better than most.

[Nginx vs Apache: Practical Considerations](https://www.digitalocean.com/community/tutorials/apache-vs-nginx-practical-considerations)

#### Three -- Install the prerequisites.
The system requirements for Magento are
1.  PHP 5.6 - PHP 7.0
  If you notice PHP is now above 7.1, you are correct, however, PHP 7.1 is not currently supported for Magento 2, so you will actually have to downgrade PHP 7.1 to PHP 7.0, yes this sucks, and Magento needs to get their stuff together and support PHP 7.1 quickly.  Keep this in mind when installing your system.
2.  Mysql >=5.6
3.  Apache 2.2 - 2.4
4.  Nginx >1.5
5.  Composer (compatible version for whichever version of PHP you are using)

Here is the official DevDocs for [Magento 2 prerequisites](http://devdocs.magento.com/guides/v2.0/install-gde/prereq/prereq-overview.html)
Here is DevDocs for [Magento 2 Optional Software](http://devdocs.magento.com/guides/v2.0/install-gde/prereq/optional.html)

#### Four -- Configure Nginx.
Alas, you will need a working LEMP or LAMP server before we can officially install Magento 2, even if installing via CLI, as it will do a base_url check, and if it fails, it will not install.
To make this easy, I am linking to the [Github Repo](https://github.com/magentofu/nginx) with my personal nginx configs, just change the user and group in `nginx.conf` and the php-fpm information in `upstream.conf`, lastly, change the server_name and $MAGE_ROOT in `conf.d/mage.conf` to suit your needs.

#### Five -- Create Database.
Here are the commands to create a database in CLI.
`mysql -uroot -p`
`CREATE DATABASE <name>;`
`CREATE USER '<name>'@'localhost' IDENTIFIED BY '<password>';`
`GRANT ALL PRIVILEGES ON <database_name>.* TO '<user_name>'@'localhost';`
`FLUSH PRIVILEGES;`
`exit`

#### Six -- Set permissions of directory
[Magento DevDocs](http://devdocs.magento.com/guides/v2.0/install-gde/install-quick-ref.html) for reference.
Add a user you would like to use as the Magento file system owner.

*IMPORTANT! Never ever run any bin/magento commands as any other user other than file system owner, especially never ever run as root user! You will screw up your permissions on generated files, which may not be apparent initially but will give your problems later on.*

As root user:
`adduser <username>`
`passwd <username>`

Find the web server's user's group.
For nginx we just set up, this will be what we set it as in nginx.conf which is generally `http` group. For apache check /etc/httpd/conf/httpd.conf for group.
`grep -i '^user' /etc/nginx/nginx.conf`
or
`grep -i '^user' /etc/httpd/conf/httpd.conf`
`grep -i '^group' /etc/httpd/conf/httpd.conf`

Add Magento file system owner to the web server's group.
`gpasswd -a <user> <group>`

Now become the Magento file system user. Remember the warning!
`su <username>`

From here on out do not use root user, everything will be done as Magento file system user.

#### Seven -- Download Magento 2 metapackage via Composer
First, you will need to create an account on [magentocommerce.com](https://www.magentocommerce.com/magento-connect/customer/account/login/) as this is where the keys to access the magento repo reside.

After creating an account, select the Connect tab, then Developer and then Secure Keys.
type in a name for the keys and select `Generate new`

The Public Key will be used as `username` and the Private Key will be used as `password` for Composer.

*All commands will be as Magento file system user*

For reference [Get the Magento CE metapackage](http://devdocs.magento.com/guides/v2.1/install-gde/prereq/integrator_install_ce.html)

Navigate to your web root and install Magento via composer.
`composer create-project --repository-url=https://repo.magento.com/ magento/project-community-edition .`

You will then be prompted for a username and password, this will be the Public Key and Private Key respectively from your magentocommerce.com account.
You can save the permissions as well by hitting enter or selecting Y when prompted. These will save the keys in /home/<user>/.composer/auth.json
One thing to note, Magento 2 saves composer keys in two places, so say you are also installing the Sample Data afterwards, it will again prompt you for your username and password, but this time saves them in var/composer_home/.auth.json

In web root, set the permissions of the directory.
`chown -R <magento_user>:<web_server_group> .`

#### Eight -- Install Magento 2
Now, we actually install Magento 2.

For reference [Install Magento 2 Software](http://devdocs.magento.com/guides/v2.1/install-gde/install/cli/install-cli.html)

`chmod +x bin/magento`
*you can also prefix the command with php ie php bin/magento*

*IMPORTANT! ensure you are the Magento File System Owner User*

What you will need from the previous steps:
1.  Database Name
2.  Database User
3.  Database User Password
4.  Server Name from `conf.d/mage.conf`

Now we start:

`cd /path/to/Magento_2/`
`su <magento_user>`

`php bin/magento setup:install --backend_frontname=my_admin --base-url=http://server_name --use-secure=0 --use-secure-admin=0 --db-name=<database_name> --db-user=<database_user> --db-password=<database_password> --admin-firstname=<Firstname> --admin-lastname=<Lastname> --admin-email=<user@example.com> --admin-user=admin --admin-password=<password> --language=en_US --currency=USD --timezone=America/Chicago --use-rewrites=1`

After installation, you should see something similiar

```
Post installation file permissions check...
For security, remove write permissions from these directories: '/path/to/magento2/app/etc'
[Progress: 274 / 274]
[SUCCESS]: Magento installation complete.
[SUCCESS]: Admin Panel URI: /my_admin
```

Now, lets set the permissions for the Magento files.
`find . -type f -exec chmod 644 {} \;`
`find . -type d -exec chmod 755 {} \;`

You should now be able to navigate to the frontend of your store
`http://server_name`

I have noticed that sometimes you must deploy static files if the css does not show properly, however, since you should be in developer mode, this should not be an issue, anyhow, if you cannot properly see your store:
`php bin/magento setup:static-content:deploy`

If you still cannot see your store, you've done messed up.

Review the steps and try again.

#### Install Nodejs, Npm, and Grunt

Now that you have a working development store, we want to use fancy shmancy Live Reload.

For reference [Installing and configuring Grunt](http://devdocs.magento.com/guides/v2.1/frontend-dev-guide/css-topics/css_debug.html)

Install Nodejs, Npm, and Grunt-Cli global as root user.

Most Linux Disto's have Nodejs and Npm in their Official Repos, so check the docs for your Linux Distro on how to install these.

*As root user*
For CentOS:
`yum install nodejs npm`

For Arch Linux
`pacman -S nodejs npm`

For Ubuntu or Debian
`apt-get install nodejs npm`

Afterwards, install grunt-cli globally.
`npm install -g grunt-cli`

Next,  copy package.json.sample and Grunfile.js.sample to package.json and Gruntfile.js.
`cp package.json.sample package.json`
`cp Gruntfile.js.sample Gruntfile.js`

Become Magento file system user
`su <magento_user>`

*As Magento User*
Run Npm install.
`npm install`

Next, create your theme skeleton directories and files.

To create a skeleton for a custom theme follow [my other tutorial here](http://magentofu.com/create-a-custom-theme-in-magento-2)

After creating the initial theme structure, we want to add the theme to the Grunt config.

To do this, in the `dev/tools/grunt/configs/themes.js` file, add your theme to `module.exports` like following:

```
module.exports = {
    ...
    <theme>: {
        area: 'frontend',
        name: '<Vendor>/<theme>',
        locale: '<language>',
        files: [
            '<path_to_file1>', //path to root source file
            '<path_to_file2>'
        ],
        dsl: '[less|scss]'
    ...
    },
```

This is an example straight from my `dev/tools/grunt/configs/themes.js` file:

```
module.exports = {
    jessie: {
        area: 'frontend',
        name: 'Magentofu/jessie',
        locale: 'en_US',
        files: [
            'css/styles-m',
            'css/styles-l'
        ],
        dsl: 'less'
    }
}
```

This file will be empty except for notes if you have not run grunt at least once, it will populate the Magento Blank, Magento Luma, and Backend Themes once grunt is run.

To see live reload in action, install the Livereload Chrome extension in the next step and then run grunt exec:<theme>, then grunt watch.
`grunt exec:<theme>`
`grunt watch`

Install the Chrome Livereload extension.
[Get the extension here](https://chrome.google.com/webstore/detail/livereload/jnihajbhpnppcggbcgedagnkighmdlei)

After the extension is installed, navigate to the store frontend
`http://server_name`

Then select the Livereload button, the center of the icon will become filled, and hovering over the icon will state "Livereload connected".

Check devtools as sometimes it will say "Livereload.js:CONNECTION REFUSED" in which case you will need to troubleshoot your system, for example, I cannot use Livereload while connected to a VPN.

Keep in mind, live reload is only for changing CSS, changing layout files or templates, you will still need to hard refesh, although it is pretty nice when styling your site to see your changes immediately.

#### Shoutout to Inchoo
[This splendid article from Inchoo](http://inchoo.net/magento-2/css-preprocessing-in-magento-2/) explains exactly how livereload and grunt works, and some of the intricacies and nuances you may run into.

#### Configure Git Version Control.
I'm only going to do a simple Git configuration here.  I will go more in depth of setting up a production Git environment in another article.

Install git based on your Linux Distro.

Setup git configuration
`git config --global user.name '<Your Name>'`
`git config --global user.email '<Your Email>'`

Change directory to the webroot.
`cd /path/to/webroot/`

Run git init.
`git init`

Setup a <b>private</b> repo on Github or Gitlab or Bitbucket. Note, Bitbucket has private repos for the free level while the others require a paid subscription for private repos.

*It is not wise to use a public repo, if you do decide to use a public repo, ensure you use a .gitignore file and never store your credentials in Git!*

Setup a .gitignore file, you do not need to store your media, your passwords, or any other sensitive information in Git.

Here is a sample .gitignore file

```
#==========================#
# Magentofu .gitignore     #
#==========================#

# Custom
#=========================#
/*.sql
/*.sql.gz
/*.tar.gz
/*.html
/*.md
/*.sample
/*.txt
/*.lock
/*.log
/*.phar
/*.zip
/*.ini
/.htaccess
/.travis.yml

# Theme
#=========================#
!/dev/tools/grunt/configs/themes.js

# Default
#=========================#
/.buildpath
/.cache
/.metadata
/.project
/.settings
atlassian*
/nbproject
/sitemap
/sitemap.xml
/.idea
/.gitattributes
/app/config_sandbox
/app/etc/config.php
/app/etc/env.php
/app/code/Magento/TestModule*
/lib/internal/flex/uploader/.actionScriptProperties
/lib/internal/flex/uploader/.flexProperties
/lib/internal/flex/uploader/.project
/lib/internal/flex/uploader/.settings
/lib/internal/flex/varien/.actionScriptProperties
/lib/internal/flex/varien/.flexLibProperties
/lib/internal/flex/varien/.project
/lib/internal/flex/varien/.settings
/node_modules
/.grunt
#/Gruntfile.js
#/package.json
/.php_cs
/.php_cs.cache
/grunt-config.json
/dev/tools/grunt/configs/local-themes.js

/pub/media/*.*
!/pub/media/.htaccess
/pub/media/attribute/*
!/pub/media/attribute/.htaccess
/pub/media/analytics/*
/pub/media/catalog/*
!/pub/media/catalog/.htaccess
/pub/media/customer/*
!/pub/media/customer/.htaccess
/pub/media/downloadable/*
!/pub/media/downloadable/.htaccess
/pub/media/favicon/*
/pub/media/import/*
!/pub/media/import/.htaccess
/pub/media/theme/*
/pub/media/theme_customization/*
!/pub/media/theme_customization/.htaccess
/pub/media/wysiwyg/*
!/pub/media/wysiwyg/.htaccess
/pub/media/tmp/*
!/pub/media/tmp/.htaccess
/pub/media/captcha/*
/pub/static/*
!/pub/static/.htaccess

/var/*
!/var/.htaccess
/vendor/*
!/vendor/.htaccess
/generated/*
!/generated/.htaccess
```

Or you can copy from [My github](https://gist.github.com/djfordz/336cf932e913daf9c6fa650e1d4198bb)

Set the remote repository.
`git remote add origin git@github.com:magentofu/<private_repository>.git`

Next add all the files to your git repository.
`git add .`
`git commit -m "Initial Commit"`
`git push --set-upstream origin master`

Now, whenever you are working, you can checkout new repos, to ensure you keep track of your work
`git checkout -b new_repo`

*It is highly advisable to learn Git very well, Git is an awesome Version Control System and is used virtually everywhere. I will have another article explaining how to setup your own Git server as well as a Production Git environment soon, so check back regularly.*
