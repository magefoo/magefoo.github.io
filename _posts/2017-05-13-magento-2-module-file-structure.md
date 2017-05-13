---
layout: post
title: "Magento 2 Module File Structure"
date: 2017-05-13 10:13:03
image: '/assets/img/'
description: 'A skeleton Magento 2 Module'
main-class: 'm2'
color: '#000000'
tags:
- Magento 2
- Dev
- Cheatsheet
categories:
- Magento 2
- Dev
twitter_text: 'create a Magento 2 module'
introduction: 'A skeleton Magento 2 Module'
---

Creating a Magento 2 module follows pretty much the same methodology as Magento 1, but obviously differs significantly in many ways.  Below is the absolute minimum needed to have a functioning Magento 2 module.

First, an overview of the directory structure.  There will are two places that Magento 2 will look for code, `app/code/` directory and `vendor/` directory.  Some professional extensions will actually install themselves in the `vendor/` directory under their own namespace, but for the most part, most will install themselves into `app/code/` which is where we will be creating this extension.

Magento 2 has a repository full of sample modules, so go ahead and head over to [https://github.com/magento/magento2-samples](https://github.com/magento/magento2-samples).

Configuration files

Configuration files that are in the top level of that module’s etc directory are global to that component.
Configuration files placed in subdirectories (adminhtml, frontend, webapi_rest, webapi_soap) apply only to those respective functional areas.

*It is very important to note that directory and file structure is case sensitive and MUST follow the exact Case which is laid out, also, to note, in Magento 2 Modules, the `/Vendor/Module/` directories are both Capitalized but theme directories ONLY the Vendor is capitalized, the theme name is not `/Vendor/themename/`. Remember themes are under `app/design/` and modules are under `app/code/`*

```
<Magento Root>/app/code/Vendor/Module/
                                |   registration.php
                                |   composer.json
                                |
                                /etc/
                                    module.xml
```

Next, the module must be registered in the Magento System, this is done with the `ComponentRegistrar` Class in the `registration.php` file in the root of the module directory.

```
<?php

use \Magento\Framework\Component\ComponentRegistrar;

ComponentRegistrar::register(ComponentRegistrar::MODULE, '<Vendor>_<ModuleName>', __DIR__);

```

After adding the `registration.php` file, the module's `composer.json file must be created.

```
{
  "name": "<vendor>/<module>",
  "description": "short description of module",
  "authors": [
    {
      "name": "David Ford",
      "email": "magentofu@gmail.com",
      "homepage": "http://magentofu.com"
    }
  ],
  "require": {
    "php": "~5.6.0|7.0.2|~7.0.6",
    "magento/module-ui": "100.1.*"
  },
  "type": "magento2-module",
  "version": "1.0.0",
  "license": [
    "OSL-3.0",
    "AFL-3.0"
  ],
  "autoload": {
    "files": [ "registration.php" ],
    "psr-4": {
      "Vendor\\Module\\": ""
    }
  }
}
```

The final required file is the module.xml file, this is located in `/etc/` directory.

```
<?xml version="1.0"?>

<config xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="urn:magento:framework:Module/etc/module.xsd">

    <module name="Vendor_Module" setup_version="1.0.0">
        <sequence>
            <module name="Magento_Ui"/>
        </sequence>
    </module>
</config>
```

This is it, for a skeleton Magento 2 Module.



Below is a file structure example of a more complex and complete module.

```
# required files *

<Magento install directory>/app/
                                |
                                /code/
                                    |
                                    /Vendor/
                                        |
                                        /Module/
                                            |
                                            /etc/
                                            |   |
                                            |   module.xml*
                                            |   acl.xml
                                            |   di.xml
                                            |   eav_attributes.xml
                                            |   webapi.xml
                                            |   config.xml
                                            |   |
                                            |   /frontend/
                                            |   |       di.xml
                                            |   |       routes.xml
                                            |   |       events.xml
                                            |   |       page_types.xml
                                            |   |       
                                            |   /adminhtml/
                                            |   |       di.xml
                                            |   |       routes.xml
                                            |   |       events.xml
                                            |   |       menu.xml
                                            |   |       system.xml
                                            |   |
                                            |   /webapi_rest/
                                            |   |       di.xml
                                            |   |
                                            |   /webapi_soap/
                                            |           di.xml
                                            |
                                            /Observer/
                                            |
                                            /Block/
                                            |
                                            /Controller/
                                            |
                                            /Helper/
                                            |
                                            /Console/
                                            |
                                            /Model/
                                            |
                                            /Setup/
                                            |
                                            /Test/
                                            |
                                            /i18n/
                                            |
                                            /Plugin/
                                            |
                                            /Api/
                                            |
                                            /view/
                                            |
                                            composer.json*
                                            registration.php*
                                            LICENSE.txt
                                            LICENSE_AFL.txt
                                            README.md
                                            
```
