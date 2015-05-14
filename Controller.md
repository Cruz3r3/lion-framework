# What's an Action Controller? #

An Action Controller, or simply a Controller, is a class in charge of the execution of concrete tasks in response to certain events, typically user requests.

It's important to note the difference between the concepts action and controller:
A controller is a class in charge of the execution of one or more actions, while an action is the minimal execution unit, usually a controller's method, that perform a set of task in order to satisface an user's request.

All requests reference a pair Controller-Action to be executed, it is: An action to be executed and the controller class in charge to handle that execution.

In Lion, an action controller is a class implementing the IActionController, but usually a class subclassing the ActionController.

However, our recomendation is to just subclass the ActionController class that already implements the IActionController to define action controllers.

## Requesting actions ##

Each request in Lion references the execution of an action.
Because actions are handled by controllers, the request need to provide information regarding the controller to be used as well as the action to be executed.
In fact, every request is resolved in a pair Controller-Action, it is, which action to execute and which controller to handle the execution.

Both the controllers and the actions are identified by codes. So, each request provides enough information to resolve the controller code as well as the action code

i.e. The request http://yourdomain/foo.bar.html references the use of the controller identified as 'foo' to execute the action identified as 'bar'. Is a Lion task to resolve which class corresponds to the controller code 'foo', while is a controller task to execute the action identified as 'bar'.

When Lion resolves the controller to use, it calls to the IActionController::execute() method by passing the action code as argument.
However, by subclassing the ActionController, we just need to define methods following some naming conventions.

## Naming convention and location ##

Lion is enough flexible to allow whatever name and location for your action controllers. There are not restrictions in that sense.
However there are some naming conventions and predefined locations that make easier the creation and enablement of action controllers:

  * Use the suffix 'Controller' for all your controller classes.
**Define each controller in a single file with the same name of the controller followed by the suffix '.class.php'** Place your controllers in the /libs/controllers directory

