# Introduction #

In Lion, all the requests are redirected to the same .php file: the index.php file. It doen't matter if you request for a foo.html or a bar.php file, the index.php file will be executed instead of.

Note that the .htaccess redirects all incoming requests to the index.php.
That's the reason because you may need to enable the mod-rewrite (or equivalent) in your web server. For that purpose, our applications must have a .htaccess in order to perform the mentioned redirection.
In Lion we don't have multiple .php files to be executed directly from the browser, as we are accustomed in traditional php applications.
In that sense, a URL like http://example.com/foo.php does not corresponds to a file foo.php in the web-root of our server.

Instead of that, Lion parses every incoming request and determines where to route each request to based on our own rules.

![https://lion-framework.googlecode.com/git/doc/images/Request/tradition_php_and_lion.png](https://lion-framework.googlecode.com/git/doc/images/Request/tradition_php_and_lion.png)

This approach has several advantages, being some of them:

We can define and use friendly URLs easily (i.e. http://yourdomain.com/invoices/10948.html instead of http://yourdomain.com/index.php?module=invoices&view_id=10948)
We can change our URLs without refactorizing our file structure (i.e. changing http://example.com/catalog.html to http://example.com/games/catalog.html does not implies to create a games directory)
We can change our URLs without finding and replacing URL occurrences in our code. We just specify the new URL format and every occurrence of the URL will be adapted automatically by the framework.
We can configure filters (intercepting filter) associated to URLs declaratively.

## The URL Routing Engine ##
The URL Routing Engine is one of the key pieces within the framework: Is the one in charge of resolve where to route each request to.

In that sense, all the requests are sent to the Url Routing Engine in order to resolve a route or the other one based on the configuration where our routes are declared in.
Out of the box, routes are declared within the app/config/routes.xml file.

A route is basically the combination of 3 elements: A Front Controller to handle the request, an Action Controller to be executed and a set of Parameters to be added to the request.

Additionally, a route has associated some filters (intercepting filters) that are executed before the Front Controller receives the request and after it returns the response.