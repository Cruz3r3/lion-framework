## Introduction ##

One of the biggest issues in software systems nowadays is the management of dependencies between components. In many scenarios, software components need to know "which" components to communicate with, "where" to locate them, and "how" to communicate with them.

It becomes a problem when the way such services can be accessed is changed, such changes can potentially require the source of lot of clients to be changed.
i.e. if you have ever changed a constructor signature and realized that there are more like 20 different places where the constructor is called.

One way of structuring the code is to have the clients declare their dependency on services, and have some "external" piece of code assume the responsibility of locating and/or instantiating the services and simply supplying the relevant service references to the clients when needed. With this method, client code typically is not required to be changed when the way to locate an external dependency changes.
This type of implementation is what's known as dependency injection, being one of the today's latest development craze.

This pattern is commonly associated to Java lightweight containers, enterprise programming and similar stuffs, but it's weird to think about PHP. However, the concept goes behind the technology.

Lion framework opens an efficient PHP implementation which is more like a port of one of the most notable implementations in top of Java: the Spring framework. The reason is really simple: It's a fact that Spring approach has been strongly accepted by the community due to several reasons, so, why do we have to reinvent the wheel?

## The context container ##

Lion framework is organized by context containers, where each one represents a concrete application. At the very high level, a context is just a space where instances collaborate together without interfering with other contexts. i.e., a context manages his own session and cache, avoiding conflicts with other contexts running at the same time.

The context container is also the responsible of orchestrate the dependency injection process, being able to read the specification from a configuration file, delegating the instance's creation and dependency injection to other classes to finally managing instance life-cycles.

The easiest way to define instances and dependences is by declaring them into a XML configuration file, described shortly.

## Context instances ##

Context instance is the name that receives an instance managed by a context container in Lion framework. If you are familiar with Spring framework, context instance is more like the bean concept.

Anyway, context instances are created by an special factory designed just for that purpose: The InstanceFactory. The way to define instances and dependences is by ussing instance definition objects (InstanceDefinition). In fact, the context-instances configuration file is just translated in terms of instance definition objects by the context container.

Instance definitions provide the following information to the instance factory:

  * **A class name**: Commonly the class implementation of the context instance. However, it's allowed to define a factory method instead of using the constructor. In that case it will be the factory class name.
  * **Behavioral properties**: How the context instance should behave in the context container (i.e. if it will be a prototype or a singleton, initialization and destruction methods, ...)
  * **Properties**: A set of properties to setup the context instance once it's created. i.e. the database connection parameters such the host and port for a DAL instance.
  * **Dependences**: Which others context instances need to be set to the actual instance.

### Context instance creation ###

We have 3 ways to specify how to create a context instance:

#### Via constructor ####
This is the most simple and common way to declare instances: by just providing the class name which we want to use to create the instance.

i.e.

```
<?xml version = "1.0" standalone="yes"?>
<configuration>

  <instance>

    <instance id="exampleInstance" class="ExampleClass"/>

    <instance id="anotherExample"  class="ExampleClassTwo"/> 

  </instances>

</configuration>
```

The context factory will create the instance by just calling the constructor, without delegating to other components for that purpose.

We are also able to specify which arguments to use when calling the constructor, as described later.


#### Via factory method ####
Less common but likewise usefull in certain scenaries: by providing not just the class name but an static method (a factory method) within the class.
The instance factory will call the static method to obtain a new instance instead of calling directly to the constructor.

i.e.

```
<?xml version = "1.0" standalone="yes"?>
<configuration>

  <context-instances>

    <context-instance id="exampleInstance"
                   class="ExampleClass"
          factory-method="createInstance"/>

  </context-instances>

</configuration>
```

Again, we are also able to specify which arguments to use when calling the factory method, as described later.


#### Via factory ####
By ussing this way, we do not provide the class name but the factory class. Of course, a factory method is required for this scenary.

The instance factory will call the static method within the factory to obtain a new instance. Also note that there is an implicit dependence between the instance and the factory.

