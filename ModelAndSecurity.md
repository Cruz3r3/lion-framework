# Protecting The Model #

We can also protect some of our model services by assigning permissions.
i.e. we could force the security layer to check that the user in session has a concrete permission to be able to call the saveInvoice service:

```
<?xml version = "1.0" standalone="yes"?>

<configuration>

  <model-services>

    <!-- Invoice services -->
    <instance id="invoiceDao">
      <service name = "getAllInvoices" class-method = "loadAll"/>
      <service name = "getInvoice"     class-method = "load"/>
      <!-- Need the SAVE_INVOICES_PERMISSION to call to this service: -->
      <service name = "saveInvoice"    class-method = "save">
        <permission id="SAVE_INVOICES_PERMISSION"/>
      </service>
    </instance>

  </model-services>

</configuration>
```

To learn more about the security layer, see the Security in Lion section