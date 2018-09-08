---
layout: post
title: "Building an Admin Grid in the Front End of Magento 2"
date: 2018-09-08 19:15:30
image: '/assets/img/ui_preview2.png'
description: "Easy way to bring the Admin Grid to the Frontend"
main-class: 'dev'
color: '#000000'
tags:
- Admin Grid
- Frontend
- UiComponents
categories:
- Magento 2
- UiComponents
- Frontend
twitter_text: "Sample Module to build admin grid for use on frontend"
introduction: "Sample Module to build admin grid for use on frontend"
---

<img src="http://magefoo.com/assets/img/ui_preview2.png" alt="ui preview" />

Recently, I had the opportunity to take a development test for a very prestigous Magento Agency. I would say for the past year or so all my work has been geared towards Magento 1, so my Magento 2 skillset has been whithering from disuse simply due to my current employer's business needs. All their current shops are built on M1--Although we will be converting them to M2 this year. This was an opportunity to build something interesting in Magento 2 and dust off the Magento 2 skills a bit.

To start off, I will list the specifications laid out for the test.

1. Add a new table that remembers the IP, User Agent, and datetime of every login of each Customer.
2. Add to the Account Dashboard the IP and datetime of their most recent login before the current one.
3. Add to the customer account section a new link on the account navigation that goes to a page that shows a table displaying the customer's login history.
1. Add pagination to this table, similar to the pagination you might find on catalog/search pages.
2. Make the columns of this table sortable by IP, User Agent, and datetime.
4. Bonus 1: Add in geoip lookup to determine the location of the IP of each login, and add that information to 1 and 3 above.
5. Bonus 2: Add a button to 3 above that allows the customer to purge their own login history.  You might want to add checkboxes to the rows of table to allow you to purge certain rows, with a Select All ability.
6. Bonus 3: Add your own cool additional feature, if you want and have the time.

As you can see by the speicifications, this test was pretty in depth. One thing I wanted to do right off the bat, is use as many of Magento 2's UiComponents as possible to save me the work of building the UX and UX functionality. I thought the Admin Grid would be perfect for listing the table of IP's, plus all the functionality for delete, pagination, list, etc would be built in! So this is what I was going to do, bring the admin grid to the frontend.

