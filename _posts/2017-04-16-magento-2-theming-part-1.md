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

In Magento 2, to extend layout files their is new syntax that we need to learn. This new syntax can be found in the [Magento DevDocs: Layout instructions](http://devdocs.magento.com/guides/v2.1/frontend-dev-guide/layouts/xml-instructions.html).

Use layout instructions to:
+  move a page element to another parent element
+  add content
+  remove a page element

This is where it takes some practice, and a lot of examples to learn how to use the new syntax of the Layout Instructions.

First though, we need to know where to put these instructions.

We put the instructions in a file of the same name and under the same directory structure as the original we are trying to extend.

Instead of rewriting everything in the [Magento DevDocs: Layout file types](http://devdocs.magento.com/guides/v2.1/frontend-dev-guide/layouts/layout-types.html) I'll just link to them as they do a very good job in describing everything you need to know.

The main take away is their are three types of layout files.
+ Page Layout
+ Page Configuration
+ Generic

There is also different ways we declare each one of these and where they are found.

We put Page Layout files generally under `<theme_dir>/<Namespace>_<Module>/layouts.xml`.

Page layouts are the highest overview and change thing such as the 1 column, 2 column, with/without sidebar, etc.

Generally these aren't changed, especially for a basic customized theme, but we add them here for clarification.  The layout file we really are interested in is the Page Configuration layout files.

For Page Layout, we start the file with the ` <page_layouts xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="urn:magento:framework:View/PageLayout/etc/layouts.xsd">` scheme informtion, and continue with the `<layout></layout>` instruction.

Here is an example:

```
<page_layouts xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="urn:magento:framework:View/PageLayout/etc/layouts.xsd">
    <layout id="1column">
        <label translate="true">1 column</label>
    </layout>
    <layout id="2columns-left">
        <label translate="true">2 columns with left bar</label>
    </layout>
    <layout id="2columns-right">
        <label translate="true">2 columns with right bar</label>
    </layout>
    <layout id="3columns">
        <label translate="true">3 columns</label>
    </layout>
</page_layouts>
```

What we are really interested in, is the Page Configuration layout files, these are what change the blocks inside of the `<body>` tag.

For themes, these will be found in `<theme_dir>/<Namespace>_<Module>/layout/` directory structure and named the same as the parent, such as in the Magento_Theme name space, we would name our file `default.xml`.

Where did `default.xml` come from? It isn't in the parent blank theme?  Well, remember I said earlier, the final fallback of themes is actually `module-theme`, so if we look in `vendor/magento/module-theme/view/frontend/layout/` you will see the file we need to extend so we create a new file in `<theme_dir>/Magento_Theme/layout/default.xml`

In `default.xml`, we then start with the xml schema:

`<page xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="urn:magento:framework:View/Layout/etc/page_configuration.xsd">` 

and continue with one of the allowed instructions, such as the `<body></body>` instruction. 

For a list of allowed instructions we can use in page configuration files see the [Page configuration structure and allowed layout instructions section of the
DevDocs](http://devdocs.magento.com/guides/v2.1/frontend-dev-guide/layouts/layout-types.html)

It is pretty simple to remove items, we just find the handle of the block we want to remove and add the remove instruction like in the example below:

```
<page xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="urn:magento:framework:View/Layout/etc/page_configuration.xsd">
    <body>
        <referenceBlock name='report.bugs' remove='true'/>
        <referenceBlock name='top.search' remove='true' />
    </body>
</page>
```

But what if we do not want to simply remove the item, but move it to another parent block?  We use the `<move>` instuction to do this.

We select the handle we want to move and the parent we want to move it under. Then we declare it like so `<move element="minicart" destination="top.container">`

So, then our above example would then become:

```
<page xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="urn:magento:framework:View/Layout/etc/page_configuration.xsd">
    <body>
        <referenceBlock name='report.bugs' remove='true'/>
        <referenceBlock name='top.search' remove='true' />
        <move element='minicart' destination="top.container" />
    </body>
</page>
```

To create a new block, we would add it like so:

```
<block class="Magento\Catalog\Block\Product\View\Description" name="product.info.sku" template="product/view/attribute.phtml" after="product.info.type">
    <arguments>
        <argument name="at_call" xsi:type="string">getSku</argument>
        <argument name="at_code" xsi:type="string">sku</argument>
        <argument name="css_class" xsi:type="string">sku</argument>
    </arguments>
</block>
#example from devdocs
```

### These are simple, but powerful, methods to change the layout.  

They do get more complicated, which we will get into in another article.

Next, we will touch on generic layout files.  The location of these files for themes will be `<theme_dir>/<Namespace>_<Module>/layout` and use the `<layout></layout>` instruction.  These are the ones that closest resemble what we remember in Magento 1.  We select a section of the block via a name such as "root" or "content" and add another block under it.

An example from the DevDocs would be:

```
<layout xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="urn:magento:framework:View/Layout/etc/layout_generic.xsd">
    <update handle="formkey"/>
    <update handle="adminhtml_googleshopping_types_block"/>
    <container name="root">
        <block class="Magento\Backend\Block\Widget\Grid\Container" name="googleshopping.types.container" template="Magento_Backend::widget/grid/container/empty.phtml"/>
    </container>
</layout>
```

Where class would be block type in Magento 1 and template is like the same in Magento 1, except obviously the declaration syntax of both is different.

We link to the [Magento DevDocs again, for some final examples](http://devdocs.magento.com/guides/v2.1/frontend-dev-guide/layouts/xml-manage.html)

In the end, it takes some practice and some understanding to get a handle on layout templates, after understanding them, they do become relatively easy to use, much like Magento 1 layout files.

I cannot stress enough Magento's DevDocs, the documention for Magento 2 is a whole lot better than it was for Magento 1, if you follow along in the DevDocs, there isn't much that isn't explained, but it does take time and patience to understand. Better to do it right, than create situations in your storefront that cause errors and performance issues.
