---
layout: post
title: "Common Errors in Magento 2"
date: 2017-08-30 17:10:20
image: '/assets/img/'
description: "Resolutions to Common Errors in Magento 2"
main-class: "m2"
color: "#222222"
tags:
- Magento 2
- Errors
- Troubleshooting
categories:
- Magento 2
twitter_text: "Resolutions to Common Errors in Magento 2"
introduction: "Resolutions to Common Errors in Magento 2"
---

Magento 2 is notorious for giving errors that are extremely difficult to interpret. In this blog post we will be going through a few that I have run into lately. This post will be updated periodically so check back for more good info in the future.

## Not all Extensions are created equal.

Just because an extension is marketed for Magento does not mean that the developer of that extension followed Magento Standard Practices, or that that extension cannot introduce even more bugs than solutions it solves.  There are plenty of bad Magento developers out there, and even bad development agencies, so why would extensions whom anyone can write be any different.

One common issue I have found is devs not properly adding URN's to the xml files.  Sure, these files amy still function in Production Mode, but these URN's are necessary for Developer Mode, as Magento 2 Validates the Merged Config against these URN's. What is an URN? A Uniform Resource Name.  From the [RFC2141 which is the protocol for URN's](https://www.ietf.org/rfc/rfc2141.txt) states:

> Uniform Resource Names (URNs) are intended to serve as persistent,
   location-independent, resource identifiers. This document sets
   forward the canonical syntax for URNs.  A discussion of both existing
   legacy and new namespaces and requirements for URN presentation and
   transmission are presented.  Finally, there is a discussion of URN
   equivalence and how to determine it.

When an URN is not present in a file, whether it be a Magento Core file or an extension, you will receive an error in developer mode.  This error is below so if you ever run into it, check your URN's!

> ] PHP Fatal error: Uncaught Magento\Framework\Exception\LocalizedException: Invalid Document
Element 'config', attribute 'xsi:noNamespaceSchemaLocation': The attribute 'xsi:noNamespaceSchemaLocation' is not allowed.
Line: 2
in /<magento root>/vendor/magento/framework/Config/Reader/Filesystem.php:160
Stack trace:
#0 /<magento root>/vendor/magento/framework/Config/Reader/Filesystem.php(127): Magento\Framework\Config\Reader\Filesystem->_readFiles(Object(Magento\Framework\Config\FileIterator))
#1 /<magento root>/vendor/magento/framework/App/ObjectManager/ConfigLoader.php(69): Magento\Framework\Config\Reader\Filesystem->read('global')
#2 /<magento root>/vendor/magento/framework/App/ObjectManager/Environment/Developer.php(77): Magento\Framework\App\ObjectManager\ConfigLoader->load('global')
#3 /<magento root>/vendor/magento/framework/App/ObjectManagerFactory.php(194): Magento\Framework\App\ObjectManager\Environment\Developer->configureObjectManager(Object(Magento\Framework\Inte in /home/software/public_html/vendor/magento/framework/Config/Reader/Filesystem.php on line 160


## Do not use the Advanced Menu to Disable Modules!

Honestly, I do not know whay Magento even has an Advanced Menu in Admin->Stores->Configuration->Advanced->Advanced, truly the most useless and troublesome menu in Magento, for both Magento 1 and Magento 2. Luckily they are removing the functionality of this Menu in Version 2.2, but until then take heed if you have disabled a module in this fashion.

One Error that will occur if a module is disabled in this fashion, is this one:

> "Object DOMDocument should be created."
> " a:4:{i:0;s:37:"Object DOMDocument should be created.";i:1;s:10125:"#0 /<Magento Root>/vendor/magento/framework/View/Element/UiComponent/Config/Reader.php(95): Magento\Framework\View\Element\UiComponent\Config\DomMerger->getDom()
#1 /<Magento Root>/vendor/magento/module-ui/Model/Manager.php(261): Magento\Framework\View\Element\UiComponent\Config\Reader->read()
#2 /<Magento Root>/vendor/magento/module-ui/Model/Manager.php(169): Magento\Ui\Model\Manager->prepare('form_schedule_b...')
#3 /<Magento Root>/vendor/magento/framework/View/Element/UiComponentFactory.php(139): Magento\Ui\Model\Manager->prepareData('form_schedule_b...')
#4 /<Magento Root>/vendor/magento/framework/View/Layout/Generator/UiComponent.php(125): Magento\Framework\View\Element\UiComponentFactory->create('form_schedule_b...', NULL, Array)

*The Advanced Menu will not show any modules as disabled, even if disabled has been selected and saved, after save, it will show enable in the select box, the only way to view this is in the database itself under core_config_data table.*

More to come so stay tuned...
