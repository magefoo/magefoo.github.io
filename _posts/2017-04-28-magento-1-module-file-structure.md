---
layout: post
title: "Magento 1 Module File Structure"
date: '2017-04-28 01:48:04'
image: '/assets/img/'
description: "File structure for Magento 1 Module"
main-class: `magento1`
color: `#778090`
tags:
- Magento 1
- Quickies
- Cheatsheet
categories:
- Magento 1
- Quickies
twitter_text: 'Magento 1 Module File Structure'
introduction: "Magento 1 Module File Structure"
---

Magento 1 Cheatsheet of Module File Structure.

```
# app/etc/modules/Vendor_Module.xml

<?xml version="1.0"?>
<config>
    <modules>
        <Vendor_Module>
            <active>true</active>
            <codePool>local</codePool>
            <depends>
                <Vendor_Module />
            </depends>
        </Vendor_Module>
    </modules>
</config>
```

```
# app/code/local/Vendor/Module/etc/confix.xml

<?xml version="1.0"?>
<config>
    <modules>
        <Vendor_Module>
            <version>1.0.0</version>
        </Vendor_Module>
    </modules>

    <global>
        <blocks>
            <shortname>
                <class>Vendor_Module_Block</class>
            </shortname>
        </blocks>
        <models>
            <shortname>
                <class>Vendor_Module_Model</class>
            </shortname>
        </models>
        <helpers>
            <shortname>
                <class>Vendor_Module_Helper</class>
            </shortname>
        </helpers>
        <events>
            <observer_event_intercepted>
                <observers>
                    <shortname>
                        <class>shortname/observer</class>
                        <method>nameOfFunction</method>
                        <type>singleton</type>
                    </shortname>
                </observers>
            </observer_event_intercepted>
        </events>
    </global>

    <frontend>
        <routers>
            <shortname>
                <use>standard</use>
                <args>
                    <module>Vendor_Module</module>
                    <frontName>shortname</frontName>
                </args>
            </shortname>
        </routers>

        <layout>
            <updates>
                <shortname>
                    <file><file>.xml</file>
                </shortname>
            </updates>
        </layout>
    </frontend>
```

```
# app/code/local/Vendor/Module/etc/system.xml

<?xml version="1.0"?>
<config>
    <tabs>
        <shortname translate="label" module="customer">
            <label>Magentofu Modules</label>
            <sort_order>410</sort_order>
        </shortname>
    </tabs>
    <sections>
        <shortname translate="label" module="fuadvdel">
            <class>seperator-top</class>
            <label>Advanced Delete</label>
            <tab>shortname</tab>
            <sort_order>10</sort_order>
            <show_in_default>1</show_in_default>
            <show_in_website>1</show_in_website>
            <show_in_store>1</show_in_store>
            <groups>
                <general translate="label">
                    <label>Advanced Delete Options</label>
                    <sort_order>10</sort_order>
                    <show_in_default>1</show_in_default>
                    <show_in_website>1</show_in_website>
                    <show_in_store>1</show_in_store>
                    <fields>
                        <enable translate="label">
                            <label>Enable/Disable</label>
                            <frontend_type>select</frontend_type>
                            <source_model>adminhtml/system_config_source_yesno</source_model>
                            <comment>Enable or disable module</comment>
                            <sort_order>20</sort_order>
                            <show_in_default>1</show_in_default>
                            <show_in_website>1</show_in_website>
                            <show_in_store>1</show_in_store>
                        </enable>
                    </fields>
                </general>
            </groups>
        </shortname>
    </sections>
</config>
```

```
# app/code/local/Vendor/Module/etc/adminhtml.xml

<?xml version="1.0"?>
<config>
    <menu>
        <shortname translate="title">
            <title>Advanced Delete</title>
            <sort_order>81</sort_order>
            <children>
                <customers>
                    <title>Customer Delete</title>
                    <sort_order>0</sort_order>
                    <action>adminhtml/adminhtml_customer</action>
                </customers>
                <orders>
                    <title>Delete Orders</title>
                    <action>adminhtml/adminhtml_order</action>
                    <sort_order>10</sort_order>
                </orders>
            </children>
        </shortname>
    </menu>
    <acl>
        <resources>
            <admin>
                <children>
                    <shortname translate="title">
                        <title>Advanced Delete</title>
                        <sort_order>110</sort_order>
                        <children>
                            <customers translate="title">
                                <title>Customer Delete</title>
                                <sort_order>10</sort_order>
                            </customers>
                            <orders translate="title">
                                <title>Order Delete</title>
                                <sort_order>20</sort_order>
                            </orders>
                        </children>
                    </shortname>
                    <system>
                        <children>
                            <config>
                                <children>
                                    <shortname translate="title" module="fuadvdel">
                                        <title>Magentofu Advanced Delete</title>
                                        <sort_order>100</sort_order>
                                    </shortname>
                                </children>
                            </config>
                        </children>
                    </system>
                </children>
            </admin>
        </resources>
    </acl>
</config>
```