The module I built in its entirety is here: [https://github.com/djfordz/geoip](https://github.com/djfordz/geoip)

For those reading this who are not too familiar yet with development, I will start from the very beginning. What needs to be done first is breaking the overall project into smaller tasks.

These smaller tasks for me ended up being:
1.  Create Module Skeleton
2.  Create Install Schema script to create table.
3.  Create Observer to tie into customer login event.
4.  Add custom block to customer dashboard.
5.  Add new page--Controller, Block, Model
6.  Create admin grid on new page.
7.  Profit!

I actually built the module in the above order as well.

### Creating Module Skeleton

As we know in this post, [Magento 2 Module File Structure](http://magefoo.com/magento-2-module-file-structure/)

To build the skeleton module we need a few files.

Make the directory under `app/code` which will contain your module.
For me this is
`app/code/Dford/Geoip/`

Add the files:

registration.php

```
# app/code/Dford/Geoip/registration.php

<?php

use \Magento\Framework\Component\ComponentRegistrar;

ComponentRegistrar::register(ComponentRegistrar::MODULE, 'Dford_Geoip', __DIR__);
```

composer.json

```
# app/code/Dford/Geoip/composer.json

{
  "name": "dford/geoip",
  "description": "short description of module",
  "authors": [
    {
      "name": "David Ford",
      "email": "magefoo@gmail.com",
      "homepage": "http://magefoo.com"
    }
  ],
  "require": {
      "php": "~7.0.13|~7.1.0",
      "magento/module-ui": "101.*"
  },
  "type": "magento2-module",
  "version": "1.0.0",
  "license": [
    "OSL-3.0",
    "AFL-3.0"
  ],
  "autoload": {
    "files": [ "registration.php" ],
    "psr-4": {
      "Dford\\Geoip\\": ""
    }
  }
}
```

etc/module.xml

```
<?xml version="1.0"?>

<config xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="urn:magento:framework:Module/etc/module.xsd">

    <module name="Dford_Geoip" setup_version="1.0.0">
        <sequence>
            <module name="Magento_Ui"/>
        </sequence>
    </module>
</config>
```

Once we have the module skeleton structure complete. We will create the Install Schema Script to create the table we will need to store the IP, User Agent, Customer ID, and Date, this will be under a diretory called Setup.

```
# app/code/Dford/Geoip/Setup/InstallSchema.php

<?php

namespace Dford\Geoip\Setup;

use Magento\Framework\Setup\InstallSchemaInterface;
use Magento\Framework\Setup\ModuleContextInterface;
use Magento\Framework\Setup\SchemaSetupInterface;

class InstallSchema implements InstallSchemaInterface
{
    public function install(SchemaSetupInterface $setup, ModuleContextInterface $context)
    {
        $setup->startSetup();
        $table = $setup->getConnection()
                       ->newTable($setup->getTable('dford_geoip_data'))
                       ->addColumn(
                           'entity_id',
                           \Magento\Framework\DB\Ddl\Table::TYPE_INTEGER,
                           null,
                           ['identity' => true, 'unsigned' => true, 'nullable' => false, 'primary' => true],
                           'Entity ID'
                       )
                       ->addColumn(
                           'customer_id',
                           \Magento\Framework\DB\Ddl\Table::TYPE_INTEGER,
                           null,
                           ['unsigned' => true, 'nullable' => false],
                           'Customer ID'
                       )
                       ->addColumn(
                           'ip_address',
                           \Magento\Framework\DB\Ddl\Table::TYPE_TEXT,
                           255,
                           [],
                           'IP Address'
                       )
                       ->addColumn(
                           'user_agent',
                           \Magento\Framework\DB\Ddl\Table::TYPE_TEXT,
                           400,
                           [],
                           'User Agent'
                       )
                       ->addColumn(
                           'created_at',
                           \Magento\Framework\DB\Ddl\Table::TYPE_TIMESTAMP,
                           null,
                           ['nullable' => false, 'default' => \Magento\Framework\DB\Ddl\Table::TIMESTAMP_INIT],
                           'Created At'
                       )
                       ->setComment(
                           'DFord Geoip Trellis Test Table'
                        );
        $setup->getConnection()->createTable($table);
        $setup->endSetup();
    }
}
```

The `InstallSchema.php` script is pretty straight forward and self explanatory. We implement the `InstallSchemaInterface`, create the `public function install(SchemaSetupInterface $setup, ModuleContextInterface $context)` as required by the interface, we start the setup by calling `$setup->startSetup()`, we build our query with `$setup->getConnection()->newTable()->addColumn()->setComment()`methods, we actually push the create query with `$setup->getConnection()->createTable($table)` and finally call `$setup->endSetup()`;

The installSchema script is all we need to build the table we need, it is a lot simpler than Magento 1 where we have to add a bunch of setup options in config xml. Magento 2 is really nice in that the file structure is integral to the module whereas Magento 1, you have to spell out the file structure in config file.

Next, we need to tie into the customer login event. This I decided to use the Event/Observer system to do so.  If you have any experience with Magento then you know all about the Event/Observer Interface, it is actually one of the easiest and nicest things about the Magento framework.

Lets create our observer, again we can find an expample at [Creating a Magento 2 Observer](http://magefoo.com/creating-a-magento-2-observer/).

Unlike Magento 1, where you have to spell out every detail in the config xml, there really is only two files needed for the observer in Magento 2. The `events.xml` file and the Observer php file that will reside in its own `app/code/Dford/Geoip/Observer/` directory.

Let's start with the `events.xml` file. It is extremely straight forward, first you simply list the event you want to observe and then list the name of the observer which will listen to this event.

```
# app/code/Dford/Geoip/etc/events.xml

<?xml version="1.0"?>
<config xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="urn:magento:framework:Event/etc/events.xsd">
    <event name="customer_login">
        <observer name="CustomerLogin" instance="Dford\Geoip\Observer\CustomerLogin" />
    </event>
</config>
```

Next, we build our observer, the name of the file and the class name will be what we listed in the `events.xml` file.

```
# app/code/Dford/Geoip/Observer/CustomerLogin.php

<?php

namespace Dford\Geoip\Observer;

use Magento\Framework\HTTP\PhpEnvironment\RemoteAddress;
use Magento\Framework\HTTP\PhpEnvironment\Request;
use Dford\Geoip\Model\GeoipFactory;

class CustomerLogin implements \Magento\Framework\Event\ObserverInterface
{

    protected $_remoteAddress;
    protected $_request;
    protected $_geoipFactory;

    public function __construct(
        RemoteAddress $remoteAddress,
        Request $request,
        GeoipFactory $geoipFactory
    )
    {
        $this->_remoteAddress = $remoteAddress;
        $this->_request = $request;
        $this->_geoipFactory = $geoipFactory;
    }

    public function execute(\Magento\Framework\Event\Observer $observer)
    {
        $data['customer_id'] = $observer->getEvent()->getCustomer()->getId();
        $data['ip_address'] = $this->_remoteAddress->getRemoteAddress();
        $data['user_agent'] = $this->_request->getServer('HTTP_USER_AGENT');

        $geoip = $this->_geoipFactory->create();
        $geoip->setData($data);
        $geoip->save();

        return true;
    }
}
```

As we know with Magento 2 observers we implement the `ObserverInterface` and must use `execute()` method as defined in `\Magento\Framework\Event\ObserverInterface`.

We use the `__constuct()` method to build our constructor and use Dependancy Injection to retrieve the information we need to build our table.  What is the information we need? Customer ID, Remote IP address, and User Agent, that is it. Since we built the table using MySql timestamp function, the database will create the timestamp when the row is created.

The customer ID is provided by the customer_login event as we defined in events.xml, so all we have to do is tie into the event to retrieve the customer id. We do need to inject the remoteAddress and request classes as well as our own factory method to save to database.

Magento 2 gives us an easy way of retrieving the remote IP with `Magento\Framework\HTTP\PhpEnvironment\RemoteAddress` class and retrieving the User Agent with the `Magento\Framework\HTTP\PhpEnvironment\Request` class, which we both inject in the constructor.

We then build our execute method, retrieve the data we need, (in fact we could actually refactor my code into another method as technically the retrieval of each data point is separate from the factory we build to save into the database, but this is preference really, and since we aren't retrieving massive amounts of data, I just put it all in a single method.)

As you can see by the above code, we simply use the event to get the customer id with `$observer->getEvent()->getCustomer()->getId()`, get the remote address with `$this->_remoteAddress->getRemoteAddress()` and get the user agent with `$this->_request->getServer('HTTP_USER_AGENT')` both methods we injected into the constructor.

Next, we use our factory method, which will be explained next to create our database model in which we inject the data we retrieved and save to database as you can see in the above code.

`$geoip = $this->_geoipFactory->create()` method creates the virtual class we need to connect to the database and save our data.

In fact after the module is created, installed, and used, you will see in your `generated/` folder your virtual classes that are created.

One such class is the `generated/code/Dford/Geoip/Model/GeoipFactory.php` file which I have listed for you below. We will talk about creating this model next.

*REMEMBER: THIS IS A VIRTUAL CLASS! IT IS GENERATED AUTOMATICALLY BY MAGENTO 2, YOU WILL NOT CODE THIS, I HAVE LISTED ONLY AS AN EXAMPLE*

```

# REMEMBER THIS IS A VIRTUAL CLASS AND IS GENERATED AUTOMATICALLY UPON RUNTIME! YOU DO NOT CREATE THE FACTORY CLASS! I HAVE LISTED THIS ONLY AS AN EXAMPLE!

[08:07 dford@fu mage225] > cat generated/code/Dford/Geoip/Model/GeoipFactory.php 
<?php
namespace Dford\Geoip\Model;

/**
 * Factory class for @see \Dford\Geoip\Model\Geoip
 */
class GeoipFactory
{
    /**
     * Object Manager instance
     *
     * @var \Magento\Framework\ObjectManagerInterface
     */
    protected $_objectManager = null;

    /**
     * Instance name to create
     *
     * @var string
     */
    protected $_instanceName = null;

    /**
     * Factory constructor
     *
     * @param \Magento\Framework\ObjectManagerInterface $objectManager
     * @param string $instanceName
     */
    public function __construct(\Magento\Framework\ObjectManagerInterface $objectManager, $instanceName = '\\Dford\\Geoip\\Model\\Geoip')
    {
        $this->_objectManager = $objectManager;
        $this->_instanceName = $instanceName;
    }

    /**
     * Create class instance with specified parameters
     *
     * @param array $data
     * @return \Dford\Geoip\Model\Geoip
     */
    public function create(array $data = array())
    {
        return $this->_objectManager->create($this->_instanceName, $data);
    }
}
```

Ok, now that we have the first half of the module created, we will now create our Model which will have the business logic for talking to our database, which we call a factory pattern in our Observer.

Below is the code for your Model. In Magento 2, it is very easy to create our CRUD methods. Please remember that the factory we called is a virtual class and is created automatically by Magento 2, however the factory class IS based off our model below.

```
# app/code/Dford/Geoip/Model/Geoip.php

<?php

namespace Dford\Geoip\Model;

class Geoip extends \Magento\Framework\Model\AbstractModel
{
    public function __construct(
        \Magento\Framework\Model\Context $context,
        \Magento\Framework\Registry $registry,
        \Magento\Framework\Model\ResourceModel\AbstractResource $resource = null,
        \Magento\Framework\Data\Collection\AbstractDb $resourceCollection = null,
        array $data = []
    )
    {
        parent::__construct($context, $registry, $resource, $resourceCollection, $data);
    }

    public function _construct()
    {
        $this->_init('Dford\Geoip\Model\ResourceModel\Geoip');
    }

}
```

That's it! very easy.

We create the constructor which injects the necessary components to manipulate our data, and then call the init method with our resource model. We will need to create a resource model as well as a collection model as well.

```
# app/code/Dford/Geoip/Model/ResourceModel/Geoip.php

<?php

namespace Dford\Geoip\Model\ResourceModel;

class Geoip extends \Magento\Framework\Model\ResourceModel\Db\AbstractDb
{
    protected function _construct()
    {
        $this->_init('dford_geoip_data', 'entity_id');
    }
}
```

and the Collection Model

```
# app/code/Dford/Geoip/Model/ResourceModel/Geoip/Collection.php

<?php

namespace Dford\Geoip\Model\ResourceModel\Geoip;

class Collection extends \Magento\Framework\Model\ResourceModel\Db\Collection\AbstractCollection
{
    protected function _construct()
    {
        $this->_init(
            'Dford\Geoip\Model\Geoip',
            'Dford\Geoip\Model\ResourceModel\Geoip'
        );
    }
}
```

The above model and resource models allow for the factory model to automatically be built when we call it in our observer.

Next we need to add the block to the Customer Account Page to display the info we are saving.

Usually, in Magento 1, we will build a custom block for this, even in Magento 2.0 or 2.1 we would build a custom block, but in Magento 2.2 ViewModels were introduced and custom blocks went to the wayside. ViewModels are the business logic of the View, the View now being the .phtml file. Alan Storm has a good article on MVVC architecture of Magento 2 called [Introduction to Magento 2 -- No More MVC](https://alanstorm.com/magento_2_mvvm_mvc/) and Yireo has a good refresher on how
to use them here [ViewModels in Magento 2](https://www.yireo.com/blog/2017-08-12-viewmodels-in-magento-2)

To add our block, we will need three files, the ViewModel, the layout file, and the template file.

We will start with the ViewModel which will reside in the a new directory called `app/code/Dford/Geoip/ViewModel/`.

In our ViewModel we inject our geoip factory as well as httpContext to get the customer login info. I have also created two constants `all` and `last` which allows either the last login or all logins to be retrieved.

```
# app/code/Dford/Geoip/ViewModel/LoginHistory.php

<?php

namespace Dford\Geoip\ViewModel;

use Dford\Geoip\Model\GeoipFactory;
use Magento\Framework\App\Http\Context;

class LoginHistory implements \Magento\Framework\View\Element\Block\ArgumentInterface
{
    const GEOIP_ALL = 'all';
    const GEOIP_LAST = 'last';

    protected $_geoipFactory;
    protected $_httpContext;

    public function __construct(GeoipFactory $geoipFactory, Context $httpContext)
    {
        $this->_geoipFactory = $geoipFactory;
        $this->_httpContext = $httpContext;
    }

    public function getGeoData($flag = 'all')
    {

        $customerId = $this->_getCustomerId();

        if (empty($customerId)) {
            return;
        }

        $geoip = $this->_geoipFactory
                    ->create()
                    ->getCollection();

        $collection = $geoip
            ->addFieldToSelect('*')
            ->addFieldToFilter('customer_id', ['eq' => $customerId])
            ->setOrder('created_at')
            ->setPageSize(25)
            ->setCurPage(1);


        if ($collection->getSize() > 1) {
            $resultArray = $collection->toArray();
            if ($flag == self::GEOIP_LAST ) {
                $result =  $resultArray['items'][1];
            } else if ($flag == self::GEOIP_ALL) {
                $result = $resultArray['items']; 
            }
        } else {
            $result = 'This is your first login.';
        }
        
        return $result;
    }

    protected function _isLoggedIn()
    {
        return $this->_customerSession->isLoggedIn();
    }

    protected function _getCustomerId()
    {
        return $this->_httpContext->getValue(\Dford\Geoip\Model\Customer\Context::CONTEXT_CUSTOMER_ID);
    }
}

```

A method called `getGeoipData($flag = 'all')` is created which we will call in our template. The logic to retrieve the data from the database is in this method. We also create two other methods pertaining to customer login.

We are using httpContext for customer login instead of Session to ensure we get the correct customer ID when full page cache is enabled. Speaking of this, our Plugin we created comes from this blog post and we use it in our module. [How to get customer ID when full page cache is enabled in Magento 2](https://ranasohel.me/2017/05/05/how-to-get-customer-id-from-block-when-full-page-cache-enable-in-magento-2/).  We will discuss more about this later.

Next we create the layout file and the template file as below.

We create a file named `app/code/Dford/Geoip/view/frontend/layout/customer_account.xml` (The same name of the file we are injecting our block into)

```
# app/code/Dford/Geoip/view/frontend/layout/customer_account.xml

<?xml version="1.0"?>
<page xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="urn:magento:framework:View/Layout/etc/page_configuration.xsd">
    <body>
        <referenceContainer name="content">
            <block class="Magento\Framework\View\Element\Template" name="dashboard.geoipLogin" before="-" template="Dford_Geoip::geoip_login.phtml" as="geoipLogin">
                <arguments>
                    <argument name="view_model" xsi:type="object">Dford\Geoip\ViewModel\LoginHistory</argument>
                </arguments>
            </block>
        </referenceContainer>
    </body>
</page>
```

Not much to point out in this file, it is all pretty self-explanatory. However, this is where you will declare your ViewModel in arguments tag. the block type is `Magento\Framework\View\Element\Template` the template name is `Dford_Geoip::geoip_login.phtml` and we add arguments `name=view_model` and the class of the View Model `Dford\Geoip\ViewModel\LoginHistory`

Next comes the template file which will be placed in `app/code/Dford/Geoip/view/frontend/templates/geoip_login.phtml`

```
# app/code/Dford/Geoip/view/frontend/templates/geoip_login.phtml

<?php
$viewModel = $block->getViewModel();

// params for getGeoData last|all;
$geoDataLast = $viewModel->getGeoData(\Dford\Geoip\ViewModel\LoginHistory::GEOIP_LAST);
?>
<div class="block block-dashboard-info">
    <div class="block-title"><strong><?= __('Login History') ?></strong></div>
    <div class="block-content">
        <div class="box box-information">
            <?php if (is_array($geoDataLast)): ?>
                <strong class="box-title">
                    <span><?= __('Last Login Info') ?></span>
                </strong>
                <div class="box-content">
                    <p>IP Address: <?= __($geoDataLast['ip_address']) ?></p>
                    <p>Date: <?= __($geoDataLast['created_at']) ?></p>
                    <p>User Agent: <br/><?= __($geoDataLast['user_agent']) ?></p>
                </div>
            <?php else: ?>
                <p class="box-title"><?= __($geoDataLast) ?></p>
            <?php endif; ?>
            <div class="box-actions">
                <a href="<?php echo $block->getUrl('geoip') ?>"><?= __('Login History') ?></a>
            </div>
        </div>
    </div>
</div>
```

The only thing of note in the template file is we are calling our ViewModel we use as an argument in the layout file with `$block->getViewModel()`, which allows us access to the `getGeoipData($flag)` method, We of course use our constant in the ViewModel to call the `last` argument, then display the information in the same layout and css classes that are common to the rest of the blocks on the Customer Account Page.

### Now for the fun part. We need to create our own page to display the login history of the customer, this will use the Admin Grid UiComponents.

We first create our Controller and an Abstract Controller in `app/code/Dford/Geoip/Controller/`

We create an Abstract Controller to extend from the base abstract controller because the way controllers are independent entities, it is better to extend from your own abstract class.

```
# app/code/Dford/Geoip/Controller/LoginHistoryAbstract.php
<?php

namespace Dford\Geoip\Controller;

abstract class LoginHistoryAbstract extends \Magento\Framework\App\Action\Action
{

}
```

Next, we create our controller which will actually extend from our abstract class

```
<?php

namespace Dford\Geoip\Controller\Index;

use Magento\Framework\View\Element\UiComponentInterface;
use Magento\Framework\Controller\ResultFactory;

class Index extends \Dford\Geoip\Controller\LoginHistoryAbstract
{
    protected $customerSession;
    protected $pageFactory;
    protected $factory;

    public function __construct(
        \Magento\Framework\App\Action\Context $context,
        \Magento\Framework\View\Result\PageFactory $pageFactory,
        \Magento\Framework\View\Element\UiComponentFactory $factory,
        \Magento\Customer\Model\Session $customerSession
    )
    {
        $this->pageFactory = $pageFactory;
        $this->factory = $factory;
        $this->customerSession = $customerSession;
        return parent::__construct($context);
    }

    public function execute()
    {
        if ($this->customerSession->isLoggedIn()) {
            $isAjax = $this->getRequest()->isAjax();
            if ($isAjax) {
                $component = $this->factory->create($this->_request->getParam('namespace'));
                $this->prepareComponent($component);
                $this->_response->appendBody((string) $component->render());
            } else {
                $resultPage = $this->pageFactory->create();
                return $resultPage;
            }
        } else {
            $resultRedirect = $this->resultFactory->create(ResultFactory::TYPE_REDIRECT);
            $resultRedirect->setPath('customer/account/login');
            return $resultRedirect;
        }
    }

    protected function prepareComponent(UiComponentInterface $component)
    {
        foreach ($component->getChildComponents() as $child) {
            $this->prepareComponent($child);
        }
        $component->prepare();
    }
}
```

In the controller, we will check if the customer is logged in, if they are, then we continue to the page, if not, then we redirect to login page.

Since we are using a UiComponent instead of our own template file, we will use the `prepareComponent(UiComponentInterface $compoent)` to build our UiComponent and build it using dependancy injection in the `di.xml` file.

So, before we dig into the Admin Grid and the `di.xml` file, we will create the Layout file, `app/code/Dford/Geoip/view/frontend/layout/geoip_index_index.xml`

```
# app/code/Dford/Geoip/view/frontend/layout/geoip_index_index.xml

<?xml version="1.0"?>
<page xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" layout="1column" xsi:noNamespaceSchemaLocation="urn:magento:framework:View/Layout/etc/page_configuration.xsd">
    <head>
        <title>Customer Login History</title>
        <css src="Dford_Geoip::css/backend.css" />
    </head>
    <body>
        <referenceContainer name="content">
            <uiComponent name="geoip_grid" />
        </referenceContainer>
    </body>
</page>
```

In the layou file, we will instantiate the css in the head and add a title to the page. The css will be copied directly from the adminhtml of the theme.

The important part of the layout file is calling the uiComponent and giving it a name under the content referenceContainer.

The uiComponent will be a new xml file under the `app/code/Dford/Geoip/view/frontend/ui_component/` directory called `geoip_grid.xml`

This is the file that creates our UiComponent and is listed below, this is the exact same way you would build and Admin Grid in the backend.

```
<?xml version="1.0" encoding="UTF-8"?>
<listing xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="urn:magento:module:Magento_Ui:etc/ui_configuration.xsd">
    <argument name="context" xsi:type="configurableObject">
        <argument name="class" xsi:type="string">Magento\Framework\View\Element\UiComponent\Context</argument>
        <argument name="namespace" xsi:type="string">geoip_grid</argument>
    </argument>
    <argument name="data" xsi:type="array">
        <item name="js_config" xsi:type="array">
            <item name="provider" xsi:type="string">geoip_grid.dford_geoip_grid_data_source</item>
            <item name="deps" xsi:type="string">geoip_grid.dford_geoip_grid_data_source</item>
        </item>
        <item name="spinner" xsi:type="string">geoip_columns</item>
    </argument>
    <dataSource name="dford_geoip_grid_data_source">
        <argument name="dataProvider" xsi:type="configurableObject">
            <argument name="class" xsi:type="string">Dford\Geoip\Ui\DataProvider\DataProvider</argument>
            <argument name="name" xsi:type="string">dford_geoip_grid_data_source</argument>
            <argument name="primaryFieldName" xsi:type="string">entity_id</argument>
            <argument name="requestFieldName" xsi:type="string">customer_id</argument>
            <argument name="data" xsi:type="array">
                <item name="config" xsi:type="array">
                    <item name="component" xsi:type="string">Magento_Ui/js/grid/provider</item>
                    <item name="update_url" xsi:type="url" path="geoip/index/index"/>
                    <item name="storageConfig" xsi:type="array">
                        <item name="indexField" xsi:type="string">customer_id</item>
                    </item>
                </item>
            </argument>
        </argument>
    </dataSource>
    <filters name="listing_filters">
        <argument name="data" xsi:type="array">
            <item name="config" xsi:type="array">
                <item name="columnsProvider" xsi:type="string">geoip_grid.geoip_grid.geoip_columns</item>
                <item name="storageConfig" xsi:type="array">
                    <item name="provider" xsi:type="string">geoip_grid.geoip_grid.listing_top.bookmarks</item>
                    <item name="namespace" xsi:type="string">current.filters</item>
                </item>
                <item name="childDefaults" xsi:type="array">
                    <item name="provider" xsi:type="string">geoip_grid.geoip_grid.listing_top.listing_filters</item>
                    <item name="imports" xsi:type="array">
                        <item name="visible" xsi:type="string">geoip_grid.geoip_grid.geoip_columns.${ $.index }:visible</item>
                    </item>
                </item>
            </item>
        </argument>
    </filters>
    <columns name="geoip_columns">
        <selectionsColumn name="customer_id">
            <argument name="data" xsi:type="array">
                <item name="config" xsi:type="array">
                    <item name="indexField" xsi:type="string">customer_id</item>
                </item>
            </argument>
        </selectionsColumn>
        <column name="ip_address">
            <argument name="data" xsi:type="array">
                <item name="config" xsi:type="array">
                    <item name="filter" xsi:type="string">text</item>
                    <item name="sorting" xsi:type="string">desc</item>
                    <item name="label" xsi:type="string" translate="true">IP Address</item>
                </item>
            </argument>
        </column>
        <column name="user_agent">
            <argument name="data" xsi:type="array">
                <item name="config" xsi:type="array">
                    <item name="filter" xsi:type="string">text</item>
                    <item name="sorting" xsi:type="string">desc</item>
                    <item name="label" xsi:type="string" translate="true">User Agent</item>
                </item>
            </argument>
        </column>
        <column name="created_at" class="Magento\Ui\Component\Listing\Columns\Date">
            <argument name="data" xsi:type="array">
                <item name="config" xsi:type="array">
                    <item name="filter" xsi:type="string">dateRange</item>
                    <item name="component" xsi:type="string">Magento_Ui/js/grid/columns/date</item>
                    <item name="dataType" xsi:type="string">date</item>
                    <item name="sorting" xsi:type="string">desc</item>
                    <item name="label" xsi:type="string" translate="true">Logged In Date</item>
                </item>
            </argument>
        </column>
    </columns>
    <paging name="listing_paging">
        <argument name="data" xsi:type="array">
            <item name="config" xsi:type="array">
                <item name="storageConfig" xsi:type="array">
                    <item name="provider" xsi:type="string">geoip_grid.geoip_grid.listing_top.bookmarks</item>
                    <item name="namespace" xsi:type="string">current.paging</item>
                </item>
                <item name="selectProvider" xsi:type="string">geoip_grid.geoip_grid.dford_geoip_columns.customer_id</item>
            </item>
        </argument>
    </paging>
</listing>
```

The important parts of this file are, since we want to filter the data after it comes from the database, since we want to only display our logged in customer data and not everything in the table, we will change the dataSource class to our own DataProvider class at `Dford\Geoip\Ui\DataProvider\DataProvider.php` and we change the data url to our index controller `geoip/index/index`.

We will create our columns, filters and paging in this file.

The next important step is defining our di.xml file in `app/code/Dford/Geoip/etc/frontend/` and defining the VirtualType we need to show the Admin Grid.

```
# app/code/Dford/Geoip/etc/frontend/di.xml

<?xml version="1.0"?>
<config xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="urn:magento:framework:ObjectManager/etc/config.xsd">
   <virtualType name="Dford\Geoip\Model\Resource\Collection" type="Dford\Geoip\Ui\DataProvider\SearchResult">
        <arguments>
            <argument name="mainTable" xsi:type="string">dford_geoip_data</argument>
            <argument name="resourceModel" xsi:type="string">Dford\Geoip\Model\ResourceModel\Geoip</argument>
        </arguments>
    </virtualType>
    <type name="Magento\Framework\View\Element\UiComponent\DataProvider\CollectionFactory">
        <arguments>
            <argument name="collections" xsi:type="array">
                <item name="dford_geoip_grid_data_source" xsi:type="string">Dford\Geoip\Model\Resource\Collection</item>
            </argument>
        </arguments>
    </type> 
    <type name="Magento\Framework\App\Action\AbstractAction">
         <plugin name="dford_geoip_app_action_dispatchController_context_plugin" type="Dford\Geoip\Plugin\App\Action\Context" sortOrder="15"/>
    </type>
</config>
```

The important parts of this file are the virtualType we are defining which used the `Dford\Geoip\Ui\DataProvider\SearchResult` type we will be creating, it is actually a direct copy of the `Magento\Framework\View\Element\UiComponent\DataProvider\SearchResult` class.

In the `DataProvider.php` file we instantiate in the ui_component xml file we only change the method `searchResultToOutput(SearchResultInterface $searchResult)` to return the results of only the logged in customer. Other than changing this method the file is an exact copy from `Magento\Ui\DataProvider\DataProvider` class.

We then add the `Document.php` file which is an exact copy of `Magento\Ui\DataProvider\Document.php` to ensure when calling our version of the DataProvider class it runs with our data.

I am sure there is a better way to filter search results from the database, but this is the only way I could find in the limited time available.

The following blog post describes how to add the Controller and css to allow the admin grid to achieve the proper functionality on the frontend [Ui Grid Component on the Front End in Magento 2](https://belvg.com/blog/ui-grid-component-on-the-front-end-in-magento-2.html).

and there you have it

<img src="http://magefoo.com/assets/img/ui_preview2.png" alt="ui preview" />

<img src="http://magefoo.com/assets/img/ui_preview1.png" alt="ui preview" />