i.e.
```
<?xml version = "1.0" standalone="yes"?>
<configuration>

  <context-instances>

    <!-- The factory instance, which contains a method called
         createInstance -->
    <context-instance id="myFactoryInstance"
                   class="...">
      ...
    </context-instance>

    <!-- The instance to be created via the factory instance -->
    <context-instance id="exampleInstance"
        factory-instance="myFactoryInstance"
          factory-method="createInstance"/>

  </context-instances>

</configuration>
```

Again, we are also able to specify which arguments to use when calling the factory method, as described later.

### Instance identifiers ###
Context instances are usually identified by an unique id within the context container the instance belong to. The id is commonly defined as a context-instance level attribute within the XML configuration.

This id will be usefull later to retrieve a concrete instance from the context container. For that purpose, the container exposes a method (getContextInstance).

i.e.
```
<?php

//retrieve an instance from the context container:
$my_instance = __ApplicationContext::getInstance()->
                                     getContextInstance('exampleInstance');
```

### Instance scopes ###

Defining instances is like creating recipes for creating instances of a class defined by that instance definition. The fact that an instance definition is a recipe means that we can have many objects created from a single recipe.

However, we can manage not just dependencies and setup configurations to be plugged in objects to be created from a concrete instance definition, but the scope of the objects that we're going to create. This is a very powerfull approach, giving us the flexibility to define an scope of the objects from the configuration versus having to develop it within our PHP code.
Instances can be declared in one single scope: Lion Framework support four scopes:

singleton - Scopes a single instance definition to a single object instance per Context Container.
prototype - Scopes a single instance definition to any number of object instances.
request - Scopes a single instance definition to the lifecycle of a single HTTP request; that is each and every HTTP request will have its own instance of a instance created off the back of a single instance definition.
session - Scopes a single instance definition to the lifecycle of a HTTP Session.
i.e., if we have a resource (i.e. a PDO connection) which is not serializable into the session, we may want to declare it with scope=request.
By default, instances have scope=session, which means that once they are created, they are stored into the session.


### Singleton instances ###
The singleton pattern is extrapolated to context instances in the sense that we can declare if we want to get always the same instance (singleton) from the context container or a new one (prototype) every time we call the getContextInstance method.

By default, context instances are created in singleton mode, unless we specify otherwise.
A non-singleton instance means that the context container will return a clone of the requested instance. Lion will manage the instance life-cycle in a different manner depending on that mode.

i.e.
```
<?xml version = "1.0" standalone="yes"?>
<configuration>

  <context-instances>

    <!-- Prototype instance -->
    <context-instance id="exampleInstance"
                   class="ExampleInstance"
               singleton="false"/>

    <!-- Singleton instance -->
    <context-instance id="yetAnotherExample"
                   class="ExampleInstanceTwo" 
               singleton="true"/>

  </context-instances>

</configuration>
```

In this example, a new prototype instance of ExampleInstance will be returned every time we call to get it, while the same ExampleInstanceTwo instance will be returned since it's defined as singleton. Again, by omiting this attribute, Lion will treat an instance in singleton mode.

## Dependencies ##
As the basis of dependency injection, context instances just expose methods and properties waiting to be setted by the context container.
In that sense, instances delegate the responsability of locating and/or instantiating the services they depend on to the context container.

The way to do that is by defining which constructor arguments, arguments to a factory method or properties need to be called in order to set to.
The context container will inject those dependencies once it is able to do that. One of the context container big jobs is to resolve all the dependencies for all the instances before setting up them. This is how the inversion of control works

In lion, we have 2 ways to set dependencies: by passing them as constructor arguments or by calling setters

### Setter based injection ###
This way allow the context container to send all the collaborators to an instance by calling the setters of the dependant instance
i.e., if we need to set a database connection to a DAO, we can define a setter based injection by exposing a setter to receive the connection as argument

i.e.

```
<?php

class InvoiceDAO {

    /**
     * This variable represents our database connection.
     * 
     */
    private $_connection = null;

    ...

    /**
     * Setter for connection: It will set the connection instance
     * 
     */    
    public function setConnection(DbConnection $connection) {
        $this->_connection = $connection;
    }

    ...

}
```

Once we have define the way to inject dependencies via setter, we can declare dependencies in the XML format as following:

