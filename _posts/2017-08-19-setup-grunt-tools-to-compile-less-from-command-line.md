---
layout: post
title: "Setup Grunt tools to compile LESS from command line"
date: 2017-08-19 08:50:45
image: '/assets/img/'
description: 'How to use Grunt for Magento 2 Development'
main-class: 'm2'
color: '#000000'
tags:
- Magento 2
- Dev
- Grunt
categories:
- Magento 2
twitter_text: 'How to use Grunt for Magento 2 Development'
introduction: 'How to Use Grunt for Magento 2 Development'
---

Using Grunt to compile your Less files on the fly is a great tool to use when developing for Magento 2.  It allows you to see changes to CSS immediately, as well as alerts you to the exact code that fails compilation so it can be fixed fast and easy.

Setting up Grunt Tools is pretty straight forward, and described in Magento Dev docs here: [Compile LESS with Grunt](ihttp://devdocs.magento.com/guides/v2.1/frontend-dev-guide/css-topics/css_debug.html)

But to make a long story short, I will walk through the setup process.

First, node.js and npm need to be installed on your server, which is easily done, just follow instructions for the flavor of linux you have such as for CentOS or RHEL,`yum install nodejs npm`, debian, ubuntu `apt-get install nodejs npm`, Arch Linux `pacman -S nodejs npm`.

Afterwards, you will need to install grunt-cli globally.
`npm install -g grunt-cli`

Next, navigate to your Magento 2 web root. (The directory where Magento 2 is installed).

You will need to rename two files, rename `Gruntfile.js.sample` to `Gruntfile.js` ie `mv Gruntfile.js.sample Gruntfile.js`, next rename `package.json.sample` to `package.json`.

Once this is complete simply install the packages that are listed in package.json by using the following command

`npm install`

Once all the packages have been installed in the project, ensure your Magento 2 installation is in dev mode.

`bin/magento deploy:mode:set development`

Next, you will need to add your custom themes to `dev/tools/grunt/configs/theme.js` and example is below:

```
/**
 * Copyright © 2013-2017 Magento, Inc. All rights reserved.
 * See COPYING.txt for license details.
 */

'use strict';

/**
 * Define Themes
 *
 * area: area, one of (frontend|adminhtml|doc),
 * name: theme name in format Vendor/theme-name,
 * locale: locale,
 * files: [
 * 'css/styles-m',
 * 'css/styles-l'
 * ],
 * dsl: dynamic stylesheet language (less|sass)
 *
 */
module.exports = {
    blank: {
        area: 'frontend',
        name: 'Magento/blank',
        locale: 'en_US',
        files: [
            'css/styles-m',
            'css/styles-l',
            'css/email',
            'css/email-inline'
        ],
        dsl: 'less'
    },
    luma: {
        area: 'frontend',
        name: 'Magento/luma',
        locale: 'en_US',
        files: [
            'css/styles-m',
            'css/styles-l'
        ],
        dsl: 'less'
    },
    backend: {
        area: 'adminhtml',
        name: 'Magento/backend',
        locale: 'en_US',
        files: [
            'css/styles-old',
            'css/styles'
        ],
        dsl: 'less'
    }
};
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

Once you have added your custom theme to the `theme.js` file, remove all the files in `pub/static` and remove the `var/generation` and `var/view_preprocessed` and `var/di`, with a command like `rm -rf pub/static/* var/view_preprocessed var/generation var/di`.

When that is complete, you should now be able to compile your CSS with grunt.

To get ready to use Grunt watch for live reload capabilities, you need to run `grunt exec:<theme>`, `grunt less:<theme>`, and then `grunt watch` in that order.

## To use livereload functionality, you must install a browser extension

*If you have a firewall, you must allow port 35729, which is the default port for LiveReload.*

For Chrome install the livereload browser extension from here [LiveReload](https://chrome.google.com/webstore/detail/livereload/jnihajbhpnppcggbcgedagnkighmdlei)

Once it is installed, you can run `grunt watch` and select the live reload extension button, it should allow you to connect. In Chrome, sometimes you will get an error in console stating failed to connect, simply hard refresh the page a few times until chrome decides to connect properly.




