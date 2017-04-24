---
layout: post
title: "Magento 2 Getting Image URL for template files and CMS Blocks"
date: '2017-04-23 17:20:13'
image: '/assets/img/'
description: 'Magento 2 Getting an Image URL.'
main-class: 'm2'
color: '#000000'
tags:
- Magento 2
- Dev
- Quickies
categories:
- Magento 2
- Quickies
twitter_text: 'Magento 2 Getting an Image URL, the correct way.'
introduction: "Magento 2 Getting an Image URL the correct way"
---

Just a quick note on how to get image urls in both template files and cms blocks for Magento 2.

For template files:

Images in `Vendor/theme/Vendor_Module/web/images` or `app/code/Vendor/Module/view/frontend/web/images/`

```
<img src="<?php echo $this->getViewFileUrl('Vendor_Module::images/image.png'); ?>" />
```

or if images are in `Vendor/theme/web/images/`

```
<img src="<?php echo $block->getViewFileUrl('images/image.png'); ?>" />
```

For CMS Static Blocks or Pages:

Images in `Vendor/theme/Vendor_Module/web/images` or `app/code/Vendor/Module/view/frontend/web/images/`

```
<img src="{{ "{{view url='images/image.png'"}}}}"/>
```

or if images are in `Vendor/theme/web/images/`

```
<img src="{{ "{{view url='images/image.png'"}}}}"/>
```

That is it, so next time you need to get that image in place.