```
<?xml version = "1.0" standalone="yes"?>
<configuration>

  <context-instances>

    <context-instance id="myInvoiceDao" class="InvoiceDAO">
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

In this example, we are setting our Dao connection property by setting the connection instance defined as localDbConnection.

In the other hand, we are setting concrete values for the localDbConnection, to be concrete all the parameters to connect to our local database. Note the diference between setting a reference to another instance and defining values directly. In both cases, the context container will call to each setter once resolve dependencies of each one

Take a look at the following example to see different ways to establish dependencies via setters:

### Via setting a single value ###
We're able to declare values directly to be set to properties:

```
<?xml version = "1.0" standalone="yes"?>
<configuration>

  <context-instances>

    <context-instance id="myInvoiceDao" class="InvoiceDAO">

        <!-- via a single value -->
        <property name="id"><value>invoiceDao</value></property>

    </context-instance>

    ...    

  </context-instances>

</configuration>
```

You can set whatever literal value to whatever public setter in a class. This allow us to configure values to setup our instances declarativelly.
i.e., in our example, we have configured our connection instance with values to connect to our database.

### Via the value attribute ###
It's a short-cut of the previous case, by ussing the value attribute within the property.

```
<?xml version = "1.0" standalone="yes"?>
<configuration>

  <context-instances>

    <context-instance id="myInvoiceDao" class="InvoiceDAO">

        <!-- via the value attribute (shortcut of value node) -->
        <property name="id" value="invoiceDao"/>

    </context-instance>

    ...    

  </context-instances>

</configuration>
```

### Via assigning an array ###
Sometime, the way to set a property is by passing an array. So, Lion allow to declare arrays as properties as shown in the following example:


```
<?xml version = "1.0" standalone="yes"?>
<configuration>

  <context-instances>

    <context-instance id="myInvoiceDao" class="InvoiceDAO">

        <!-- via an array -->
        <property name="managedClasses">
          <list>
            <entry><value>Invoice</value></entry>
            <entry><value>InvoiceCollection</value></entry>
            <entry><value>Customer</value></entry>
          </list>
        </property>

    </context-instance>

    ...    

  </context-instances>

</configuration>
```

### Via assigning a hash ###
Similar to the previous case, but we can also set a key to each item by ussing the key attribute

```
<?xml version = "1.0" standalone="yes"?>
<configuration>

  <context-instances>

    <context-instance id="errorHandler" class="ErrorHandler">

        <!-- via a hash -->
        <property name="exceptionClasses">
          <list>
            <entry key="Model"><value>ModelException</value></entry>
            <entry key="Database"><value>DatabaseException</value></entry>
            <entry key="ORM"><value>ORMException</value></entry>
          </list>
        </property>

    </context-instance>

    ...    

  </context-instances>

</configuration>
```

### Via declaring an instance ###
We're able to declare a context instance as a property of another one as shown in the following example:

```
<?xml version = "1.0" standalone="yes"?>
<configuration>

  <context-instances>

    <context-instance id="myInvoiceDao" class="InvoiceDAO">

        <!-- via defining the reference directly: -->
        <property name="connection">
          <context-instance class="DbConnection">
            <property name="dbName"><value>myDb</value></property>
            <property name="dbEngine"><value>mysql</value></property>
            <property name="dbHost"><value>localhost</value></property>
            <property name="dbUser"><value>myUser</value></property>
            <property name="dbPassword"><value>secret</value></property>
          </context-instance>
        </property>

    </context-instance>

    ...    

  </context-instances>

</configuration>
```

In fact, this feature allow to be used recursivelly, meaning that you can establish whatever number of levels of instances contained inside other instances.

### Via referencing another context instance ###
Continuing with the previous case, sometime we need to set the same context instance to more than one instance, i.e., an instance representing the connection to a database could be set to more than one DAO instance.
For that purpose, there is a way to reference instances declared in our context as shown in the following example:

```
<?xml version = "1.0" standalone="yes"?>
<configuration>

  <context-instances>

    <context-instance id="myInvoiceDao" class="InvoiceDAO">

        <!-- via ref node -->
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

