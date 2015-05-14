# What's the View? #

The View is the MVC layer in charge of displaying the information to the client.

See the Model-View-Controller section in order to know more about MVC pattern and how it's implemented in Lion

The benefit of using the View is that we abstract how the information is presented to the client, being able to modify our application's look and feel without affecting to any other layer.

Unlike other traditional request-driven MVC web frameworks, Lion uses a component-based approach that eases the rich web clients development.
This approach includes:

  * A set of APIs for representing user interface components and managing their states, handling events and input validation, converting values and supporting internationalization.
  * A default set of GUI components.
  * An event management system.
  * A server to client component binding.

Because doing a web 2.0 application continues being a hard task nowadays, component based views eases this task by allowing the reuse of interface functionalities encapsulated in components.

## What's a component based view? ##

Lion component model is similar to the AWT GUI component model. It has events and properties just like the Swing component model. It also has containers that contain components, and that also are components that can be contained by other containers.
In that sense, the Lion component model is divorced from traditionals view layers. Lion view allows not just to define the interface look and feel but dyncamic behaviors.

Examples of Lion components include calendar and rich HTML input components. You may never have time to write such components, but what if they already exists?
The component model lowers the barrier to entry to add more functionality to Web applications by making a commodity of common functionality.

# Calling the View #

A view is identified in Lion with an unique code. This code is declared within the /app/config/views.xml as well as information regarding the view such the template or the renderer to be used to.

The Action Controller usually returns information regarding the view code to be called to, otherwise the framework resolves it based on the Controller code (usually trying to execute a view with the same code).

i.e.

```
<?xml version = "1.0" standalone="yes"?>

<configuration>

  <view-definitions>

    <view code="*" class="__SmartyView">
      <property name="template" value="*.tpl"/>
      <property name="eventHandlerClass" value="*EventHandler"/> 
    </view>

  </view-definitions>

</configuration>
```

This is a typical views configuration. In this file we are declaring the SmartyView class for whatever view code (represented by the wildcard).
At the same time, we are informing the framework that the template to be used will be a .tpl file with the same name as the code of the view. Same is applicable to the name of the event handler class associated to each view.

i.e., for a view with code foo, the framework will try to use the template foo.tpl, and will try to associate the fooEventHandler class as the event handler associated to.