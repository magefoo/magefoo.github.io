---
layout: post
title: "Enabling and Using the Magento 2 Profiler"
date: 2017-09-10 13:19:08
image: '/assets/img/'
description: 'Enabling and Using the M2 Profiler'
main-class: m2
color: '#222222'
tags:
- Magento 2
- Profiling
categories:
- Magento 2
twitter_text:
introduction: 'Enabling and Using the M2 Profiler'
---

### What is a profiler and why do I need to use one?

If we are going to understand why it is important to use a profiler, we need to begin with talking about performance. In today's ecommerce market performance is just as important, if not moreso than having any errors in your Magento store.  First, errors deteriorate the confidence of consumers in purchasing products from your store.  If a consumer notices an error, they will assume many things about your site, most likely disuading them from making a purchase at all.  When a potential
customer visits your site, they will be entering in the most private and protected information to make a purchase; their credit card number and all the details which coincide with it to allow for a successful purchase.  If they encounter an error, their confidence in the site becomes suspicious, and most will likely not take the chance to enter any confidential information at all, thus no sale.  I mean I certainly wouldn't. If a store owner cannot take the time, nor the effort, to
ensure their store is completely error free and functioning properly, then me as a consumer will lose all faith in doing any business with that store.  This does not just affect a single transacton, they lose me as a customer for life. Unfortunately, I am not the exception to the rule, but the normality. Not only do you lose that one transaction, you lose any future transactions as well.  This is why it is of the utmost importance to keep your store error free.

Performance is another metric used by consumers to judge a store. If the site is slow or sluggish, it will indicate to a consumer that the store is not a professional store, or possibly they will go look for a store they trust more due to the slow load times associated with a site.

#### So, how do we find the bottlenecks in the site?

*NOTE: Always create a dev/staging copy of your site, never work on the production site*

Magento 2 has a great builtin profiler, much better than Magento 1's. If using Nginx, it is preferred to enable the profiler by doing the following:

In the file `nginx.conf` or other `.conf` file where your Magento nginx config resides, add the following directly under  `location ~ (index|get|static|report|404|503)\.php$ {`

so it looks like below:

```
# PHP entry point for main application
location ~ (index|get|static|report|404|503)\.php$ {
    try_files $uri =404;
    fastcgi_pass   fastcgi_backend;
    fastcgi_buffers 1024 4k;

    fastcgi_read_timeout 600s;
    fastcgi_connect_timeout 600s;
    fastcgi_param MAGE_PROFILER $MAGE_PROFILER;
    fastcgi_param MAGE_MODE $MAGE_MODE;

    fastcgi_index  index.php;
    fastcgi_param  SCRIPT_FILENAME  $document_root$fastcgi_script_name;
    include        fastcgi_params;
}



```

next, add the following to your site's `nginx.conf` file under the server block for that site:

```
server {
listen 80;
listen 443 ssl;
ssl_certificate /etc/ssl/certs/local/local.pem;
ssl_certificate_key /etc/ssl/certs/local/local.key;
server_name example.com;
set $MAGE_MODE developer;
set $MAGE_PROFILER html;
include /example.com/html/www/nginx.conf;
}
```

reload nginx;

We have set the site in developer mode and enabled the profiler in Nginx.

If you have Apache, you can add the following directly to the .htaccess file:

```
SetEnv MAGE_PROFILER html
SetEnv MAGE_MODE developer
```

Flush the cache and reload the page.

If you do not want anyone else but yourself to see the data, you can add your IP to the allowed IP list.

```
bin/magento maintenance:allow-ips 1.1.1.1
# change 1.1.1.1 to your public IP
```

After the profiler is enabled, you will see at the bottom of the page, the load times and memory consumption of the function calls.

You will see columns for `TIME`, `AVG`, 'CNT', `EMALLOC`, `REALMEM`

Looking at the `TIME` and `AVG` columns we can see which function calls are taking the longest.

We can then track down the code in the codebase to see if any of the common Magento Code Mistakes have been made.

Common Magento Code Mistakes:
1. Calling the database directly.
2. Calling an unfiltered collection and using foreach to go through it one by one.
3. using count() as an argument in foreach loop.
4. Instantiating a model in a foreach loop.
5. Calling a collection in a foreach loop.

Generally if you see a foreach loop or SQL query that is most likely the issue.

### Using [blackfire.io](https://blackfire.io)

I have been very impressed with blackfire.io profiler. I do recommend using it to profile Magento's sites, it offers many more options and graphs to view the profiled site.

I will be adding to this article in the future so check back often!


