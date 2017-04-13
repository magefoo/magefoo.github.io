---
layout: post
title: "Using Xdebug and Qcachegrind to Profile in Magento 1"
date: 2017-04-12 11:18:03
image: '/assets/img/'
description: "Using Xdebug and Qcachegrind to profile a Magento 1 store."
main-class: 'magento 1'
color: '#B31917'
tags:
- Magento 1
categories:
- Magento 1
- Debugging
- Xdebug
twitter_text: 'Debugging in Magento 1'
introduction: "Debugging performance issues in Magento can be a nightmare, here are some tips."
---

Lets face it, not all developers are created equal.  Moreso, Magento being such a popular platform, suddenly everyone thinks they are a Magento developer, same thing happened with the Wordpress platform as well.  People who should not even call themselves developers are on sites like Upwork, Freelancer, and PeoplePerHour, hawking there substandard and, quite frankly, dangerous, services.  These freshers that have no idea what they are doing, however, from reading their profiles you
cannot tell this, they have good multiple reviews, they have projects, is that good enough? Well you have to think, the people reviewing them have absolutely no idea what code they wrote.  They have no idea if it is spagetti code or legitimate code.  Sure, their function which they hired that person for may "work", but does it work in all corner cases? Is it optimized, did their normally fast site suddenly start taking many seconds to load in certain areas? When they left the review, it
was most likely a cursory user functionality test of what they implemented. The reviewer probably wouldn't notice the performance degredation, or other issues until well after the review was left.

*If performance and speed matter to you, do not hire from these freelancer sites. Period.*

I only make the above comment due to the statistics of finding a Magento programmer that actually knows Magento inside and out and can spot these performance bottlenecks and fix them, most senior Magento Specialists already have jobs that pay them way more than what they can get on these sites.

