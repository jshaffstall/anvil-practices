# Anvil Design Practices

This document lists some of the deliberate design decisions I've made in my projects to save time and avoid repetitive code.  This will evolve as my choices do.

# Use Hash Routing

The [Hash Routing library](https://github.com/s-cork/HashRouting) simplifies so many aspects of navigation from form to form, and allows users to use their 
browser's Back button in the way that they expect.

# Write For The Standard User

In previous projects I'd had a lot of error handling code that returned error messages for situations that were really exceptions.  The situations would only happen
if a user were trying to hack the system.  Now I catch those situations and raise an exception, knowing that normal users will never see them.

In server functions, for example, I've used decorators to check permissions and raise exceptions when a function is being called by someone who shouldn't be calling it.
Not taking care of this in the function itself has taken many of my server functions down to just one or two lines of code.

# Have Server Functions Do One Thing

In previous projects I'd have a server function do one thing if the user calling it was a normal user, and something more if the user calling it was an admin.  Now I 
have two separate functions, one for normal users and one for admins, with appropriate decorators to check permissions.  Both of those functions then call a utility
function (not marked as anvil.server.callable) to do the actual work.  

Here's an example from a recent project:

```
def add_mystery(mystery_title, author):
  mystery_id = str(uuid4())

  while app_tables.mysteries.get(mystery_id=mystery_id):
    mystery_id = str(uuid4())
    
  app_tables.mysteries.add_row(author=author,title=mystery_title,mystery_id=mystery_id)

@anvil.server.callable(require_user=is_admin)
def admin_add_mystery(mystery_title, author):
  add_mystery(mystery_title, author)
  
@anvil.server.callable(require_user=is_author)
def author_add_mystery(mystery_title):
  add_mystery(mystery_title, anvil.users.get_user())
```
