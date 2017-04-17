---
layout: post
title: "Adding log statements in Magneto 2"
date: 2017-04-16 20:09:42
image: '/assets/img/'
description: 'Just q quickie on how to log specific thing in Magento 2'
main-class: 'Magento 2'
color: '#000000'
tags:
- Magento 2
- Debugging
categories:
- Magento 2
- Debugging
twitter_text: 'Adding log statements in Magento 2'
introduction: "Just a quickie on how to add a log statement in Magento 2"
---

In Magento 1, logging was pretty simple, we add `Mage:log($var, null, 'test.log');` anywhere you needed to know something.

With dependancy injection in Magento 2, things are a little different. 

There are two main ways to log things.

Injecting `\Psr\Log\LoggerInterface` or using the `\Zend\Log\Writer` class.

Normally, if the logger is already injected, I'll just hijack it and use it:

```
$this->logger->debug($var);
```

If not, just declare it and use it:

```
\Magento\Framework\App\ObjectManager::getInstance()
    ->get('Psr\Log\LoggerInterface')->debug('message');
```

But what if you are outside the scope of `\Psr\Log\`? Use the simple, inject `\Zend\Log\` like below: 

```
$writer = new \Zend\Log\Writer\Stream(BP . '/var/log/test.log');
$logger = new \Zend\Log\Logger();
$logger->addWriter($writer);
$logger->info('Your text message');
```

How do you know if the logger is already injected?

Look for the `$logger` variable in the class you are attempting to log from, and in the constructor.

```
 /**
  * @var \Psr\Log\LoggerInterface
  */
 protected $_logger;


 public function __construct(
         \Magento\Framework\Model\ResourceModel\Db\Context $context,
         \Magento\Store\Model\StoreManagerInterface $storeManager,
         \Magento\Eav\Model\Config $eavConfig,
         Product $productResource,
         \Magento\Catalog\Model\Category $catalogCategory,
         \Psr\Log\LoggerInterface $logger,
         $connectionName = null
     ) {
         $this->_storeManager = $storeManager;
         $this->_eavConfig = $eavConfig;
         $this->productResource = $productResource;
         $this->_catalogCategory = $catalogCategory;
         $this->_logger = $logger;
         parent::__construct($context, $connectionName);
     }
```

If this is the case, then just hijack it and use: 

```
$this->logger->debug($var);
```

