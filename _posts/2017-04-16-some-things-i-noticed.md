---
layout: post
title: "A few things about Magento 2 to be aware of"
date: 2017-04-16 14:09:03
image: '/assets/img/'
description: 'Just a few things I have noticed with Magento 2'
main-class: 'misc'
color: '#000000'
tags:
- Magento 2
- Notes
categories:
- Misc
- Magento 2
twitter_text: 'A few things I have noticed while working with Magento 2'
introduction: "Just a few things I have noticed while working with Magento 2"
---

*This list is not exhaustive, and I will be continually adding to it while I notice more things, so check back often!*

1.  Magento 2 Bundled products will take an exceptionally long time to load in both the frontend and especially Admin Product Edit if there are way too many Bundles and Products in the Single Bundled Product.  You will need a lot of memory on your local computer as well to load the Admin Bundled Product edit page or it will crash. So split up your bundled products if you decide to make them, a too large of one with many products under each option is gonna give you and your customers a headache.

2.  Have a lot of products in a single category page? You may have noticed Magento 2 does not handle this well.  You will see half your products not listed on the category page! There is an open issue for this which can be viewed [here](https://github.com/magento/magento2/issues/8681).  One thing that can be done to alleviate this in the mean time.  You can follow the comment from [gclift](https://github.com/gclift) which have been reprinted here.

1. Create a new Module or use an existing custom module. You can follow my other post on how to do this [here](https://magentofu.com/magento-2-module-file-structure/).
2. Create a new Class that extends the Full Action.
3. Edit di.xml in the etc directory to include a preference node.
4. Clear Cache and Reindex.

```
namespace {company}\{module}\Model\Indexer\Product\Category\Action;

class Full extends \Magento\Catalog\Model\Indexer\Category\Product\Action\Full
{
    protected function isRangingNeeded(){
        return false;
    }
}
FILE: {company}{module}\etc\di.xml

<?xml version="1.0"?>
<config xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="urn:magento:framework:ObjectManager/etc/config.xsd">
    <preference
            for="Magento\Catalog\Model\Indexer\Category\Product\Action\Full"
            type="{company}\{module}\Model\Indexer\Product\Category\Action\Full" />
</config>
```
