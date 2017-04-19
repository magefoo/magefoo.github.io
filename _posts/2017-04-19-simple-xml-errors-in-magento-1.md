---
layout: post
title: "Simple XML Errors in Magento 1"
date: 2017-04-19 11:49:02
image: '/assets/img/'
description: 'How to debug simple XML errors in Magento 1'
main-class: 'magento1'
color: '#DA6D48'
tags:
- Magento 1
- Debug
categories:
- Magento 1
twitter_text: 'Debug Simple XML errors in Magento 1'
introduction: "Debugging Simple XML errors in Magento 1"
---

This error indicates a parsing problem with one of the module XML files.

However, by default the error messages doesn’t help very much in pinpointing what file is the trouble-maker. Here is a tip to help.

Open up Config.php located at `/lib/Varien/Simplexml/Config.php` and go to line 510:

```
$xml = simplexml_load_string($string, $this->_elementClass);

#Below this line, add the following:

if(!$xml){ Mage::log($string); }
```

This snippet will add the offending line and the full text of the offending XML file to your system.xml file so you can fix the error.

After correcting the problem, be sure to refresh the Magento cache.

Also, remove the troubleshooting line from Config.php once the problem is fixed.
