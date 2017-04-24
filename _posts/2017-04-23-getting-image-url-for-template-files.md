---
layout: post
title: "Getting Image URL for template files"
date: 2017-04-23 17:20:13
image: '/assets/img/'
description:
main-class:
color:
tags:
categories:
twitter_text:
introduction:
---

Just a quick on how to get image urls in both template files and cms blocks for Magento 2.

For template files:

Images in `Vendor/theme/Vendor_Module/web/images` or `app/code/Vendor/Module/view/frontend/web/images/`

```
<img src="<?php echo $this->getViewFileUrl('Vendor_Module::images/image.png'); ?>" />
```

or if images are in `Vendor/theme/web/images/`

```
<img src="<?php echo $block->getViewFileUrl('images/image.png'); ?>" ?>
```

For CMS Static Blocks or Pages:

Images in `Vendor/theme/Vendor_Module/web/images` or `app/code/Vendor/Module/view/frontend/web/images/`

```
<img src="{{view url='Vendor_Module::images/images.png'}}"/>
```

or if images are in `Vendor/theme/web/images/`

```
<img src="{{view url='images/image.png'}}"/>
```
