---
layout: post
title: Where do you store your Django signals?
categories:
  - web
  - django
permalink: /blog/2011/django-signals.html
---

If you're using Django to build your web application, more often than not there
is already a convention for organizing your modules and packages.  A typical
Django application is structured something like this:


    project/
        __init__.py
        app1/
            __init__.py
            forms.py
            models.py
            views.py
            signals.py
            utils.py
            urls.py
        app2/
            ...
        templates/
        settings.py
        urls.py
        ...

And so forth.  Overall, this structure is pretty manageable, though minor
deviations are common.

If there is significant interaction between your various relational models,
we often run into the requirement of performing an action based on an event
related to a model, such as a "save" event.  Keeping these actions decoupled
from the actual model is a textbook example where the *Observer* pattern
comes in handy.  This is exactly what Django's signals infrastructure aims to
provide.

As detailed in the example above, the typical placement of signal handlers related to an
application's models is in the application package itself, in a module aptly
named "signals".

The same reasoning that pulls your actions out of model save methods can be applied to your handlers.
As your application grows in complexity it also tends to happen that your signal handlers begin to
interact with models from several different applications.  In this case it is
not completely clear (at least to me) where the signals should live, and since signal logic
is by definition decoupled from the models there are few clues to let us know what handlers
a model's event might trigger since we can only associate a single application
with a signal handler.

The structure I'm experimenting with right now is to create a single signals
package underneath the project package to organize all signals code.  I can then
add as many modules to the signals package as I like, with informative names 
like "user_signals" or "sluggable_model_signals".  The file structure of the
signals package looks like:

    signals/
        __init__.py
        user_signals.py
        sluggable_model_signals.py
        ...

Signals/__init__.py is defined as:

    """ Module for registering centralized signals """

    __all__ = [
        'user_signals',
        'sluggable_model_signals',
        ]


    from example.signals import *

The __all__ keyword makes sure the modules can be imported using the "import \*"
functionality.  Finally, the example.signals package is added to INSTALLED_APPS,
before any of our defined applications to ensure the signals are included only
once and early enough to register properly.
