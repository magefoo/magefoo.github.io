---
layout: post
title: "Magento 1 - Source Model for Dropdown in Admin Configuration of Custom Attribute"
date: 2018-07-19 07:03:41
image: '/assets/img/'
description: 'Create a souce model for configuration dropdown from an attribute'
main-class: 'magento1'
color: '#B21917'
tags:
- Magento 1
- Source Model
categories:
- Magento 1
- Development
twitter_text: 'Create a souce model for configuration dropdown from an attribute'
introduction: 'Create a souce model for configuration dropdown from an attribute'
---

This is just a quickie of how to create a Source Model from an Attribute you want to display in a module settings.

First add the source_model to your `etc/system.xml` file.

```
<?xml version="1.0"?>
<config>
    <sections>
        <filoupsell translate="label" module="filoupsell">
            <class>separator-top</class>
            <label>Filofax Yearly Upsells</label>
            <tab>filolocale</tab>
            <frontend_type>text</frontend_type>
            <sort_order>7000</sort_order>
            <show_in_default>1</show_in_default>
            <show_in_website>1</show_in_website>
            <show_in_store>1</show_in_store>
            <groups>
            	<general translate="label">
                    <label>Filofax Upsell General</label>
                    <frontend_type>text</frontend_type>
                    <sort_order>10</sort_order>
                    <show_in_default>1</show_in_default>
                    <show_in_website>1</show_in_website>
                    <show_in_store>1</show_in_store>
                    <fields>
                        <enable translate="label">
                            <label>Enable/Disable</label>
                            <comment>Enable Filofax Yearly Upsell Banner on Product View Page</comment>
                            <frontend_type>select</frontend_type>
                            <source_model>adminhtml/system_config_source_yesno</source_model>
                            <sort_order>10</sort_order>
                            <show_in_default>1</show_in_default>
                            <show_in_website>1</show_in_website>
                            <show_in_store>1</show_in_store>
                        </enable>
                        <year translate="label">
                            <label>Year upsell applies to</label>
                            <comment>Enter year of the product you want banner to show</comment>
                            <frontend_type>select</frontend_type>
                            <source_model>filoupsell/system_config_source_year</source_model>
                            <sort_order>20</sort_order>
                            <show_in_default>1</show_in_default>
                            <show_in_website>1</show_in_website>
                            <show_in_store>1</show_in_store>
                        </year>
                    </fields>
                </general>
            </groups>
        </filoupsell>
    </sections>
</config>
```

In the above example I want to show a dropdown of a product attribute. So I set `<frontend_type>select</frontend_type>` and `<source_model>filoupsell/system_config_source_year</source_model>` 
NOTE: filoupsell is the shortname of the module in `config.xml` and `system_config_source_year` is the directory structure or path under Model so your file would be `Model/System/Config/Source/Year.php`.

Then in your file `Year.php` you would have the following code as an example I am pulling a product attribute called `year`.

```
// Model/System/Config/Source/Year.php

<?php

class Filofax_Upsells_Model_System_Config_Source_Year
{

    protected $_options;

    public function getAllOptions($withEmpty = true, $defaultValues = false)
    {

        $attribute = Mage::getModel('eav/entity_attribute')
            ->loadByCode(Mage_Catalog_Model_Product::ENTITY, 'year');

        return $attribute->getSource()->getAllOptions();

    }

    public function toOptionArray($isMultiselect = false)
    {
        if (!$this->_options) {
            $this->_options = $this->getAllOptions();
        }

        $options = $this->_options;

        if (!$isMultiselect) {
            array_unshift($options, array('value' => '', 'label' => Mage::helper('adminhtml')->__('--Please Select--')));
        }

        return $options;
    }
}
```

As you can see, I am using the `eav/entity_attribute` model to load the attribute by attribute code. Then returning the attribute options to `toOptionArray()` which is then called by Magento in configuration to list the options.

![Magento Module Configuration Dropdown Menu](assets/img/magento_configuration_module_settings.png)
