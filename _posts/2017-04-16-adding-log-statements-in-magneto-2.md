---
layout: post
title: "Adding log statements in Magneto 2"
date: 2017-04-16 20:09:42
image: '/assets/img/'
description: 'Just q quickie on how to log specific thing in Magento 2'
main-class: 'dev'
color: '#000000'
tags:
- Magento 2
- Debug
- Dev
categories:
- Magento 2
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

If not, just declare it as a dependancy in the constructor of the class and use it:

```
protected $logger;

public function __construct(\Psr\Log\LoggerInterface $logger)
{
    $this->logger = $logger
}
```

Then you can use where ever you need to log like so:

```
$this->logger->debug($variable);
```

There are multiple log levels associated with Psr Logger they can be found in `LoggerInterface` and are listed below:

```
public function emergency($message, array $context = array());
public function alert($message, array $context = array());
public function critical($message, array $context = array());
public function error($message, array $context = array());
public function warning($message, array $context = array());
public function notice($message, array $context = array());
public function info($message, array $context = array());
public function debug($message, array $context = array());
public function log($level, $message, array $context = array());
```

Also, keep in mind Psr Logger can only log strings not arrays or objects. One can get around this by using `print_r($array, true)` as $message to print arrays, this can also be done with the Zend Logger below as well.


My favorite way to log however, especially during troubleshooting is as follows:


```
$writer = new \Zend\Log\Writer\Stream(BP . '/var/log/test.log');
$logger = new \Zend\Log\Logger();
$logger->addWriter($writer);
$logger->info('Your text message');
```
You can also use the Zend Log when you are outside the scope of `\Psr\Log\`.

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

Lastly, to ensure logs are translated accoringly, wrapping them in the `__()` function is always a good idea and can be done as follows:

```
$this->logger->critical(__("Message to log Error: %1", $exception->getMessage()));
```

The above will translate the message and also parse the getMessage() function if you add it in a catch block.
