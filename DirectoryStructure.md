# Lion directory structure #
After uncompressing lion, we're going to see the following directories:

![https://lion-framework.googlecode.com/git/doc/images/Lion/simple_directory_structure.png](https://lion-framework.googlecode.com/git/doc/images/Lion/simple_directory_structure.png)

The /app directory is where we're going to develop our application.

![https://lion-framework.googlecode.com/git/doc/images/Lion/directory_structure.png](https://lion-framework.googlecode.com/git/doc/images/Lion/directory_structure.png)

The /lion directory contains the framework code. We don't need to alter any content within this directory.

![https://lion-framework.googlecode.com/git/doc/images/Lion/include_lion_php.png](https://lion-framework.googlecode.com/git/doc/images/Lion/include_lion_php.png)

The index.php file is always executed for each single request (see Request Dispatcher section to learn why).
This file just includes the lion.php, a file containing the main core class: Lion, the lion engine, a class in charge of load and execute the rest of the framework.


## Lion Framework classes ##
All the lion framework classes are contained within the /lion directory. Almost all of them are located under the /lion/libs directory.
We do not need (ideally) to alter whatever content under that directory.

Lion classes have an typical naming convention to avoid conflicts with whatever other third-party library or even with our own application code: The name of all classes and interfaces start with a double underscore, (i.e. Lion, FrontController, AuthenticationManager, ...).

## Application classes ##
Our application classes should be placed under /app directory. In fact, almost all of them should be placed under /app/libs.
This directory contains some subdirectories out of the box to place our classes and files grouped by the kind of task they are designed to. i.e. Controller classes should be placed in the /app/libs/controllers, templates should be placed within the /app/templates, and so on.