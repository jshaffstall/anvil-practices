# Working With Hash Routing

The Hash Routing dependency is amazing and will be used for all my projects.  Here are some of the changes I've made in my projects to better work with hash routing.

## [Handling Session Timeouts](https://anvil.works/forum/t/routing-navigation-with-url-hash/3949/37)

If your app stays open but inactive for 30 minutes, the session will timeout.  All the data rows you've retrieved will no longer be valid, the user won't be logged in, etc.  The default Anvil handling of this is to refresh the page, but that refreshes at the base URL of your app, not the URL with hash routing for the current page. 

Add the following to your __init__ for your main routing form to fix this:

```
routing.on_session_expired()
```

This just gives the routing library permission to override Anvil's default handling of a session timeout.  Now when the user clicks to refresh the session, it'll reload the page with the hash routing intact.

## Handling Server Authentication Errors

I use decorators on my server functions to make sure that the user has sufficient permissions to execute them.  If they've bookmarked an internal page and go back to it when they're not logged in, that authentication exception will be sent back to the client.  We need to handle that on every page so that we can redirect the user back to an appropriate form for them to login again.

I have an error handling module that looks like this:

```
import anvil.server
import anvil.users
from anvil import alert

from HashRouting import routing

error_redirect = ""

def error_handler(err):
  if type(err) == anvil.users.exceptions.AuthenticationFailed:
    alert("You don't seem to have the permissions to do that.  Are you still logged in?", title="Permission Error")
    routing.set_url_hash(error_redirect)
  else:
    alert(err.args[0], title="Something Bad Happened!")  
```

In my forms, I set ErrorHandling.error_redirect in the __init__ function, and then do whatever servers calls I need to do in the form_show function.  If an authentication exception is returned, the error handling code will display the vague permissions error and redirect them.


    
      


