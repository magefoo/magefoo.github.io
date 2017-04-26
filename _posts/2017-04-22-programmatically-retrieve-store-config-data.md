---
layout: post
title: "Programmatically Retrieve Store Config Data"
date: 2017-04-22 13:11:07
image: '/assets/img/'
description: 'Get the data you need from backend configuration'
main-class: 'quickie'
color: '#000000'
tags:
- Magento 2
- Dev
- Quickie
categories:
- Magento 2
- Quickie
twitter_text: 'Get the data you need from backend configuration'
introduction: "Get the data you need from backend Configuration"
---

To pull in config data to your module, I personally like to make a helper function to do so.

```
#app/code/Vendor/Module/Helper/Data.php

<?php

namespace Vendor\Module\Helper;

use Magento\Framework\App\Helper\AbstractHelper;

class Data extends AbstractHelper
{
    public function getConfig($config_path)
    {
        return $this->scopeConfig->getValue(
            $config_path,
            \Magento\Store\Model\ScopeInterface::SCOPE_STORE
        );
    }
}
```

After creating the function in the helper, if the helper is not already injected in the file where you need to pull the config data, you will need to inject it like so.


```
#app/code/Vendor/Module/Model/<filename>/php

<?php

namespace Vendor\Module\Model;

use Vendor\Module\Helper\Data as Helper;

class SomeClass
{
    protected $_helper;

    public function __construct(Helper $helper)
    {
        $this->_helper = $helper;
    }

    public function getEmailAddress()
    {
        return $emailAddress = $this->_helper->getConfig('general/contact/email');
    }
}
```

To find the format of the configuration data you need to retrieve, go to the core module, for which the data needed is, and look in `vendor/magento/module-<name>/etc/adminhtml/system.xml` the format will be `section/group/field` so you are looking for `<section id="<section_name>"...`, `<group id="<group_name>"...`, and `<field id="<field_name"...`, just grab the id's and put them in order as the getConfig param i.e. `$this->helper->getConfig('section/group/field');`


