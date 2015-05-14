# Why to rewrite URLs? #

URL rewriting is a method of creating search engine friendly URLs, also known as SEO (Search Engine Optimization) URLs. Dynamic URLs have apparently a negative effect on search engine ranking. To get around this issue the URL rewriting technique is used. URL rewrite tools can examine a website and produce a simplified link for each resource on the website.

i.e. the link:

http://example.com/index.php?module=invoices&view_id=10948

can be rewritten as

http://example.com/invoices/10948.html

As part of a web application usability regarding the URLs, general recommendation is that URLs be chosen so that they:

  * Are short.
  * Are easy to type.
  * Visualize the site structure.
  * Are guessable, allowing the user to navigate through the site by using intuition to fid resourcesoff parts of the URL.

# Routes #
Different URL formats accepted by Lion are specified within route definitions. A route is a way to describe the mapping between an URL and how to route the execution flow.
A route defines a pattern for the URL, in pure perl compatible regular expression syntax. First found route matching an URL will be the one used to process the request.

A route defines the following components:

  1. Which front controller will attend the request.
  1. Which action/controller will be executed.
  1. Which other parameters will be appended to the request.

## Defining a Route ##
The way to define routes is by adding them to the configuration (by default, the app/config/routes.xml file)

i.e.

```
<?xml version = "1.0" standalone="yes"?>
<configuration>

  <routes>

    <route id="invoices_route" uri-pattern="^\/invoices\/$invoice_id\.html$">
      <front-controller class="__HttpFrontController"/>
      <action controller="invoices" code="search"/>
      <parameter name="invoice_id" value="$invoice_id"/>
      <variable name="$invoice_id" var-pattern="^\d+$"/>
    </route>

</configuration>
```

In this example we are defining a route for URLs matching the regular expression \/invoices\/$invoice\_id\.html$. It is, URLs like /invoices/xxx.html, being xxx a number.
An URL matching this pattern will be routed to the invoices controller, executing the search action and passing the invoice id as a parameter.

The front controller class to attend the request will be the HttpFrontController class (this is the default one, so we can omit this line).

Note that the way to define variables within the URL regular expression is by ussing the prefix dollar ($) followed by an identifier. In our example, $invoice\_id.
It's also important to note that we can also impose a regular expression to restrict the value for a variable. In our example, we are restricting the value for $invoice\_id to the pattern ^\d+$.

According to that, the full regular expression that need to matches an URL in order to be routed by the invoice\_search route is the following one: \/invoices\/\d+\.html$
i.e. http://example.com/invoices/10948.html

Now, let's see a more generic example:

```
<?xml version = "1.0" standalone="yes"?>
<configuration>

  <routes>

    <route id="search" uri-pattern="^\/$module\/$search_id\.html$">
      <front-controller class="__HttpFrontController"/>
      <action controller="$module" code="search"/>
      <parameter name="search_id" value="$search_id"/>
      <variable name="$module" var-pattern="^(invoices|clients|accounts)$"/>
      <variable name="$search_id" var-pattern="^\d+$"/>
    </route>

</configuration>
```

In this example, we are reusing the same route definition for the controllers invoices, clients and accounts.

## Conditional parameters ##
Lion allows the use of conditional parameters, meaning parameters that will be added just if a condition is satisfaced.
Conditions can be defined in 2 different ways:

By checking if a variable has a concrete value, by ussing the if-equals tag.
By checking if a variable has been set, by ussing the if-isset tag.
i.e., checking the variable value:

```
<?xml version = "1.0" standalone="yes"?>
<configuration>

  <routes>

    <route id="search" uri-pattern="^\/$module\/$search_id\.$extension$">
      <front-controller class="__HttpFrontController"/>
      <action controller="$module" code="search"/>

      <if-equals variable="$extension" value="html">
        <parameter name="use_cache" value="1"/>
      </if-equals>

      <parameter name="search_id" value="$search_id"/>
      <variable name="$module" var-pattern="^(invoices|clients|accounts)$"/>
      <variable name="$search_id" var-pattern="^\d+$"/>
      <variable name="$extension" var-pattern="^(html|action)$"/>
    </route>

</configuration>
```

In this example, we are adding a variable (use\_cache) just in case the extension is 'html'.

i.e., checking if a variable has been set:

```
<?xml version = "1.0" standalone="yes"?>
<configuration>

  <routes>

    <route id="search" uri-pattern="^\/$module\/$search_id(\_$page_id)?\.html$">
      <front-controller class="__HttpFrontController"/>
      <action controller="$module" code="search"/>
      <parameter name="search_id" value="$search_id"/>

      <if-isset variable="$page_id">
        <parameter name="pagination" value="1"/>
        <parameter name="page_id" value="$page_id"/>
      </if-isset>

      <variable name="$module" var-pattern="^(invoices|clients|accounts)$"/>
      <variable name="$page_id" var-pattern="^\d+$"/>
      <variable name="$search_id" var-pattern="^\d+$"/>
    </route>

</configuration>
```

In this example, we are adding 2 variables (pagination and page\_id) just if the page\_id is set.