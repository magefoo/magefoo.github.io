---
layout: post
title: "Creating the skeleton directory structure for Theming in Magento 2"
date: 2017-04-16 12:59:58
image: '/assets/img/'
description: 'Magento 2 theme directory structure'
main-class: 'theme'
color: '#000000'
tags:
- Magento 2
- Theme
categories:
- Magento 2
- Theme
twitter_text: 'Magento 2 Theme Directory Structure'
introduction: "A Magento 2 Skeleton Theme"
---

There are a few things that need to happen, when creating a custom theme in Magento 2.  We will layout a game plan, then walk through each aspect of the creation of a custom theme.

1.  Decide what will be the parent theme.
2.  Create the custom theme directory structure.
3.  Create required files.

For reference, follow along in the [Magento DevDocs: Frontend Developer Guide](http://devdocs.magento.com/guides/v2.1/frontend-dev-guide/bk-frontend-dev-guide.html)

This is for a initial skeleton theme, there will be follow-up articles delving deeper into Magento 2 Theme development later, so check back often! 

*IMPORTANT! Only the Vendor Namespace is Capitalized when creating the directory structure, the theme name directory, is not.*

It is important to note, for a Magento 2 Module, the Vendor Namespace and Module Name directories are capitalized, however for a theme directory structure, only the Vendor Namespace is capitalized the theme name directory is not.

For example, the correct way:

`app/design/frontend/Magentofu/jessie`

If I were to capitalize `jessie` it would cause problems for me using Grunt and also for other things down the road, so don't capitalize your theme directory, which is the directory right under the Vendor directory, which is capitalized.

If you forget this, and intialize your theme, there will be database entries created using the directory structure.

If you then decide to delete a theme, use the `bin/magento theme:uninstall` command, do not just delete the directory structure, as database entries still exist for the theme!

If you forget and delete the directory structure manually, you will need to delete the database entries as well. You can do this by following the directions here: [Delete Theme Manually Article here](http://magento.fu/deleting-a-theme-in-magento-2)

#### One -- Decide what to use as the parent theme.

In Magento 2 like Magento 1, themes fallback and recurse through a theme hierarchy.  The difference between Magento 2 and Magento 1 is, Magento 2, you decide which theme is the parent theme, then it only falls back to the parent theme, the parent theme falls back to its parent, etc.

It is important to note, the final fallback theme is not actually a theme, but the modules which the theme extends, for example, Magento_Theme, extends from `vendor/magento/module-theme/` I know, right? So all those Directories in your theme? Are the modules your theme extends from. Remember this.

#### Two -- Create the custom theme directory structure.

*Remember to keep you theme name directory lowercase and only your Vendor Directory Uppercase*

It will look something like this:

`mkdir -p app/design/frontend/Magentofu/jessie/`

Next create the required directories and files

`mkdir -p app/design/frontend/Magentofu/jessie/etc/`

`mkdir -p app/design/frontend/Magentofu/jessie/web/css/source/`

`mkdir -p app/design/frontend/Magentofu/jessie/media/`

Your directory structure will eventually look like this:

```
app/design/frontend/<Vendor_Namespace>/<theme_dir>/
                                                ├── <Vendor>_<Module>/
                                                │	├── web/
                                                │	│	├── css/
                                                │	│	│	├── source/
                                                │	├── layout/
                                                │	│	├── override/
                                                │	├── templates/
                                                ├── etc/
                                                ├── i18n/
                                                ├── media/
                                                |       |- preview.jpg
                                                ├── web/
                                                │	├── css/
                                                │	│	├── source/
                                                │	├── fonts/
                                                │	├── images/
                                                │	├── js/
                                                ├── composer.json
                                                ├── registration.php
                                                ├── theme.xml
```

Create the required files:

`touch app/design/frontend/Magentofu/jessie/composer.json`

`touch app/design/frontend/Magentofu/jessie/registration.php`

`touch app/design/frontend/Magentofu/jessie/theme.xml`

`touch app/design/frontend/Magentofu/jessie/etc/view.xml` (optional)

#### Sample `composer.json`

```
    {
        "name": "magentofu/jessie",
        "description": "professional theme made for selling jewelry or small knicknacks",
        "require": {
            "php": "~5.5.0|~5.6.0|~7.0.0",
            "magento/theme-frontend-blank": "100.0.*",
            "magento/framework": "100.0.*"
        },
        "type": "magento2-theme",
        "version": "100.0.1",
        "license": [
            "OSL-3.0",
            "AFL-3.0"
        ],
        "autoload": {
            "files": [
                "registration.php"
            ]
        }
    }
```

#### Sample `registration.php`

*change the frontend/Magentofu/jessie to whatever your theme directory is*

```
    <?php
    /**
     * Copyright © 2015 Magento. All rights reserved.
     * See COPYING.txt for license details.
     */
    \Magento\Framework\Component\ComponentRegistrar::register(
        \Magento\Framework\Component\ComponentRegistrar::THEME,
        'frontend/Magentofu/jessie',
        __DIR__
    );
```

#### Sample `theme.xml`

*change the parent as well as title, the title is what shows up in admin and can be capitalizedv*

*be sure you add a preview_image to the media directory! or you will get an error!*

```
    <theme xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="urn:magento:framework:Config/etc/theme.xsd">
         <title>Magentofu Jessie</title>
         <parent>Magento/blank</parent>
         <media>
             <preview_image>media/preview.jpg</preview_image>
         </media>
     </theme>
 ```

 #### Sample `etc/view.xml`

*customized from Magento Luma Theme*

 ```
    <?xml version="1.0"?>
    <!--
    /**
     * Copyright © 2013-2017 Magento, Inc. All rights reserved.
     * See COPYING.txt for license details.
     */
    -->
    <view xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="urn:magento:framework:Config/etc/view.xsd">
        <media>
            <images module="Magento_Catalog">
                <image id="bundled_product_customization_page" type="thumbnail">
                    <width>140</width>
                    <height>140</height>
                </image>
                <image id="cart_cross_sell_products" type="thumbnail">
                    <width>200</width>
                    <height>248</height>
                </image>
                <image id="cart_page_product_thumbnail" type="small_image">
                    <width>165</width>
                    <height>165</height>
                </image>
            </images>
        </media>
    </view>
  ```

You now have a Skeleton Theme that can be selected in Magento Admin.

Content->Configuration->Store View->edit

