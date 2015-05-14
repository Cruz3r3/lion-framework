# The Uri class #

By delegating the URL rewrite to Lion, the URL format depends on the route definitions. Because URL formats are sensible to be changed, is a bad practice to hardcode URLs within the code/templates since they could stop working after changing the URL format within the route specification.

The Uri class has been designed to cover this aspect. It provides methods to discompound an URL into components as well as to do the inverse task, to compose an URL as from the components.

i.e. Discompound an URL into components:

```
<?php

//Discompound an url into single elements:
$uri = __UriFactory::getInstance()->
                     createUri('http://yourdomain.com/invoices/10948.html');

$parameters = $uri->getParameters(); 
//--> [invoice_id => 10948]

$controller_code = $uri->getControllerCode(); 
//--> invoices

$action_code = $uri->getActionCode(); 
//--> default

$route_id = $uri->getRouteId(); 
//--> invoice_search

//we can also know which front controller attends the request:
$front_controller_class = $uri->getFrontControllerClass();
//--> __HttpFrontController
```

i.e. Compound an URL as from components:

```
<?php

//Compound an url from single elements:
$uri = new __Uri();
$uri->setControllerCode('invoices');
$uri->setParameters(array('invoice_id' => 10948));
$uri->setRouteId('invoice_search');

$url = $uri->getUrl(); 
//--> /invoices/10948.html

$url = $uri->getAbsoluteUrl();
//--> http://yourdomain.com/invoices/10948.html
```

See the Uri definition for more information.