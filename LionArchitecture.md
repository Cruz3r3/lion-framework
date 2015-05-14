# Architecture overview #

Lion is compounded by 3 main pieces:

  * The Request Dispatcher System: The most in top layer just in front of the client. This is the one in charge of request handling tasks.
  * The Context Container: This layer represents the application itself. All the application specific logic is contained within this layer.
  * The Dual MVC: A combination of a MVC and a MVP together. MVC gives the framework a clear layer separation while MVP eases the creation of rich/ajax based user interfaces.

Apart from that, there are a bundle of general purpose libraries and classes used by all the framework, such the error handling module, the I18n system and the event management system.

![https://lion-framework.googlecode.com/git/doc/images/Lion/lionarchitecture.png](https://lion-framework.googlecode.com/git/doc/images/Lion/lionarchitecture.png)

# The Request Dispatcher #

The request dispatcher system is one of the lion key parts. As mentioned, is the most in top layer listening for client requests.
It has been designed and optimized to be as lightweight as possible due to the execution frequency that it represents.

The Request Dispatcher System is compounded by 3 main parts:

## The Url Routing Engine ##

This is the Lion URL rewriting engine, delegating the URL parsing from the webserver to Lion itself.
Different URL formats accepted by Lion are specified in route definitions. Each route describes how to map an URL in terms of Lion components, it is: which action/controller has request the client side, which front controller will attend the request and which parameters will be passed as part of the request.
The url routing engine has also the capacity to perform the transformation in the oposited way, it is, to compose an URL as from the envolved components.

For more information regarding the Url Routing Engine, please see the URLs and Routes section.

## The Request Filter Chain ##

This is an intercepting filter in top of the Front Controller.

The intercepting filter is a pluggable mechanism to process common services in a standard manner without requiring changes to core request processing code.
The filters intercept incoming requests and outgoing responses, allowing preprocessing and post-processing.
Lion allows to add and remove these filters unobtrusively, without requiring changes to our existing code.

For more information regarding the Request Filter Chain, please see the Request Intercepting Filters section.

## The Front Controller ##

The front controller provides a centralized entry point that controls and manages Web request handling. It's the initial point of contact for handling the request in terms of the application.
Of course, any filter within the associated filter chain will be executed before the front controller.

For more information regarding the Front Controller, please see the FrontController.pkg section.

# The Context Container #

Lion framework is organized by contexts, where each context represents a concrete application. At the very high level, a context is just a space where instances collaborate together without interfering with other contexts. i.e., a context manages his own session and cache, avoiding conflicts with other contexts running at the same time.

## Context Instances ##

Context instance is the name that receives an instance managed by a context container in Lion framework. If you are familiar with Spring framework, context instance is more like the bean concept.

To learn more about context instances and how they are managed by the context container, take a look at Dependency Injection

## The Security Framework ##

Lion has his own framework to handle security. The security layer is a session based RBAC (Role Based Access Control), where instances are protected by permissions and permissions are group by roles.

For more information regarding the security layer, please see the Security in Lion section.

## Session, Cache and Logging facades ##

Well, Lion framework does not implement mechanism to handle the session, nor the cache nor the logging, but provides several interfaces to the most notable frameworks and libraries for that purpose.

A context container has a method to retrieve each one, bound to each context

For more information regarding the session facade, please see the The Session section.

For more information regarding the cache facade, please see the The Cache facade section.

For more information regarding the logging facade, please see the The Logging facade section.

## The Configuration Management System ##

The Lion configuration system features an infrastructure opened for extension.
It's the way that Lion exposes to define the configuration parameters within an application context. Again, Lion integrates the configuration management system at context container level, it is, configuration parameters just affect to the instance within the same ampplication context where they are defined in

For more information regarding the configuration management system, please see the Configuration section.

# The Dual-MVC #

Lion is compound by 2 different MVC approaches together:

  * The push-style MVC, which is the classic design pattern which decouples data access and business logic (model) from presentation layer (view) by introducing an intermediate component: the controller.
  * The pull-style MVC, also known as component based, where the view is able to "pull" data from multiple controllers as needed. This approach eases the creation of rich web user interfaces.

![https://lion-framework.googlecode.com/git/doc/images/MVC/dualmvc.png](https://lion-framework.googlecode.com/git/doc/images/MVC/dualmvc.png)

To be concrete, the component based MVC is one of the major efforts invested in lion:
Lion component model is similar to the AWT GUI component model. It has events and properties just like the Swing component model. It also has containers that contain components, and that also are components that can be contained by other containers.
In that sense, the Lion component model is divorced from traditionals view layers. Lion view allows not just to define the interface look and feel but dyncamic behaviors.

For more information regarding the Model-View-Controller, please see the Model-View-Controller section.

For more information regarding the Component based UI, please see the The Component Model section.

# General purpose libraries #

Lion has a bundle of general purpose libraries designed as helper to perform all the common tasks needed by the rest of layers, to highlight:

  * The I18n management system, in charge of all the I18n aspects of the application.
  * The event management system, in charge of event broadcasting and listener callback executions.
  * The error management system, a way to centralize and organize the error and exception handling.

## The I18n System ##

Lion brings his own I18n framework, perfectly integrated within the rest of the architecture in order to use language specific literals wherever we prefer within our code.

For more information regarding the I18n management system, please see the I18n section.

## The Event Management System ##

todo

## The Error Management System ##

todo