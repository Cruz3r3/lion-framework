# Defining model services #

Lion proposes a way to separate the model from the rest of the application: by defining a set of services working as a gateway to the model.
Those services are what we have known as model services

Model services are defined within the configuration, by specifying where to route each service call to a real model class.
i.e. We could define a set of model services and redirect them to a concrete Dao:

```
<?xml version = "1.0" standalone="yes"?>

<configuration>

  <model-services>

    <!-- Invoice services -->
    <class name="InvoiceDao">
      <service name = "getAllInvoices" class-method = "loadAll"/>
      <service name = "getInvoice"     class-method = "load"/>
      <service name = "saveInvoice"    class-method = "save"/>
    </class>

  </model-services>

</configuration>
```

In this example, we are declaring some model services (getAllInvoices, getInvoice and saveInvoice) that will be routed to some InvoiceDao methods, to be concrete to loadAll, load and save.
Continuing with our example, the Dao that we are talking about could be more like the following one:

```
<?php

class InvoiceDao {

    public function loadAll() {
        ...
    }

    public function load($invoice_id) {
        ...
    }

    public function save(Invoice $invoice) {
        ...
    }

}

```

To call to a model service from outside the model layer, we'll use the ModelProxy instance (singleton).
i.e.:

```
<?php

    //retrieve all the invoices:
    $invoices = __ModelProxy::getInstance()->getAllInvoices();

    //retrieve a concrete invoice:
    $invoice  = __ModelProxy::getInstance()->getInvoice($invoice_id);

    //save an invoice:
    __ModelProxy::getInstance()->saveInvoice($invoice);

```

The first advantage of this approach is the fact that we are hiding how the model is structured internally by just exposing some services to be consumed from the rest of layers.
In our example, we are calling to getAllInvoices by ignoring that our call will be routed to a DAO:

![https://lion-framework.googlecode.com/git/doc/images/Model/ModelProxy.png](https://lion-framework.googlecode.com/git/doc/images/Model/ModelProxy.png)

Internally, the model proxy creates an InvoiceDao instance to call to his method. It also pass all the received parameters as them to return the received result.

Lion also allows to route service calls to context instance methods. To do that, we can use the instance tag instead of the class:

```
<?xml version = "1.0" standalone="yes"?>

<configuration>

  <model-services>

    <!-- Invoice services -->
    <instance id="invoiceDao">
      <service name = "getAllInvoices" class-method = "loadAll"/>
      <service name = "getInvoice"     class-method = "load"/>
      <service name = "saveInvoice"    class-method = "save"/>
    </instance>

  </model-services>

</configuration>
```

This is more like it could be done in a real project: Our DAOs have been initialized by dependency injection, so it's expected that they have been initialized with all their dependencies, i.e. the instance representing the database connection or the transaction manager to use:

```
<?xml version = "1.0" standalone="yes"?>

<configuration>

  <context-instances>

    <context-instance id="invoiceDao" class="InvoiceDao">
        <property name="connection"><ref id="localDbConnection"/></property>
    </context-instance>

    <context-instance id="localDbConnection" class="DbConnection">
      <property name="dbName"><value>myDb</value></property>
      <property name="dbEngine"><value>mysql</value></property>
      <property name="dbHost"><value>localhost</value></property>
      <property name="dbUser"><value>myUser</value></property>
      <property name="dbPassword"><value>secret</value></property>
    </context-instance>  

  </context-instances>

</configuration>
```

To learn more about dependency injection in lion, see the Dependency Injection section