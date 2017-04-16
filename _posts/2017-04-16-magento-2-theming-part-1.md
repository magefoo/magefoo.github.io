---
layout: post
title: "Magento 2 Theming: Layout Files"
date: 2017-04-16 11:06:16
image: '/assets/img/'
description: 'How to do stuff with Layout files in Magento 2'
main-class: 'Magento 2'
color: '#000000'
tags:
- Magento 2
- Theme
- Howto
categories:
- Magento 2
- Theme
- Howto
twitter_text: 'How to do stuff with Layout files in Magento 2'
introduction: "How to do stuff with Layout files in Magento 2"
---

Magento 2 can be tough to understand sometimes. Through my work, I have seen a tremendous amount of 'how not to do stuff', which led me write this article.  Magento 2 theming can, with practice, not be difficult. Here are a few pointers to get you started.

To be honest, the [Magento 2 DevDocs for Frontend Theme Development](http://devdocs.magento.com/guides/v2.1/frontend-dev-guide/bk-frontend-dev-guide.html), are pretty straight forward and easy to understand, so reading and following them is critical to properly theme in Magento 2.

Magento 2, like Magento 1, uses the fallback theme method, so, if something is not found in the current, theme, it falls back to the parent theme. 

The first thing of note, the directory structure is different than Magento 1, <b>Only the Vendor Name is capitalized</b>, the theme name is not, so when setting up your directory structure, it will follow this example:

```
app/design/frontend/Magentofu/magentofu/
```

*notice, only the Vendor Name is Capitalized*

If you capitalize both the Vendor Name and the Theme Name directory it will cause issues down the road.  If you have already setup a theme directory structure, and loaded the Magento Admin panel, a database entry will be created for the theme, so do not just delete the files, use the `bin/magento theme:uninstall command` and follow the instructions [here](http://devdocs.magento.com/guides/v2.0/install-gde/install/cli/install-cli-theme-uninstall.html)

If you have already deleted the theme structure after loading the Magento Admin, you can follow the directions in my [other post](http://magentofu.com/deleting-a-theme-in-magento-2/) to manually delete the database entries of the theme.

### Now On To Layout Files 
In Magento 2, you do not need to create all new layout files or copy over layout files from the parent theme, you extend the parent theme layout, which means, only change what you need to change, no copying over the entire layout file.  If you need to override a layout file completely, there is a special way of doing so. You need to put it in special directory called, you guessed it `override`. Here is the directory structure for overriding layout files. But remember! you do not need to
generally override a layout file, extending a layout file is plenty enough!


```
  <theme_dir>
  |__/<Namespace_Module>
    |__/layout
      |__/override
         |__/base
           |--<layout1>.xml
           |--<layout2>.xml
```

To override a theme layout file completely, here is again the directory structure to do so.

```
<theme_dir>
  |__/<Namespace_Module>
    |__/layout
      |__/override
         |__/theme
            |__/<Parent_Vendor>
               |__/<parent_theme>
                  |--<layout1>.xml
                  |--<layout2>.xml

```

*You will almost never have to override a parent theme layout file, extending the layout file of the parent theme is plenty in most cases.*

#### Now that we have that out of the way, lets learn to remove items, move items, and generally do whatever when extending layout files.
*UNFINISHED MORE TO COME*
