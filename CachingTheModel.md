# Caching the model #

Another advantage of using model services approach is the caching capability.
We can declare caching properties associated to model by using the cache and cache-ttl attributes at service level.

```
<?xml version = "1.0" standalone="yes"?>

<configuration>

  <model-services>

    <!-- Invoice services -->
    <class name="InvoiceDao">
      <service name = "getAllInvoices" 
       class-method = "loadAll"
              cache = "true" 
          cache-ttl = "300"/>

      <service name = "getInvoice" 
       class-method = "load" 
              cache = "true" 
          cache-ttl = "60"/>

      <service name = "saveInvoice" class-method = "save"/>
    </class>

  </model-services>

</configuration>
```

In this example, we are caching the getAllInvoices service to expire after 300 seconds (ttl), as well as the getInvoices with a ttl of 1 minute

The cache-ttlis optional, being 0 by default, forcing the cache to never expire