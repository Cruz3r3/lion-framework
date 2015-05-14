# System Requirements #

Since Lion is a web application framework built on top of PHP, you will need a web server (i.e. Apache) and PHP installed.

## PHP Version ##
We recommend to use **PHP 5.3** for an optimal performance.

## PHP Configuration ##
In order to make Lion's reverse AJAX work correctly, you may want to disable zlib.output\_compression. However, you can use Apache mod\_deflate or its equivalent to compress output other than AJAX responses.

We also recommend changing output\_buffering to off in your php.ini file in order to make reverse AJAX to work propertly. For example, reverse AJAX is used by the asynchonous file uploader in order to refresh the upload progress bar.
However, it's important to note that this is not a requirement to make the framework function normally.

If you are using the php\_domxml extension, we recommend you disabling it in order to make the framework work properly since Lion uses some PHP-native XML classes which come into conflict with some classes of this module.


## PHP Recommended Extensions ##
If you use APC (Alternative PHP Cache), we recommend enabling the apc.rfc1867 directive in order to make the asynchonous file uploader component function normally. This component uses the APC module to update a progress bar while uploading files.

Note: APC is not a requirement to make the Lion framework work and it may be part of future PHP distributions (i.e. PHP6).

> IMPORTANT: You may need to enable the apc.stat\_ctime setting in your php.ini (apc.stat\_ctime=1) if you have APC enabled. Otherwise, you may experience unexpected behaviors in the output like blank screens or the wrong template rendered instead of the expected one.

## Apache mod\_rewrite (or equivalent) ##
Lion needs the Apache mod\_rewrite (or equivalent) enabled in order to route all the requests to the framework properly.

To configure mod\_rewrite in Apache properly, make sure that:

You have installed and enabled the Apache mod\_rewrite module in your server.
You have enabled the AllowOverride directive in your apache configuration (httpd.conf).
You have modified the correct Apache configuration file located in the directory where Apache is reading the configuration from.
Take into account that some configurations allow either one configuration file per user or per domain.

