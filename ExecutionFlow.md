## Introduction ##

A Lion application needs to include an special class to launch the rest of the framework in each request: **The Lion Engine**.
To include the Lion Engine, we just need to include the **lion.php** file located in the root directory within the lion distribution.

At the same time, there are 3 main parts of the framework architecture that are involved in every request: The **Context Container**, the **Request Dispatcher** and the **Dual-MVC**.

The Context Container represents the application itself as an space where all our application instances live in. It's loaded by the Lion Engine and used by the rest of the framework to discover all the instances involved in a request.

The execution sequence order is, at the very top level, as shown in the following image:

![https://lion-framework.googlecode.com/git/doc/images/Lion/basic_lion_lifecycle.png](https://lion-framework.googlecode.com/git/doc/images/Lion/basic_lion_lifecycle.png)

The request is routed to the Lion Engine. The Lion Engine loads the Context Container and delegates to the Request Dispatcher.
The Request Dispatcher resolves some of the classes involved in the request, executes some logic and delegates to the Dual-MVC.
The Dual-MVC executes the logic itself to create a response in answer to the given request. Finally, the response is returned through upper layers.

## Lion Life-cycle ##
Now, let's zoom a little bit the execution sequence explained above:

![https://lion-framework.googlecode.com/git/doc/images/Lion/lion_lifecycle.png](https://lion-framework.googlecode.com/git/doc/images/Lion/lion_lifecycle.png)

  1. The request arrives to our index.php. The index.php does not belong to lion framework but to our application. It includes the Lion Engine in which delegates the execution to. Note that we must redirect all the request to the index.php file. Lion applications contains a .htaccess prepared to do that out of the box. The URL route engine of Lion will route the request to a controller or the other one depending on the URL.
  1. The index.php includes de lion.php file in which delegates to. As explained below, lion.php contains the Lion Engine.
  1. The Lion engine initializes the Application Context in which the rest of our application instances are contained in. Once the application context has been started-up, it loads the configuration which will be used to setup the rest of the instances.
  1. Once the Application Context has been initialized, the request is sent to the Url Route Engine which is the component in charge of parse the incoming request URL and determine the best route to redirect the request to. A route is basically the combination of the following information:
    * The intercepting filters to be executed to.
    * The front controller to handle the request to.
    * The controller to handle the request.
    * The parameters to be added to the request (or modified) previous to call the controller.
  1. After the route is resolved, the request is intercepted by the filters associated to it. Filters have logic to be executed before and after calling the front controller.
  1. Once the filters has been executed, the Front Controller get the control. The front controller contains generic logic associated to each kind of request. i.e. the HttpFrontController contains logic associated to HTTP incoming requests while the CommandLineFrontController contains logic associated to command line executions.
  1. The front controller delegates to the Dual-MVC. In that sense, an Action Controller is resolved to attend the request. The Action Controller is a class usually representing the requested page (in a web environment) which usually retrieves a portion from the Model to send to the view, according to the MVC pattern.
  1. Finally, the controller returns the control to the view. The view is the MVC piece in charge of rendering the user interface, it is, how the information will be shown to the user.
  1. While the view is being executed, the Event Handler associated to it will be called. In that sense, the view delegates to the Event Handler in order to perform some operations associated basically to user interface controls. The Event Handler will be also able to access to the Model for his own purposes, like the Action Controller.
  1. Finally, the rendered view will be returned as part of the response to the Front Controller.
  1. The Front Controller at the same time returns the response to the upper layer, being executed again all the filters associated to the current route.