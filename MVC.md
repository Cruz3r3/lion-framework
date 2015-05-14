# Introduction to the Dual MVC #

Lion is compound by 2 different MVC approaches together:
  * The push-style MVC, aka **Model View Controller or just MVC**, which is the classic design pattern which decouples data access and business logic (model) from presentation layer (view) by introducing an intermediate component: the controller.
  * The pull-style MVC, aka **Model View Presenter or MVP**, where the view is able to "pull" data from multiple controllers as needed. This approach eases the creation of rich web user interfaces.

![https://lion-framework.googlecode.com/git/doc/images/MVC/dualmvc.png](https://lion-framework.googlecode.com/git/doc/images/MVC/dualmvc.png)

For a better understanding of both patterns, let say that **MVC** is the piece in charge of executing the logic to show a page while **MVP** is the piece in charge of executing the logic associated to the page UI (i.e. the logic that is executed when the user click on a button or select an option from a drop-down list).

This does not mean that Lion executes the MVC before delegating in the MVP. i.e. When the user submit a form, the event associated to the submit is executed at the very beginning of the request, before giving the control to the MVC.

Lion allow the usage of the MVC, the MVP or both together on a per-request basis, which means that, for some request we may want to use just the MVC while for some other request we may prefer just the MVP or even both togethers.

For more information regarding the Model-View-Controller, please see the Model-View-Controller section.

For more information regarding the Model-View-Presenter, please see the Model-View-Presenter section.