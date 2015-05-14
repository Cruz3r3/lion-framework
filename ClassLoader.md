# Lion class loader #

Lion has a class loader, a class in charge of locate and include class files on demand, it is, as soon as they are needed by the code).

The main advantage of the class loader is the fact that it saves the effort of maintain and organize include directives, because it allow us to declare where our files are located in.

The class loader allow to map classes with their files by declaring rules in the includepath file (/app/config/includepath.xml).

We can group classes containing in the same directory by ussing the cluster tag. A cluster has an attribute to let the framework know where files are located in (all paths in a cluster are relative to the application):

```
<?xml version = "1.0" standalone="yes"?>
<classes>

  <cluster name="My classes" path="/libs/classes">
    ...
  </cluster>

</classes>
```

Inside a cluster we can declare in which files are located our classes as well as our interfaces by ussing the class and interface tag respectively:

```
<?xml version = "1.0" standalone="yes"?>
<classes>

  <cluster name="My cluster" path="/libs/classes">
    <class name="MyClass" file="MyClass.php"/>
    <interface name="MyIFaz" file="MyIfaz.php"/>
  </cluster>

</classes>
```

We can also use wildcards in order to establish naming conventions.
i.e. We can declare that whatever file with the suffix .class.php in a cluster corresponds to a class with the same name of the file (i.e. the file MySuperCoolController.class.php contains the definition of the MySuperCoolController class)

```
<?xml version = "1.0" standalone="yes"?>
<classes>

  <cluster name="My classes" path="/libs/classes">
    <class name="*" file="*.class.php"/>
  </cluster>

</classes>
```

Finally, we can map a cluster to a directory and all the sub-directories recursively by ending the path with 3 dots (...)
i.e.

```
<?xml version = "1.0" standalone="yes"?>
<classes>

  <cluster name="My classes" path="/libs/classes/...">
    <class name="*" file="*.class.php"/>
  </cluster>

</classes>
```

So, in this example we are applying the same naming convention, affecting not just to files containing in /libs/classes but all the files in all the sub-directories recursively.

## includepath.xml files ##
Whatever file names as includepath.xml and located wherever you prefer is read by the class loader in order to get mapping rules.
Initially we have the app/config/includepath.xml file with the following content:

```
<?xml version = "1.0" standalone="yes"?>
<classes>
 
  <!-- Components -->
 <cluster name="Components" path="/libs/components/...">
    <class name="*" file="*.class.php"/>
    <interface name="*" file="*.interface.php"/>
  </cluster>

  <!-- Event Handlers -->
  <cluster name="Event Handlers" path="/libs/eventhandlers/...">
    <class name="*" file="*.class.php"/>
    <interface name="*" file="*.interface.php"/>
  </cluster>

  <!-- Controllers -->
  <cluster name="Controllers" path="/libs/controllers/...">
    <class name="*" file="*.class.php"/>
    <interface name="*" file="*.interface.php"/>
  </cluster>

  <!-- Model classes -->
  <cluster name="Model" path="/libs/model/...">
    <class name="*" file="*.class.php"/>
    <interface name="*" file="*.interface.php"/>
  </cluster>

  <!-- Plugin classes -->
  <cluster name="Plugins" path="/libs/plugins/...">
    <class name="*" file="*.class.php"/>
    <interface name="*" file="*.interface.php"/>
  </cluster>

</classes>
```

Here we have defined some clusters, so Lion will search recursivelly for files matching the defined patterns in order to establish the mapping between classes/interfaces and their respectively files.

However, all of them have the same naming convention: Classes are contained in files with the same name plus the suffix .class.php while interfaces use the suffix .interface.php

## Declaring third-party autoloaders ##
Some frameworks like Doctrine may have their own class autoloaders.
In order to integrate propertly those autoloaders within the Lion class loader, the includepath file allow to declare third-party autoloaders via the autoload tag:

i.e.

```
<?xml version = "1.0" standalone="yes"?>
<classes>

  ...
  <autoloader class="Doctrine" method="autoload" />
  ...

</classes>
```

In the example above we are declaring the Doctrine::autoload method as an autoloader. So, we just need to define where the Doctrine class is located in, since the doctrine autoloader will be the class in charge of locate the rest of files.

## Benefits of using the class loader ##
There are 3 main benefits on using the class loader:

  * To avoid the use of include directives within php files. On small project this doesn't matter, but on relative big projects it becomes a great advantage.
  * As consequence of the previous point, refactoring the code by moving files from one to other folder is a really easy task because just implies to adapt the specific includepath files without altering the code at all.
  * To plug new libraries ready to use on lion by just adding new includepath files. Again, code does not need to know where libraries are located on.