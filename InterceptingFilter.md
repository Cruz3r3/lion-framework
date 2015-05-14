# Introduction #

Lion defines a complete infrastructure of modules and classes to dispatch and transform client requests in terms of application components.
The request dispatching mechanism receives many different types of requests, which require varied types of processing. Some requests are simply forwarded to a concrete controller, while other requests must be modified, audited, or uncompressed before being further processed.

When a request enters on Lion, it often must pass several entrance tests prior to the main processing stage. i.e. perform the user authentication, switch the language, validate certain constraints, ...

The key to solving this problem in a flexible and unobtrusive manner is to have a simple mechanism for adding and removing processing components, in which each component completes a specific filtering action. This pattern is known as intercepting filter, which is one of the most interesting features on Lion request dispatcher system.

## Intercepting filter pattern ##

Filters intercept incoming requests and outgoing responses, allowing preprocessing and post-processing. We are able to add and remove these filters unobtrusively, without requiring changes to our existing code.

> Lion intercepting filter has been based on the J2EE intercepting filter pattern.
To learn more about this pattern, take a look at Core J2EE Patterns - Intercepting Filter
Execution life-cycle is as shown in the following sequence:

![https://lion-framework.googlecode.com/git/doc/images/Request/interceptingFilter.png](https://lion-framework.googlecode.com/git/doc/images/Request/interceptingFilter.png)

Filters are executed before giving the control to the front controller (preFilter) and after returning the response to the client (postFilter).

A filter is a class extending the Filter abstract class, which contains a template method to allow the execution of code before and after giving the control to the front controller.
A filter class has the following appearance:

```
<?php

class MyFilter extends __Filter {

    /**
     * This method is executed BEFORE giving the control to the front controller
     * 
     */
    public function preFilter(__IRequest &$request, __IResponse &$response) {
        ...
    }

    /**
     * This method is executed AFTER giving the control to the front controller
     * 
     */
    public function postFilter(__IRequest &$request, __IResponse &$response) {
        ...
    }

}
```

However, we are able to just implement one of those methods, depending on the concrete filter.
i.e., imagine a filter to intercept when the currency parameter is present within the request in order to execute specific code before giving the control to the front controller:

```
<?php

class CurrencyFilter extends __Filter {

    /**
     * Change the currency in session by a given one if the
     * currency parameter is present within the request.
     *
     */
    public function preFilter(__IRequest &$request, __IResponse &$response) {
        if($request->hasParameter('currency')) {
            $currency_iso_code = $request->getParameter('currency');
            //set the currency iso code within the CurrencyManager
            //singleton instance:
            CurrencyManager::getInstance()->
                             setCurrencyIsoCode($currency_iso_code);
        }
    }

}
```

## Intercepting filter and Routes ##
Filters are associated to routes declaratively within the configuration.
When lion resolves a route depending on the request, it recover the filter chain associated to the route and executes it.

The way to associate a filter to a route is within the configuration, inside the filters section.

i.e.

```
<?xml version = "1.0" standalone="yes"?>

<configuration>

  <filters>

    <filter name="myFilter" class="MyFilter">
      <apply-to>
        <route id="myRoute"/>
      </apply-to>
    </filter>  

  </filters>

</configuration>
```

in this example, we are associating our filter to the myRoute route, which means that lion will execute this filter just when the request belong to the myRoute route.

To learn more about routes, see the URLs and Routes section.
We can also associate a filter to all the available routes, for all of those filters which must be executed always.
i.e., imagine that the currency filter of the example above must be executed for every request:

```
<?xml version = "1.0" standalone="yes"?>

<configuration>

  <filters>

    <!-- apply currency filter to all existing routes,
         which means that it will be always executed -->
    <filter name="currency_filter" class="CurrencyFilter">
      <apply-to>
        <all-routes/>
      </apply-to>
    </filter>  

  </filters>

</configuration>
```