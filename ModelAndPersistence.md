# Adding Persistence #

There are more like hundred of elegant approaches to equip our model with persistence capabilities. However, lion does not implement any of them :)
Why? The answer is clear: Because there are more like hundred of frameworks to do that out there.

What Lion provides is an easy way to integrate them as part of the model layer.

# Integrating an ORM #

Nowadays, ORM is one of the best paradigms to manage the persistence vs. maintaining SQL and handling persistence via code directly.

ORMs make transparent the database as well as how instances are loaded and stored on it.
In this section, we're going to show some practices to integrate an ORM within the model layer.

## Doctrine ##

Doctrine is a PHP ORM (object relational mapper) for PHP 5.2.3+ that sits on top of a powerful PHP DBAL (database abstraction layer). One of its key features is the ability to optionally write database queries in an OO (object oriented) SQL-dialect called DQL inspired by Hibernates HQL. This provides developers with a powerful alternative to SQL that maintains a maximum of flexibility without requiring needless code duplication.

## Including Doctrine ##

Doctrine has the following directory structure out of the box:

![https://lion-framework.googlecode.com/git/doc/images/Model/DoctrineDirStructure.png](https://lion-framework.googlecode.com/git/doc/images/Model/DoctrineDirStructure.png)

The only file that we're going to include is the Doctrine.php, placed within the doctrine lib directory. The Doctrine.php contains the main class Doctrine, which has an autoload method in charge of include the rest of files as soon as they are needed.

First of all, we need to place Doctrine inside our project. A good practice is to use a directory to place all the third-party libraries. We usually place our libraries within a directory thrdparty under the libs (/libs/thrdparty/) just to keep a placing convention, but it could be placed wherever

Second, we need to include the Doctrine.php file, so we can do that easily by adding an entry within the app/config/includepath.xml file:

```
<?xml version = "1.0" standalone="yes"?>
<classes>

  ...

  <cluster name="Doctrine classes" path="/libs/thrdparty/doctrine/lib">
    <class name="Doctrine" file="Doctrine.php"/>
  </cluster>

  ...

</classes>
```

So, the Doctrine.php file will be included just when needed, more like on demand, it is, just when the Doctrine class is used.
Now let's register the Doctrine autoload function that we have told about within our configuration file as following:

```
<?xml version = "1.0" standalone="yes"?>

<configuration>

  <configuration-directives>
    <autoload class="Doctrine" method="autoload"/>
  </configuration-directives>

</configuration>
```

This will register the Doctrine autoload callback instead of ussing the spl\_autoload\_register function directly within our code.
Now we are able to consume doctrine API directly from our model.

## Configuring Doctrine ##

We recommends to use Lion's dependency injection capabilities to configure Doctrine. It eases and centralizes the doctrine configuration. Let's see it with some examples:

The following code has been extracted from doctrine documentation, explaining how to use PDO to create a connection:

```
<?php

$dsn = 'mysql:dbname=testdb;host=127.0.0.1';
$user = 'dbuser';
$password = 'dbpass';

try {
    $dbh = new PDO($dsn, $user, $password);
    $conn = Doctrine_Manager::connection($dbh);
} catch (PDOException $e) {
    echo 'Connection failed: ' . $e->getMessage();
}

?>
```

Well, we can use dependency injection to setup a connection, let's see it in terms of context instance definitions:

```
<?xml version = "1.0" standalone="yes"?>

<configuration>

  <context-instances>

    <!-- Setup the dbh instance: -->
    <context-instance id = "dbh" class = "PDO">
        <constructor-arg>
           <value>mysql:dbname=testdb;host=127.0.0.1</value>
        </constructor-arg>
        <constructor-arg><value>dbuser</value></constructor-arg>
        <constructor-arg><value>dbpass</value></constructor-arg>
    </context-instance>      

    <!-- define the doctrine connection instance as the result
         of calling the connection factory method within the
         Doctrine_Manager class: -->
    <context-instance id = "conn"
                   class = "Doctrine_Manager"
          factory-method = "connection"/>
        <constructor-arg><ref id="dbh"/></constructor-arg>
    </context-instance>

  </context-instances>

</configuration>
```

Now it's as easy as get the conn instance by asking the application context:

```
<?php

    //gets the conn instance from the application context:
    $conn = __ApplicationContext::getInstance()->
                                  getContextInstance('conn');

```

Take the followin benefits of configuring Doctrine with Lion's dependency injection:
  * Maintain a configuration file declarativelly (vs. maintain a PHP source code programatically)
  * Let Lion to discover and resolve dependency between instances (vs. keep a concrete order to create instances programatically)
  * The connection instance is managed by the application context: it can be retrieved from whatever part within our code (vs. implementing singleton mechanism to make available our connection instance)