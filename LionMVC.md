# MVC in Lion #

Lion has a MVC implementation ready to be used by just adding our **Action Controllers** and **templates**.

Some of the most valuable features of Lion MVC are:

  1. MVC is Dual, as the integration of a MVC and a MVP pattern together.
  1. In that sense, the view layer is based on components: Components simplifies the development of rich user interfaces. Unlike other MVC frameworks, Lion uses a component-based View approach: Graphical components within the user interface correspond to instances in the server side. The state of UI components is synchronized between the client and the server. Out of the box, Lion uses Smarty for its display technology, but it can also accommodate to other display technologies.
  1. Reusable business layer: There are not contracts between the business layer and the rest of the system.  # To make it happens, the model is exposed by defining a set of services that acts as middleware, hidding the model classes to the rest of the application. This approach has a lot of advantages, i.e. the easy way to expose some of those services as webservices declarativelly.
  1. Clear separation between the model, the view and the controller.
  1. Authorization already integrated: We can protect and control the access to the model, the view and the controller declarativelly or programmatically. We can also perform some very low grain protections, i.e. restricting the viewable of some UI zones depending on user privileges.

## Basic life-cycle ##

The MVC life-cycle in Lion begins in an special class: The **Action Dispatcher**.
The Action Dispatcher is the class in which the **Front Controller** delegates to in order to execute the **Action Controller** associated to the incoming **Request**.

Once the ActionDispatcher receives a Request (normally from the Front Controller, but not limited to) it performs the following tasks:

  1. It resolves and call to an Action Controller. The Action Controller returns a portion of the **Model** as well as some information regarding the **View** to be executed to.
  1. After that, it resolves the View and sends the Model information to it.
  1. Finally, it setups the **Response** with the information provided by the View. The Response is sent back to upper layers.

![https://lion-framework.googlecode.com/git/doc/images/MVC/mvc_lifecycle.png](https://lion-framework.googlecode.com/git/doc/images/MVC/mvc_lifecycle.png)

The ModelAndView represents a portion of the model retrieved by the Action Controller as well as information regarding the view to execute to.

### Controllers and Actions ###

There are 2 different concepts in Lion that we need to understand clearly: The Controller and the Action.

A Controller is a class in charge of the execution of one or more actions, while an action is a the minimal execution unit, usually a Controller's method.