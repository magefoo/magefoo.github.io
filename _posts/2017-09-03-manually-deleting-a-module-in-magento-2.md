---
layout: post
title: "Manually deleting a module in Magento 2"
date: 2017-09-03 05:57:55
image: '/assets/img/'
description: 'A few pointers when deleting a Magento 2 Module'
main-class: 'm2'
color: '#222222'
tags:
- Magento 2
- Modules
categories:
- Magento 2
twitter_text: 'A few pointers when deleting a Magento 2 Module'
introduction: 'A few pointers when deleting a Magento 2 Module'
---

When deleting a module it is best to use Magento 2's built in functionality for this. However, their is one caveat with using it, the module must have been installed using composer.
If the module was installed with composer, then all that needs to be done is using the `bin/magento module:uninstall Vendor_Module`.

If the module was installed manually and not with composer the above command will tell you so. In this case, it is important to find and delete all database data associate with the module.  The best way to do this, is manually going through the setup and upgrade scripts and looking which columns, tables, and data are installed into the database when the module was installed. After compiling the list, then simply find the tables, columns, and or data that particular module installed.

Regardless of whether the module installed database data or not, one row in table `setup_module`.

`setup_module` table takes the place of `core_resource` of Magento 1, and lists all modules that are installed, when removing a module's files, the entry into `setup_module` table also needs to be deleted.

It is important to take note that if the module uses it's own Backend Model for its configurations settings in config, your store will experience problems if the rows pertaining to the module are not deleted from table `eav_attribute`.

If deleting a module manually, it is always important to remove database data as well as the files, since every module adds its own data, every module is different, their may be a list in the Google Sphere for more popular ones like M2Epro, but in general, one needs to decipher the setup and upgrade scripts to ensure all db data is removed and the reference to the module be removed from table `setup_module`.

Once the database data is removed, ensure the reference to the module is removed from `app/etc/config.php`, remove the directories in app/code/ related to the module and your Magento 2 store should now not have any references to that particular module.