In this example we have reference the localDbConnection instance to be set in the connection property of the myContractorDao instance.
In general, the usage of references is the most common and best way to declare dependencies between instances, because open the configuration to reference instances in more than one place while let the configuration more clear and readable.

In the other hand, you can reference instances as part of an array or a hash, let see in the following example:

```
<?xml version = "1.0" standalone="yes"?>
<configuration>

  <context-instances>

    <context-instance id="myDaoManager" class="DaoManager">

        <!-- via a hash -->
        <property name="daos">
          <list>
            <entry key="Invoice"><ref id="invoiceDao"/></entry>
            <entry key="Company"><ref id="companyDao"/></entry>
            <entry key="Customer"><ref id="customerDao"/></entry>
          </list>
        </property>

    </context-instance>

    ...    

  </context-instances>

</configuration>
```

### Via the ref attribute ###
It's a short-cut of the previous case, by ussing the ref attribute within the property:

```
<?xml version = "1.0" standalone="yes"?>
<configuration>

  <context-instances>

    <context-instance id="myInvoiceDao" class="InvoiceDAO">

        <!-- via ref attribute (shortcut of ref node) -->
        <property name="connection" ref="localDbConnection"/>

    </context-instance>

    ...    

  </context-instances>

</configuration>
```

As you can see, the are too many ways to define dependencies between instances.


### Constructor based injection ###
This way allow the context container to send all the collaborators to an instance as constructor arguments
i.e., if we need to set a database connection to a DAO, we can define a constructor based injection by exposing the connection as a constructor argument

i.e.

```
<?php

class InvoiceDAO {

    /**
     * This variable represents our database connection.
     * 
     */
    private $_connection = null;

    /**
     * Constructor method: It will require a connection instance
     * 
     */    
    public function __construct(DbConnection $connection) {
        $this->_connection = $connection;
    }

    ...

}
```

Once we have define the way to inject dependencies via constructor, we can declare dependencies in the XML format as following:

```
<?xml version = "1.0" standalone="yes"?>
<configuration>

  <context-instances>

    <context-instance id="myInvoiceDao" class="InvoiceDAO">
        <constructor-arg><ref id="localDbConnection"/></constructor-arg>
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

In this example, we are defining which concrete DbConnection instance will be sent as parameter when building our ContractorDAO instance (identified as myContractorDao).

We can define dependencies as constructor argument in the same way as setters, being able the constructor-arg node to accept same formats as the property node.
Maybe a different is that we are able to establish the order in which arguments are passed when calling the constructor. i.e.:

```
<?php

class InvoiceDAO {

    /**
     * This variable represents our database connection.
     * 
     */
    private $_connection = null;

    private $_persistence_engine = null;

    /**
     * Constructor method: It will require a connection instance
     * 
     */    
    public function __construct(DbConnection $connection, 
                                $persistence_engine) {
        $this->_connection = $connection;
        $this->_persistence_engine = $persistence_engine;
    }

    ...

}
```

In this example, we are expossing 2 arguments for the constructor: The connection instance and the persistence engine as the second one.
Our configuration file could be like the following one:

```
<?xml version = "1.0" standalone="yes"?>
<configuration>

  <context-instances>

    <context-instance id="myInvoiceDao" class="InvoiceDAO">
        <constructor-arg index="0"><ref id="localDbConnection"/></constructor-arg>
        <constructor-arg index="1"><ref id="doctrine"/></constructor-arg>
    </context-instance>

  </context-instances>

</configuration>
```

However, the order the constructor arguments are defined is the one they'll be sent to the constructor in case the index attribute is not present


### The null value ###
Sometime, we need to initialize properties by passing null. To do that, we can use the null node as shown in the following example:

```
<?xml version = "1.0" standalone="yes"?>
<configuration>

  <context-instances>

    <context-instance id="myInvoiceDao" class="InvoiceDAO">

        <!-- do not use any database engine by default -->
        <property name="defaultDbEngine"><null/></property>

    </context-instance>

    ...    

  </context-instances>

</configuration>
```

In this example, a new prototype instance of ExampleInstance will be returned every time we call to get it, while the same ExampleInstanceTwo instance will be returned since it's defined as singleton. Again, by omiting this attribute, Lion will treat an instance in singleton mode.