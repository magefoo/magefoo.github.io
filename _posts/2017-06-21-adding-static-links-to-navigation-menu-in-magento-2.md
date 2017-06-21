---
layout: post
title: "Adding static links to Navigation Menu in Magento 2"
date: 2017-06-21 13:02:38
image: '/assets/img/navlinks.png'
description: 'Adding a static link such as home in navigation menu'
main-class: 'm2'
color: '#000000'
tags:
- Magento 2
- Dev
- Module
categories:
- Magento 2
twitter_text: 'Adding Static Links to Navigation menu in Magento 2'
introduction: "Adding Static Links to Navigation Menu in Magento 2"
---

![image](http://magefoo.com/assets/img/navlinks.png)

The Navigation Menu in Magento 2 shows all the Categories you want it to from the Admin section, and can be controlled from Products->Categories.  But say you want to add a static link, such as a home button?  You could create an empty category for this and change the url rewrite to use `/` but we are devs, and we like doing things better.

I see a bunch of blog posts about creating an Observer, however this is not necessarily the way to go, nor is it a valid way since Magento 2.1, so we will create a Plugin instead, much like you can find in the core code `vendor/magento/module-catalog/`

An easy way to add static links to the Navigation Menu is to create a module with a plugin.  This is where that Dependancy Injection everyone talks about comes into play.

So lets create our module first.

Our composer.json file

```
#app/code/Magefoo/NavLinks/composer.json

{
    "name": "magefoo/navlinks",
    "description": "N/A",
    "authors": [
        {
            "name": "David Ford",
            "email": "djfordz@gmail.com",
            "homepage": "http://magefoo.com"
        }
    ],
    "require": {
        "php": "~5.5.0|~5.6.0|~7.0.0",
        "magento/module-ui": "100.1.*"
    },
    "type": "magento2-module",
    "version": "1.0.0",
    "license": [
        "OSL-3.0",
        "AFL-3.0"
      ],
    "autoload": {
        "files": [ "registration.php" ],
        "psr-4": {
            "Magefoo\\NavLinks\\": ""
        }
    }
}
```
Our registration.php file.

```
#app/code/Magefoo/NavLinks/registration.php

<?php
\Magento\Framework\Component\ComponentRegistrar::register(
    \Magento\Framework\Component\ComponentRegistrar::MODULE,
    'Magefoo_NavLinks',
    __DIR__
);
```

Our module.xml

```
#app/code/Magefoo/NavLinks/etc/module.xml

<?xml version="1.0"?>
<config xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="urn:magento:framework:Module/etc/module.xsd">
    <module name="Magefoo_NavLinks" setup_version="1.0.0">
        <sequence>
            <module name="Magento_Ui"/>
        </sequence>
    </module>
</config>
```

Now lets create the di.xml file that will inject our plugin in the right place

```
# app/code/Magefoo/NavLinks/etc/frontend/di.xml

<?xml version="1.0"?>
<config xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="urn:magento:framework:ObjectManager/etc/config.xsd">

    <type name="Magento\Theme\Block\Html\Topmenu">
        <plugin name="navLinksTopmenu" type="Magefoo\NavLinks\Plugin\Block\Topmenu" sortOrder="0" />
    </type>
</config>
```

and finally, our Plugin file

```
# app/code/Magefoo/NavLinks/Plugin/Block/Topmenu.php

<?php

namespace Magefoo\NavLinks\Plugin\Block;

use Magento\Framework\Data\Tree\NodeFactory;

class Topmenu
{

    /**
     * @var NodeFactory
     */
    protected $nodeFactory;

    /**
     * Initialize dependencies.
     *
     * @param \Magento\Framework\Data\Tree\NodeFactory $nodeFactory
     */
    public function __construct(
        NodeFactory $nodeFactory
    ) {
        $this->nodeFactory = $nodeFactory;
    }

    /**
     *
     * Inject node into menu.
     **/
    public function beforeGetHtml(
        \Magento\Theme\Block\Html\Topmenu $subject,
        $outermostClass = '',
        $childrenWrapClass = '',
        $limit = 0
    ) {
        $node = $this->nodeFactory->create(
            [
                'data' => $this->getNodeAsArray(),
                'idField' => 'id',
                'tree' => $subject->getMenu()->getTree()
            ]
        );
        $subject->getMenu()->addChild($node);
    }

    /**
     *
     * Build node
     **/
    protected function getNodeAsArray()
    {
        return [
            'name' => __('Home'),
            'id' => 'home',
            'url' => '/',
            'has_active' => true,
            'is_active' => true
        ];
    }
}
```


That is it! Easy as one-two-three.

