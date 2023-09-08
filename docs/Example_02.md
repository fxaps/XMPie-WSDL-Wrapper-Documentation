# Configuring The `ServiceMaker` Instance

The `$ServiceMaker` Object helps you retrieve an instance of SoapClient specific to an XMPie Web Service.
As such you can construct ServiceMaker() in a *similar* manner to SoapClient.

**SoapClient construction** `SoapClient::__construct ( mixed $wsdl [, array $options ] )`.  
**ServiceMaker construction** `ServiceMaker::__construct ( array $options, string $wsdl = 'http://localhost' )`.

Notice how the parameters are reversed, this is due to keeping consistency with
[wsdl2phpgenerator](https://github.com/wsdl2phpgenerator/wsdl2phpgenerator) off which this project is based.

`$options` defaults to `[]` - refer to [SoapClient](http://php.net/manual/en/soapclient.soapclient.php) for options.  
`$wsdl` defaults to `http://localhost` - change to a remote URL as required.

```php
<?php
use XMPieWsdlClient\XMPie\uProduce\v_9_0_2\_Loader\ServiceMaker;

//The properties
$options = [];
$wsdl = 'https://www.myserver.com';

//Options 1 = set properties via constructor
$ServiceMaker = new ServiceMaker($options, $wsdl);

//Option 2 - set properties via setter methods
$ServiceMaker = new ServiceMaker();
$ServiceMaker->setUProduceUrl($wsdl);
$ServiceMaker->setOptions($options);

//Create 3 new Services, each Service using the same properties defined above
$ServiceUser = $ServiceMaker->User_SSP();
$ServiceJob = $ServiceMaker->Job_SSP();
$ServiceCampaign = $ServiceMaker->Campaign_SSP();
```
