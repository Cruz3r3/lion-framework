# Development environment #
By default Lion is configured to work in **DEBUG MODE** which reduces development efforts because:

Configuration files can be changed without needing to delete the cache nor the session cookies. Lion detects when a configuration file has been changed, added or even removed. When it happens, Lion resets both the session and the cache, reloads the new configuration and caches it again.
Error messages are shown with the full trace

Classes and interface files are detected as soon as they are added, removed or altered. Since the class loader scans recursivelly for new classes and interface files, it caches the resultant tree to improve the performance. However, the class loader does not cache in DEBUG MODE.
Plugins and components can be added or removed in hot. The cache is updated automatically when a change is detected in that way.

However, there are 2 mayor issues with DEBUG MODE: Security and performance. So, DEBUG MODE is not recommended for production environment at all

# Production environment #
Turning DEBUG MODE off is recommended for production environment.

When Lion works without DEBUG MODE, everything is cached the very first time and Lion does not check for content changes in order to refresh the cache nor to reset the session.


# Configuring the DEBUG MODE value #
DEBUG\_MODE is configurable via the **app/lion.ini** file:

```
;Set as no for production environment
DEBUG_MODE = yes
```