There is one site however that is trying to put together the real Magento Specialists in one place, it is called [commercehero.io](https://commercehero.io).

Sadly, I have even seen agencies implement code that severely impacts performance in Magento, so it isn't limited to just freelancers.  So, one needs to do their homework thoughly, ensure they are hiring a dev that knows how to spot bad performance inhibiting code.

Ok, I am off my soapbox, for now.  This article is an attempt to let the average person see what it takes to profile a site for performance and then track down the code that is causing the performance hit.

The best place to start, is reading this document that was released from Magento themselves.  It explains the five most common coding mistakes that degrade performance.  This list is by no means exhaustive, and I have seen much more than just these five issues in the wild. But here it is.  Read it. Live it.
[Conquer the 5 most common Magento coding issues to optimize your site for performance](http://info2.magento.com/rs/magentosoftware/images/Conquer_the_5_Most_Common_Magento_Coding_Issues_to_Optimize_Your_Site_for_Performance.pdf). Just in case this link ever disappears, you can get a copy
[here](https://www.dropbox.com/s/7d16uz2ap1ucirq/Conquer_the_5_Most_Common_Magento_Coding_Issues_to_Optimize_Your_Site_for_Performance%20%281%29.pdf?dl=0).

### This article is about profiling in Magento 1.x only, another article will cover Magento 2 as they are vastly different and Magento 2 has many more tools available to help profile the site, one being this free and well put together extension from Mirasvit, [Mirasvit module profiler](https://github.com/mirasvit/module-profiler).

Second, I would like to mention, never make changes on a live site, copy your live site to a development environment and use the development environment to do your profiling and tests.  Profiling slows down site performance in itself, so having profiling tools working as the site is running, will slow the site down quite a bit, so you've been warned, do not profile a live site, unless it is a quick profile and it is turned off afterward.

There are two different kind of profiling tools, active and passive.  Active are much more intrusive, and cause much more slowness than passive. An example of an active profiler is [Xdebug](https://xdebug.org/).  Passive profilers are actually made to stay on during operation and gather information and use up less resources, are less intrusive, but do have less information available.  An example of a passive profiler is [xhprof](https://github.com/phacility/xhprof) and its
counterpart, [xhgui](https://github.com/preinheimer/xhprof).

I tend to like Xdebug for profiling, only because it is easier to setup on a remote server and it creates a single file which I can download and analyze in [Kcachegrind](https://kcachegrind.github.io/html/Shot1.html) or Qcachegrind for Gnome folks.

I am concentrating this article on Xdebug using Qcachegrind, there will be another article for xhprof and xhgui.  To install xdebug, you can follow these well written instructions here: [Installing Xdebug and configuring for PHP profiling purposes](http://www.weasy.net/2015/02/installing-xdebug-and-configuring-for.html).

Next install Kcachegrind or Qcachegrind, there are plenty of howto's on the internet for this, so I will not go into detail, in Arch Linux, it is as simple as `sudo pacman -S qcachegrind`

*note: when enabling `xdebug.profiler_enable=1` you will be enabling for the entire server and it will slow load times down immensely!* 

Instead, I prefer to use a chrome extension called [xdebug helper](https://chrome.google.com/webstore/detail/xdebug-helper/eadndfjplgieldjbigjakmdgkmoaaaoc) and instead of `xdebug.profiler_enable=1` I use `xdebug.profiler_enable_trigger=1` together with `xdebug.profiler_enable_trigger_value=12345`, then putting that value into the options of the xdebug
helper chrome extension, will only trigger the profiler when turned on in the extension, this way you do not end up with hundreds of files to sift through trying to find the one that you intended to profile.  Below is my xdebug ini:

```
zend_extension=xdebug.so
xdebug.remote_enable=on
xdebug.remote_host=127.0.0.1
xdebug.remote_port=9000
xdebug.remote_handler=dbgp
xdebug.profiler_enable_trigger=1
xdebug.profiler_enable_trigger_value=151
xdebug.profiler_output_name=callgrind.out.%t
xdebug.profiler_output_dir=/home/<user>/php_profile
xdebug.profiler_append=1
```

Generally, servers that are used to to serve ecommerce sites are headless, meaning, they do not have a GUI to navigate or run programs that depend on graphics.  So I install xdebug on the server which has the site I want to profile, and Qcachegrind on my local computer. I then rsync the files that were created when I navigated to the site with Profiling enabled in Xdebug Helper Chrome Extension, if you notice these files are saved in the directory I stated in
`xdebug.profiler_output_dir` or `/home/<user>/php_profile/` Another note, the output file name must start with `callgrind` such as what I have in `xdebug.profiler_output_name=callgrind.out.%t` this is due to the Qcachegrind program only seeing files called callgrind.

So, once you have your callgrind file downloaded from the server and opened in Qcachegrind you will see something similiar below. It takes a little while to get used to reading the results, so do not fret if you do not understand immediately what all this information means.

![Qcachegrind Output](http://imgur.com/a/VFuQU)

Finally, the steps to find what you are looking for.

Start by sorting "Self" or "Incl." in the function-list, this will put the functions that take the longest on top.
Find the function that takes the most time to load, checking both "self" and "incl" and not the one with the biggest "incl."
Follow the bread crumbs from there.

Note, Qcachegrind's Source code view is awesome, however only works if the application being profiled is on the same computer as where the profile file originated. THus Rsync'ing the file from a remote computer to your local one, will disable the ability to use the Source code viewing functionality of Qcachegrind, so you will have to manually open the file with the bad code and look for it, chances are you will find something horrible in a foreach loop like below:

```
 foreach($where as $_id) {
        $product = Mage::getModel('catalog/product')->load($_id);
        if ( $product->getVisibility() == 1 ) {
                $parent_ids = Mage::getResourceSingleton('catalog/product_link')
                           ->getParentIdsByChild($_id, Mage_Catalog_Model_Product_Link::LINK_TYPE_GROUPED);
                if ( ! $parent_ids ) {
                        $parent_ids = Mage::getModel('catalog/product_type_configurable')->getParentIdsByChild($_id);
                }
                if ( isset( $parent_ids[0] ) ) {
                        $parents[] = $parent_ids[0];
                }
        } else {
                $parents[] = $_id;
        }
}
```

The above code is the type of code that will destroy the load times on your store, this is a real example from a customer who hired a dev from Upwork.
