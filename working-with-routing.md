# Working With Hash Routing

The Hash Routing dependency is amazing and will be used for all my projects.  But, once you start using it you need to make some changes.

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
  if type(err) == anvil.server.SessionExpiredError:
    alert('Your session has timed out. Please refresh the page to continue', 
          title='Session Expired' , 
          buttons=[('Refresh Now', None)],
          dismissible=False)
    
    #self.call_js('refreshSession', anvil.server.get_app_origin()+"#"+routing.get_url_hash())
    routing.reload_page(True)
  elif type(err) == anvil.users.exceptions.AuthenticationFailed:
    alert("You don't seem to have the permissions to do that.  Are you still logged in?", title="Permission Error")
    routing.set_url_hash(error_redirect)
  else:
    alert(err.args[0], title="Something Bad Happened!")  
```

In my forms, I set ErrorHandling.error_redirect in the __init__ function, and then do whatever servers calls I need to do in the form_show function.  If an authentication exception is returned, the error handling code will display the vague permissions error and redirect them.

    
      


