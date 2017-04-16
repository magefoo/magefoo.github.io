---
layout: post
title: "Deleting a Theme in Magento 2"
date: 2017-04-16 12:18:25
image: '/assets/img/'
description: 'Pointers when deleting a theme in Magento 2'
main-class: 'Magento 2'
color: '#000000'
tags:
- Magento 2
- Theme
- General
categories:
- Magento 2
- Theme
- General
twitter_text: 'Deleting a theme in Magento 2'
introduction: "Pointers when deleting a theme in Magento 2"
---

I'd like to add before we begin, which trips many people up, because once you create the theme directory structure and load Magento Admin, the directory structure, becomes the theme name, and an entry is created in the database.
If then, the directory structure is deleted, there are still references to it in the database, which can cause issues if not taken care of properly. So I would like to go over the very first thing you should know when naming your theme, unlike Magento 1,
the Vendor Namespace will be capitalized <b>but</b> the theme name will be *all lower case*!  Example `app/design/frontend/Magentofu/magentofu/`. This is important, many people capitalize both the Vendor Namespace and the theme name, which then causes issue down the road, they realize there mistake, and delete the directory structure, but then the theme still exists in admin! 

Arrgghh....If you are that guy, here is how to delete the theme reference in the Magento 2 database as well.

*note, to delete a theme properly, you should use the `bin/magento theme:uninstall` command, and follow the directions [here](http://devdocs.magento.com/guides/v2.0/install-gde/install/cli/install-cli-theme-uninstall.html)*

However, for emergency situations, like if you already deleted the directory structure, then do this:

1.  Delete the content from app/design/frontend/<Vendor>
2.  Delete the `var/view_preprocessed` & `var/di` & `pub/static/frontend` folders.
3.  Database: `SELECT * FROM theme;` (find entry for your theme) `DELETE FROM theme WHERE theme_id = '<theme_number>';`
4.  Database: `SELECT * FROM core_config_data WHERE path like '%theme%';` (replace your default theme id in any place the records have your old theme id).
5.  If production mode, deploy static content `bin/magento setup:static-content:deploy` &  recompile `bin/magento setup:di:compile`
5.  Flush cache `bin/magento cache:flush`

*this is not an exhaustive list as there still may be illegitimate theme references in other database tables as well, these are only the one's I know about.*
