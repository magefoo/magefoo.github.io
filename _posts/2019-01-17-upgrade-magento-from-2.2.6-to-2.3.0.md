---
layout: post
title: "Upgrade Magento from 2.2.6 to 2.3.0"
date: 2019-01-17 16:33:27
image: '/assets/img/'
description: "Updating to Magento 2.3"
main-class: m2
color:
tags:
- upgrade
- magento 2
- 2.3.0
categories:
- magento 2
twitter_text:
introduction: "Upgrading to Magento 2.3.0"
---

For updating, you are still technically running 2.2 so you will need to use php7.1 after you have updated to 2.3 then you can switch to php 7.2.

Also the upgrade can only be done from 2.2.6 -> 2.3.0

Upgrading from Magento 2.2.6 to 2.3.0 isn't as clear cut as it has been in the past. Please follow the directions in the devdocs to do a proper upgrade to 2.3.0

https://devdocs.magento.com/guides/v2.3/comp-mgr/cli/cli-upgrade.html#upgrade-cli-script

Which is condensed below:

```

1) composer require magento/product-community-edition 2.3.0 –no-update

2) composer config preferred-install dist

3) composer config sort-packages true

4) composer config prefer-stable true

5) composer require –dev friendsofphp/php-cs-fixer:~2.10.0 –no-update

6) composer require –dev sebastian/phpcpd:~3.0.0 –no-update

7) php -r ‘$autoload=json_decode(file_get_contents(“composer.json”), true); $autoload[“autoload”][“psr-4”][“Zend\Mvc\Controller\”]= “setup/src/Zend/Mvc/Controller/”; file_put_contents(“composer.json”, json_encode($autoload, JSON_PRETTY_PRINT|JSON_UNESCAPED_SLASHES));’ (copy this as one line, please)

8) composer update

9) php bin/magento setup:upgrade

10) php bin/magento setup:static-content:deploy

11) php bin/magento setup:di:compile

12) php bin/magento cache:flush
```
