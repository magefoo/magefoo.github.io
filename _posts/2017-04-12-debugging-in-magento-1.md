---
layout: post
title: "Debugging in Magento 1"
date: 2017-04-12 11:18:03
image: '/assets/img/'
description: "Debugging Magento can be a nightmare, here are some tips."
main-class: 'magento 1'
color: '#B31917'
tags:
- Magento 1
categories:
- Magento 1
- Debugging
- Xdebug
twitter_text: 'Debugging in Magento 1'
introduction: "Debugging in Magento can be a nightmare, here are some tips."
---

Debugging a Magento issue can be complicated.  Here are some tips when debugging in Magento 1.

Always tail the appropriate logs when debugging, if an error happens and a log entry is created, it is easier to see exactly when the log entry was created.

*note: all commands in this blog will be for Linux and Vim*

`tail -f var/log/system.log var/log/exceptions.log /path/to/<php-fpm log file>`


*more to come*