![https://lion-framework.googlecode.com/git/doc/images/ActionController/directory_structure.png](https://lion-framework.googlecode.com/git/doc/images/ActionController/directory_structure.png)

## Controller name ##

The fact of defining a controller class is not enough to be able to execute it.
To enable a controller in Lion we need to create an entry in the app/config/controllers.xml, associating a code to each one.

```
<?xml version = "1.0" standalone="yes"?>
<configuration>

  <controller-definitions>

    <controller code="FooBar" class="FooBarController"/>

  </controller-definitions>

</configuration>
```

In this entry we are specifying that the controller code 'FooBar' correspond to the action controller BooBarController.
However, there is a special rule designed for all the controllers that haven't been declared in this file:

```
<?xml version = "1.0" standalone="yes"?>

<configuration>

  <controller-definitions>

    <controller code="*" class="*Controller"/>

  </controller-definitions>

</configuration>

```

In this entry we are specifying that a controller code corresponds to the controller with same name plus the suffix 'Controller'.
i.e. the controller code 'FooBar' corresponds to the controller FooBarController
This is the reason because we recomend to use the 'Controller' suffix in controller classes.

## Controller file and location ##

The file /app/config/includepath.xml contains the following mapping rule:

```

<?xml version = "1.0" standalone="yes"?>
<classes>

  ...

  <!-- Controllers -->
  <cluster name="Controllers" path="/libs/controllers">
    <class name="*" file="*.class.php"/>
  </cluster>

  ...

</classes>
```

This mapping rule let Lion know that any file placed in libs/controllers with the suffix '.class.php' corresponds to a class with the same name without the suffix.

So, by placing controller files in that directory and following the naming convention, it's not needed to declare explicitly the location of your controllers in the includepath.xml file.

## Defining Actions ##

By subclassing the ActionController class, it's really easy to define actions by just defining method with the following naming conventions:

The name of defaultAction() method is due to the setting value DEFAULT\_ACTION\_CODE ('default') followed by the 'Action' suffix.
Of course, it can be configured to use other prefix by changing the corresponding setting value

  * All the actions are controller's methods with the suffix 'Action' (i.e. fooAction(), barAction(), ...)
  * A request for an action will be handled by the controller by executing a method with same name as the action followed by the suffix 'Action'. i.e. to execute the action 'fooBar', an ActionController will try to execute the 'fooBarAction()' method.
  * If a method for an action is not found, an error will be trigged! (sorry, but it's similar to a 404)
  * However, it's possible to define a default action (an action that will be executed when no actions have been specified). It will correspond to one of the following methods:
  * A method with the same name as the controller code plus the suffix 'Action' (i.e., the method that corresponds to the default action for a controller with code 'foo' will be the 'fooAction()')
  * The defaultAction() method if the previous one does not exist.

And again: those naming conventions belong to the ActionController. If you implement the IActionController then you should develop your own ways to execute actions.

i.e. A Controller capable to handle multiple actions:

```

<?php

/**
 * This controller support the actions 'foo' and 'bar'.
 * It also implements a default action to be executed in
 * case no actions are specified.
 *
 */
class FooBarController extends __ActionController {

    /**
     * This method will be executed just if the controller
     * has been executed without specifying any action:
     *
     */
    public function defaultAction() {
        //the code of default action here
    }

    /**
     * This method will be executed if the specified action
     * is 'foo'
     *
     */
    public function fooAction() {
        //the code of 'foo' action here
    }

    /**
     * This method will be executed if the specified action
     * is 'bar'
     *
     */
    public function barAction() {
        //the code of 'bar' action here
    }

}

```

## Defining common tasks: preExecute and postExecute methods ##

There are 2 special methods in a class implementing the IActionController: IActionController::preExecute() and IActionController::postExecute(). In ActionController those methods have been already defined empty in order to allow the subclassing just when needed.

Those methods are usefull to define common tasks that should be executed before and/or after any action in a controller.
So, the preExecute() method will be executed previous to the action execution while the postExecute() method will be executed after the action execution (also after the view execution if the action returned a ModelAndView instance).

```

<?php

/**
 * This class has 2 special methods: preExecute and
 * postExecute
 *
 */
class FooBarController extends __ActionController {

    /**
     * This method will be executed BEFORE any action on
     * this controller.
     *
     */
    public function preExecute() {
        //common code here
    }

    /**
     * This method will be executed AFTER any action on this
     * controller (even after the view execution if case
     * action returns a __ModelAndView instance).
     *
     */
    public function postExecute() {
        //common code here
    }

}

```

## Validating Action executions ##

The file app/config/controllers.xml has not been designed just to define codes for controllers, but to define policies to grant or deny the execution of action within a controller.

In fact, there are some controller's properties that can be defined declaratively in app/config/controllers.xml that Lion will check previous to executing any action handled by the controller:

  * validRequestMethod: The request method allowed to execute actions on a controller. i.e. we can declare a controller to be executed just in case the request method is POST. By default, a controller has not request method restrictions, it is, it can be executed by ussing whatever request method.
Allowed values for this property are:
REQMETHOD\_NONE: There is not any valid request method for current controller. It's similar to disable the controller
REQMETHOD\_GET: The controller can be executed just in case the request method is GET
REQMETHOD\_POST: The controller can be executed just in case the request method is POST
REQMETHOD\_GET\_POST: The controller can be executed in both GET and POST request methods
REQMETHOD\_COMMAND\_LINE: The controller can be executed just from the command line (by ussing PHP CLI)
REQMETHOD\_ALL (default value): The controller can be executed in any case
  * **requestable**: If a controller can be 'requested' directly by the user or not. A controller not requestable means that it's not allowed to specify the controller code within the request. By default, a controller can be requested directly.
  * **requireSsl**: If a controller can be executed in a non-secure protocol or not. i.e. some controllers are able to be executed just in case the http protocol is secured. By default, a controller can be executed without any protocol restriction, it is, in both the secured and non-secured protocol.

i.e.:

```

<?xml version = "1.0" standalone="yes"?>
<configuration>

  <controller-definitions>

    <controller code = "nonRequestableActions" 
               class = "NonRequestableActionsController">
      <property name = "requestable" 
               value = "false"/>
    </controller>

    <controller code = "sslActions" 
               class = "SslActionsController">
      <property name = "requireSsl" 
               value = "true"/>
    </controller>

    <controller code = "commandLineActions" 
               class = "CommandLineActionsController">
      <property name = "validRequestMethod" 
               value = "REQMETHOD_COMMAND_LINE"/>
    </controller>

  </controller-definitions>

</configuration>
```

And yes: Lion does not support those validations at action level, just at controller level. This is not a Lion limitation, it has been designed as it due to the most common use case: assign same validation rules to all the actions within the same controller.

You can also play with the '**' symbol in your controller definitions. See the following example:**

```

<?xml version = "1.0" standalone="yes"?>
<configuration>

  <controller-definitions>

    <controller code = "ssl_*" 
               class = "Ssl*Controller">
      <property name = "requireSsl" 
               value = "true"/>
    </controller>

  </controller-definitions>

</configuration>
```

In this example, we are restricting to SSL protocol all the controllers with the prefix 'ssl_' in the code. i.e. the controller code ssl\_login, according to this mapping rule, corresponds to the controller SslLoginController, and the execution of any action within this controller is allowed just over SSL protocol._

Last but not the least, Controller authorization aspects can be also declared in the app/config/controllers.xml file. Yeah, the app/config/controlelrs.xml becomes an interesting file.
Because controllers are classes implementing the ISystemResource, we can assign permissions to them

```
<?xml version = "1.0" standalone="yes"?>
<configuration>

  <controller-definitions>

    <controller code="admin_*" class="*AdminController">
      <permission id="ADMIN"/>  
    </controller>

  </controller-definitions>

</configuration>
```

In this example, we have protected all the controllers that correspond to a code with the prefix 'admin