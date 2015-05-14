# What's the model? #

The model is the piece within your application containing your business logic. Because is a good practice to separate your business logic from the rest of your application clearly, lion implements a Model-View-Controller pattern to ease the application structuration, where the Model become one of the 3 pieces within this pattern.

See the Model-View-Controller section in order to know more about MVC pattern and how it's implemented in Lion

We can find as part of the model layer the following classes:
  * Your business classes: A set of value holder classes representing your business artifacts.
  * Your DAOs: Classes in charge of loading/saving your business objects from and to a storage engine (i.e. a database)
  * Your model services: Classes with the services that will be public from outside the model, representing the front-end to access to your model.
  * An ORM: A set of classes in charge of making transparent how business objects are persisted (i.e. making transparent the SQL usage by offering an API)
  * A database abstraction layer: A set of classes in charge of making transparent the concrete storage engine (i.e. the concrete database engine)

However, the model is not limited to those classes nor responsibilities, but to all the logic with a direct relationship to your business logic