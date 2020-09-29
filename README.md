# XMPie-WSDL-Wrapper-Documentation
**Documentation for the XMPie WSDL Wrapper Projects**
This is the online documentation for the following 2 projects
- XMPie-WSDL-Wrapper-uProduce [Located Here](https://github.com/fxaps/XMPie-WSDL-Wrapper-uProduce).
- XMPie-WSDL-Wrapper-uStore [Located Here](https://github.com/fxaps/XMPie-WSDL-Wrapper-uStore).  

I have had to split the project into smaller parts as with each upgrade of uProduce and uStore,
the code base became more and more unwieldy.

## Table of Contents
- [Purpose](#purpose)
- [Supported XMPie Applications and Versions](#supported-xmpie-applications-and-versions)
- [Installation](#installation)
- [Basic Usage](#basic-usage)
- [Plain PHP SOAP vs XMPie-WSDL-Wrapper SOAP](#plain-php-soap-vs-xmpie-wsdl-wrapper-soap)
- [Concepts](#concepts)
- [Full Simple Examples](#full-simple-examples)
- [Full Advanced Examples](#full-advanced-examples)
- [Licensing, Support and Contributing](#licensing-support-and-contributing)



## Purpose
XMPie provides an Application Programming Interface (API) as a way to interact with 
the uStore and uProduce Applications. The API is available in the form of Microsoft Web Services.
Microsoft Web Services are based on a core set of standards, describing the syntax and semantics of software communication:

- Extensible Markup Language (XML) - provides the common syntax for representing data 
- Simple Object Access Protocol (SOAP) - provides the semantics for data exchange  
- Web Services Description Language (WSDL) - provides a mechanism for describing the capabilities of a Web service 

The project provides a PHP Wrapper to the XMPie Web Services, thus providing an Object Oriented Programming (OOP)
methodology to interact with the XMPie Web Services.


## Supported XMPie Applications and Versions
This project aims to support the latest versions of uStore and uProduce.

| Application   | Versions                                                    | 
| ------------- |-------------------------------------------------------------|
| uStore      | 12.1|
| uProduce        | 8.2.3, 9.8.2|

Even if your specific version of uProduce or uStore is not in the supported list,
pick the closet version as per the installation instructions below.

## Installation
The recommended method of installation is via Composer, to use the latest version:

```json
{
"require": {
    "php": ">=7.0.0",
    "fxaps/xmpie-wsdl-wrapper-uproduce": "*",
    "fxaps/xmpie-wsdl-wrapper-ustore": "*"
  }
}
```

To install a specific version or a version closest to your version of uProduce/uStore 
use the following examples:
```code
//uProduce
composer require fxaps/xmpie-wsdl-wrapper-uproduce:8.2.3.*
composer require fxaps/xmpie-wsdl-wrapper-uproduce:9.8.2.*

//uStore
composer require fxaps/xmpie-wsdl-wrapper-ustore:12.1.*
composer require fxaps/xmpie-wsdl-wrapper-ustore:13.0.*
```
You will notice that the version syntax is similar to that of uProduce/uStrore with an additional release number
uProduce -  Major.Minor.Patch.Release
uStore -  Major.Minor.Release
The release number signifies a release of this package, i.e. if I need to update the generated code due to an issue.
Leaving a star (*) in the composer require statement ensures you get the latest release of the package for your version
of uProduce and uStore. 


## Plain PHP SOAP vs XMPie-WSDL-Wrapper SOAP

### Plain PHP SOAP
To interact with the XMPie Web Service (or any SOAP Service) you could use the
built in PHP SoapClient class, similar to the following:

```php
<?php
$p = new stdClass();
$p->inUsername = 'amelia';
$p->inPassword = 'my$3cret';

$licensingService = new SoapClient("http://..../Licensing_SSP.asmx?wsdl");
$result = $licensingService->GetAvailableClicks($p);
print_r($result);
```

There biggest drawbacks of the above are:

1. **Missing Methods.** Methods are marked as missing in IDE's such a PhpStorm or WebStorm. See `GetAvailableClicks` in the image below.
   Missing methods are due to the fact that SoapClient uses a lot of magic methods - methods which are hard to debug.  
   ![Missing Method](https://github.com/fxaps/XMPie-WSDL-Wrapper/blob/master/docs/images/MissingMethod.png?raw=true)
   
2. **No type hinting.** You can probably guess that `inUsername` should be a string
   but what about more obscure parameters such as `inProps` or `inMacTypeHex`?
   Is that a String, Array or Int?

3. **No code completion.** Constantly referring to the API documentation to get the exact spelling and case can be
   a tedious process. Was that `CreateNewFromCPKG` or `CreateNewFromCpkg`?

### XMPie-WSDL-Wrapper SOAP
1. **No Missing Methods.** XMPie-WSDL-Wrapper replaces SoapClient magic methods
   with concrete Classe, Methods and Properties.

2. **Type hinting.** All class methods and properties are properly DocBlocked (e.g. `@var ArrayOfProperty $inProps`) so
   you can be sure of passing the expected variable type.

3. **Code completion.** Need we say more?

4. **Method chaining.** Familiarise yourself with method chaining to make your code more readable and efficient.


## Concepts
XMPie-WSDL-Wrapper is based on the work of [wsdl2phpgenerator](https://github.com/wsdl2phpgenerator/wsdl2phpgenerator).
The wsdl2phpgenerator utility is used to generate PHP Classes, Methods and Properties from the XMPie Web Services.
Those resulting PHP Classes are wrapped by XMPie-WSDL-Wrapper to provide an easy and simple way to
access **any** of the XMPie Web Services through a common interface.

Through XMPie-WSDL-Wrapper, you make use of **Request** and **Response** cycles via a **Service**.

- **Request:** The parameters of the data you are requesting. 
- **Service:** This API that will serve you the data.
- **Response:** The data you asked for in your Request.

In other words, you create a **Request** which you send to a **Service** to get back a **Response**.

## Starting with the Factory
The easiest way to get and create **Requests** and **Services** is via the Factory.
This also has the added benefit of passing in some setup parameters making your code reusable.

**uProduce Factory**
```php
<?php
require __DIR__ . '/vendor/autoload.php';

use XMPieWsdlClient\uProduceFactory;

$xmpOptions =
    [
        'url' => '127.0.0.1',
        'username' => 'admin',
        'password' => 'admin',
    ];

$Factory = new uProduceFactory($xmpOptions);
$RequestFabricator = $Factory->getUProduceRequestFabricator();
$ServiceFabricator = $Factory->getUProduceServiceFabricator();
```


**uStore Factory**
```php
<?php
require __DIR__ . '/vendor/autoload.php';

use XMPieWsdlClient\uStoreFactory;

$xmpOptions =
    [
        'url' => 'https://127.0.0.1/',
        'username' => 'some.admin.user@example.com',
        'password' => '$secr3tP@ss',
    ];

$Factory = new uStoreFactory($xmpOptions);
$RequestFabricator = $Factory->getUStoreRequestFabricator();
$ServiceFabricator = $Factory->getUStoreServiceFabricator();
```

Via the created ```$RequestFabricator``` and ```$ServiceFabricator``` you can now create the
required *Request* and *Response* Objects.

### The *Request* Object
Before you can get data, you need to format your request into a structure the Web Service understands. 
Formatting the parameters - such as username, password and JobId - is the job of the Request Object Class.

Through the instance `$RequestFabricator`, we can create a structured `$Request` Object for *any* XMPie Web Service.

```php
<?php
//I need some info about UserID 1...
$Request = $RequestFabricator->User_SSP()->GetAllProperties()->setInUserID(1);

//I need all the messages from JobID 14809...
$Request = $RequestFabricator->Job_SSP()->GetMessages()->setInJobID(14809);

//I need some information about CampaignID 457...
$Request = $RequestFabricator->Campaign_SSP()->GetAllProperties()->setInCampaignID(457);
```

It is important to note that the `$Request` Object above *does not* hold the data we are requesting from the API,
it merely holds the request parameters in a format that is ready to send to a *Service*.

### The *Service* Object
To get the data, you need to employ the Service Object Class to process the `$Request` Object.

Through the instance `$ServiceFabricator`, we can create a SoapClient `$Service` Object for *any* XMPie Web Service.

```php
<?php
//I need a SoapClient for the User Service...
$Service = $ServiceFabricator->User_SSP();

//I need a SoapClient for the Job Service...
$Service = $ServiceFabricator->Job_SSP();

//I need a SoapClient for the Campaign Service...
$Service = $ServiceFabricator->Campaign_SSP();
```

Now that we have `$Request` and `$Service` instances, we can use then to get the XMPie Web Service.

### The *Response* Object
There is no need to explicitly make a `$Response` instance - it's a product of calling a *Service*.

```php
<?php
$Response = $Service->GetAllProperties($Request);
```

One thing to note about the`$Response` object, it contains the *entire* response (i.e all the 'packaging')
from the API. Most of the time you only need the *result* (i.e. the 'goods') from inside the `$Response`.
Each `$Response` contains a handy `get<method_name>Result()` method to extract the result/s from $Response.

```php
<?php
$Response = $Service->GetAllProperties($Request);
$result = $Response->getGetAllPropertiesResult();
print_r($result);
```
The variable `$result` contains the all the properties of the requested user and
you can now work with `$result` in your PHP code.

> *TIP* Consult the `docs` folder for advanced examples on how to work with the
data-types that are returned from XMPie Web Services.


## Full Simple Example for uProduce
Here is an overly verbose example:
```php
<?php
require __DIR__ . '/vendor/autoload.php';

use XMPieWsdlClient\uProduceFactory;

$xmpOptions =
    [
        'url' => '127.0.0.1',
        'username' => 'admin',
        'password' => 'admin',
    ];

$Factory = new uProduceFactory($xmpOptions);
$RequestFabricator = $Factory->getUProduceRequestFabricator();
$ServiceFabricator = $Factory->getUProduceServiceFabricator();


//Call 1 - I need to know how many clicks I have left
$Request = $RequestFabricator->Licensing_SSP()->GetAvailableClicks();
$Service = $ServiceFabricator->Licensing_SSP();
$Response = $Service->GetAvailableClicks($Request);
$result = $Response->getGetAvailableClicksResult();
print_r($result);


//Call 2 - I need information about UserID 1
$Request = $RequestFabricator->User_SSP()->GetAllProperties()->setInUserID(1);
$Service = $ServiceFabricator->User_SSP();
$Response = $Service->GetAllProperties($Request);
$result = $Response->getGetAllPropertiesResult();
print_r($result);


//Call 3 - I need all the messages from JobID 14809...
$Request = $RequestFabricator->Job_SSP()->GetMessages()->setInJobID(14809);
$Service = $ServiceFabricator->Job_SSP();
$Response = $Service->GetMessages($Request);
$result = $Response->getGetMessagesResult();
print_r($result);


//Call 4 - I need some information about CampaignID 457...
$Request = $RequestFabricator->Campaign_SSP()->GetAllProperties()->setInCampaignID(457);
$Service = $ServiceFabricator->Campaign_SSP();
$Response = $Service->GetAllProperties($Request);
$result = $Response->getGetAllPropertiesResult();
print_r($result);
```

In each of the calls above, you see the same 3 steps:
1. Create a **Request** 
2. Send the **Request** to a **Service** 
3. Get back a **Response** (from which we extract the result and use in our PHP code).

Here is the same as above example, but using method chaining to save on a few lines:
```php
<?php
require __DIR__ . '/vendor/autoload.php';

use XMPieWsdlClient\uProduceFactory;

$xmpOptions =
    [
        'url' => '127.0.0.1',
        'username' => 'admin',
        'password' => 'admin',
    ];

$Factory = new uProduceFactory($xmpOptions);
$RequestFabricator = $Factory->getUProduceRequestFabricator();
$ServiceFabricator = $Factory->getUProduceServiceFabricator();


//Call 1 - I need to know how many clicks I have left
$Request = $RequestFabricator->Licensing_SSP()->GetAvailableClicks();
$result = $ServiceFabricator->Licensing_SSP()->GetAvailableClicks($Request)->getGetAvailableClicksResult();
print_r($result);


//Call 2 - I need information about UserID 1
$Request = $RequestFabricator->User_SSP()->GetAllProperties()->setInUserID(1);
$result = $ServiceFabricator->User_SSP()->GetAllProperties($Request)->getGetAllPropertiesResult();
print_r($result);


//Call 3 - I need all the messages from JobID 14809...
$Request = $RequestFabricator->Job_SSP()->GetMessages()->setInJobID(14809);
$result = $ServiceFabricator->Job_SSP()->GetMessages($Request)->getGetMessagesResult();
print_r($result);


//Call 4 - I need some information about CampaignID 457...
$Request = $RequestFabricator->Campaign_SSP()->GetAllProperties()->setInCampaignID(457);
$result = $ServiceFabricator->Campaign_SSP()->GetAllProperties($Request)->getGetAllPropertiesResult();
print_r($result);
```

## Full Simple Example for uStore
Skipping the overly verbose example and going straight to the streamlined method chaining example:
```php
<?php
require __DIR__ . '/vendor/autoload.php';

use XMPieWsdlClient\uStoreFactory;

$xmpOptions =
    [
        'url' => 'https://127.0.0.1/',
        'username' => 'some.admin.user@example.com',
        'password' => '$secr3tP@ss',
    ];

$Factory = new uStoreFactory($xmpOptions);
$RequestFabricator = $Factory->getUStoreRequestFabricator();
$ServiceFabricator = $Factory->getUStoreServiceFabricator();

$Request = $RequestFabricator->UserWS()->GetUserByEmail()->setEmail('Joe.Citizen@example.com');
$result = $ServiceFabricator->UserWS()->GetUserByEmail($Request)->getGetUserByEmailResult();

//If we print_r($result) we would get something like this...

/*
    XMPieWsdlClient\XMPie\uStore\v_12_1\WSAPI\UserWS\User Object
        (
            [UserID:protected] => 1005
            [Login:protected] => Joe.Citizen@example.com
            [FirstName:protected] => Joe
            [LastName:protected] => Citizen
            [Email:protected] => Joe.Citizen@example.com
            [CreatedLoginSessionID:protected] => 1
            [CreatedDate:protected] => 2014-04-29T11:25:52.84
            [ModifiedLoginSessionID:protected] => 90503
            [ModifiedDate:protected] => 2020-07-31T13:57:51.52
            [StatusID:protected] => 1
            [ExternalID:protected] =>
            [PhoneNumber:protected] => 
            [MobileNumber:protected] => 0400 000 000
            [FaxNumber:protected] => 02 1234 5678
            [CompanyName:protected] => FX
            [Department:protected] => Sample
            [JobTitle:protected] =>
            [IsActivated:protected] => 1
            [IsLocked:protected] =>
            [AssignedToStoreID:protected] => 0
            [GenderID:protected] => -1
            [BirthDay:protected] => 0001-01-01T00:00:00
            [Custom1:protected] =>
            [Custom2:protected] =>
            [Custom3:protected] =>
            [Custom4:protected] =>
            [Custom5:protected] =>
        )
*/

//Because there are concrete classes that understand the response, we have access to methods such as the following... 
print_r($result->getEmail());           //Joe.Citizen@example.com
print_r($result->getFirstName());       //Joe
print_r($result->getLastName());        //Citzen
print_r($result->getMobileNumber());    //0400 000 000

//If you had used plain old SOAP, you would be using DOMDocument to extract the information!!
```


## Licensing, Support and Contributing
**Licensing**  
This project is licensed under the MIT License - you are free to use the code in this project
under the terms of this [License](https://github.com/fxaps/XMPie-WSDL-Wrapper/blob/master/LICENSE).

Please note however, the XMPie SDK (and associated code samples) are licensed products from XMPie.
Consult your local [XMPie Dealer](www.xmpie.com) to make a purchase.

**Support**  
Because the XMPie SDK is a licensed product, any requests to 'help me do something' will be ignored. 
An example of what will be ignored:
- Can you please show me how to execute an InDesign Document?
- How do I get the download path of a Job?
- How do I upload a Campaign into the Dashboard?

**Contributing**  
If there is a bug in the code, please raise an [issue](https://github.com/fxaps/XMPie-WSDL-Wrapper/issues).
Because this project uses a code generation utility, I cannot accept pull requests as the
next round of code generation would overwrite the pull request.
Please raise an [issue](https://github.com/fxaps/XMPie-WSDL-Wrapper/issues) so that I can fix the code generation utility.
If you would like to contribute to the code generation utility, I can add you to the private project.

