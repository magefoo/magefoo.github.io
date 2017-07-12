---
layout: post
title: "Creating a Magento 2 Observer"
date: 2017-07-12 12:02:22
image: '/assets/img/'
description: 'A skeleton framework for a Magento 2 Observer'
main-class: 'm2'
color: '#000000'
tags:
- Magento 2
- Development
categories:
- Magento 2
twitter_text:
introduction: 'Create a Magento 2 Observer'
---

Allthough the concept of the Event/Observer pattern still remains the same between Magento 1 and Magento 2, the way in which one goes about implementing an Observer in Magento 2 is quite different.  We will create a skeleton module reacting to an observed event.

First, as always, create your module skeleton. Refer to the [Magento 2 Module File Structure](http://magefoo.com/magento-2-module-file-structure/) post to create the initial module.

I always find reading the Magento 2 DevDocs the best way in understanding how the code works together in Magento 2 versus any blog post. Magento has spent a lot of time and effort in creating documentation this time around, which if you come from Magento 1, you know that was not always the case.  So head over to the devdocs and read about [Magento 2 Events/Observers](http://devdocs.magento.com/guides/v2.1/extension-dev-guide/events-and-observers.html).

Like in Magento 1, you can manipulate the data that is passed to the Observer, which is one of the best features of using an Observer. Unlike Magento 1.x, observer files are not placed in the Model directory, instead we will be creating a separate directory called `Observer'. So the file structure would be like below.

```
Vendor/Module/
            |
            registration.php
            composer.json
            |
            |
            etc/
            |   |
            |   module.xml
            |   |
            |   frontend/
            |          |
            |          events.xml (for frontend events)
            |
            |
            Observer/
                    |
                    Filename.php
```

It is also important to note, with Magento 2, unlike Magento 1, Configuration files are placed in certain directories to differentiate what part of the Magento 2 framework they will apply to. If we create our `events.xml` file directly under the `etc/` directory, then it would affect both the frontend and backend, however if we create a directory called `frontend/` or a directory called `adminhtml/` then we would place our events.xml file appropriately, depending on what we are observing. In
Magento 1.x this type of compartmentalizing was done inside the config.xml file whereas in Magento 2 it is done inside the directory structure itself.

So, lets create our `events.xml` file. In this example, I have placed it under the directory 'etc/frontend/' as I want to affect the frontend of the shop.

```
<?xml version="1.0"?>
<config xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="urn:magento:framework:Event/etc/events.xsd">
    <event name="name_of_event_observed">
        <observer name="myObserverName" instance="Vendor\Module\Observer\Filename" />
    </event>
    # these event groups can continue for as many as you want to have observers for.
    <event name="name_of_event_observed">
        <observer name="myObserverName" instance="Vendor\Module\Observer\AnotherFilename" />
    </event>
</config>
```

The above is self explanatory. You name the event you would like to observer in the event tag, and you name your observer and namespace in the observer tag.

Personally, if I need to find a certain event I'd like to use, I grep for `eventManager-\>dispatch` which will list the events. Such as `grep -rA1 eventManager-\>dispatch vendor/magento` should do the trick, and you can narrow down the search by simply narrowing the directory you would like to search in.

Now the actual Observer is created in the `Observer` directory.

```
<?php

namespace Vendor\Module\Observer;

use Magento\Framework\Event\ObserverInterface;

class Filename implements ObserverInterface
{
    public function __contruct()
    {
        //Optional function
        //Observer initialization code...
        //You can use dependency injection to get any class this observer may need.
    }

    public function execute(\Magento\Framework\Event\Observer $observer)
    {
        $myEventData = $observer->getData('myEventData');
        //Additional oberser execution code...
        //Mandatory function
    }
}
```

That is all that is needed to create an observer in Magento 2. 
