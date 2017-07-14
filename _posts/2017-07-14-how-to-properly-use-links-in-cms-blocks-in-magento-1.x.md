---
layout: post
title: "How to properly use links in CMS Blocks in Magento 1.x"
date: 2017-07-14 12:49:49
image: '/assets/img/'
description: 'Properly linking content in cms pages for Magento 1'
main-class: 'magento1'
color: `#759dc8`
tags:
- Magento 1
- CMS
categories:
- Magento 1
twitter_text:
introduction: 'Properly link content in Magento 1'
---

A lot of store owners just hard-code the store's URL into links in CMS Blocks.  This is a bad idea for many reasons, among them being, if switching to https, it will cause mixed-content on the page, setting up a dev store would link all content to the live site, etc.

Here are the proper ways to link content for Magento 1. I have another article which explains the same for Magento 2 called [Magento 2 Getting Image URL for template files and CMS Blocks](http://magefoo.com/getting-image-url-for-template-files/).

### For Magento 1.x this is how we should link images, categories, or products in CMS Blocks.

For images:

```
<img src={{ "{{media url='wysiwyg/filename.jpg'"}}}}" alt='image' />
```

*Paths are relative to the `media/` directory.*

Then we have th `url` and `direct_url` attributes.

`url` generates a URL using Magento routing.  It will take anything you pass to the store url directive append it to the store base URL and appne a `/`.

examples:

```
<a href="{{ "{{store url=contacts'}}}}">Contact Us</a>
```

If you need to create a URL to a category or product, you can use the direct_url parameter. In that case, whatever you pass to the store directive will simply be appended to the base URL

For a category page:

```
<a href="{{ "{{store direct_url='category_url.html'"}}}}">category</a>
```

*store _direct gives the base url of the site*

If you need to generate URL's with query parameters, there are two ways using thei \_query parameter.

```
<a href="{{ "{{store direct_url='category/subcategory.html' _query='a=param_a&b=param_b'}}}}">Our Latest Range</a>
```

or

```
<a href="{{ "{{store direct_url='category/subcategory.html' _query_a='param_a' _query_b='param_b'}}}}">Our Latest Range</a>
```

For blocks:

```
"{{ "{{block type="cms/block" block_id="staticblock_id"}}}}"
```

or

```
"{{ "{{block type="catalog/navigation" name="catalog.category.list" template="catalog/navigation/category.phtml"}}}}"
```

Taken mostly from [http://magebase.com/magento-tutorials/quick-tip-using-the-store-template-directive-in-magento/](http://magebase.com/magento-tutorials/quick-tip-using-the-store-template-directive-in-magento/)
