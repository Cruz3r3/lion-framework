## I get an error 55601: Unknown Url format ##
Ensure that your web server url rewrite engine is working (i.e. for Apache users: ensure that the mod rewrite module is enabled as well as the Apache AllowOverride directive).

Also check the .htaccess file located in the root directory of your application. Sometimes, depending on your server configuration, you may need to fix the directory where your application is located within your web server:

```
...
  <IfModule mod_rewrite.c>
    RewriteEngine On
    #fix the following directive if you experience problems with url redirections:
    your_application/directory/here  
  ...
```

Note: Lion has its own Url rewrite engine. A common mistake is to try and make a call directly to the "index.php" file from the browser (i.e. http://yourdomain/index.php).
Instead, try using an URL ending with the .html extension (i.e. http://yourdomain/index.html). This is the default url format out of the box for Lion. Don't worry though, you can change this behavior by customizing and defining your own routes. Take a look at URLs and Routes for more information on how to do this.

Also take a look at [System requirements](SystemRequirements.md) and Installation section.

## A Forbidden error is shown when I try to execute an application from the browser ##
This is commonly due to a permissions issue.
Just check that the web server user has enough permissions to access to both the Lion framework and to your application files.

## A 404 error is shown when I try to execute the index.html page ##
Your web server is not redirecting requests properly to the index.php file.

For Apache users, the most common causes include:

  * The **mod rewrite** module is disable.
  * The **AllowOverride** directive is disabled.

Take a look at System requirements section.

## I get a Javascript error on some tutorials, such as the "Hello World Deluxe" ##
There is a directive in your "settings.ini" file of your Application directory. Just make sure it is set to point to Lion's "forms" location.