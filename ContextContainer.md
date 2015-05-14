# Introduction #

Lion framework is organized by contexts, where each context represents a concrete application. At the very high level, a context is just a space where instances collaborate together without interfering with other contexts. i.e., a context manages his own session and cache, avoiding conflicts with other contexts running at the same time.

A context is linked to a physical directory where application's classes and configuration files are contained in, being able to handle his own configuration and classes.

The first context that lion loads is the one representing the main application (also known as the application context).

Getting the application context is really simple: there is a singleton instance representing it. The context also exposes some accessor to retrieve context contained instances such the session or the configuration.
i.e.

```
<?php

//get the application context:
$application_context = __ApplicationContext::getInstance();

//get the context configuration:
$configuration = $application_context->getConfiguration();

//get the context session:
$session = $application_context->getSession();

//get the context cache:
$cache = $application_context->getCache();
```