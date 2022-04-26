# Tips & Tricks

Very little of this is my work, most came from the Anvil forums.  I've linked to the forum posts where I could find them.

## Trigger Password Change Dialogue

Anvil's Users service has a built-in password change mechanism.  When the user clicks the link in their email your app will launch, and the first time the app executes `anvil.users.get_user()` on the client the password change dialogue will appear.  To make sure that dialogue appears right away as the user expects, always execute `anvil.users.get_user()` in your startup form/module, even if you don't need to for other reasons.

## [Copy To Clipboard](https://anvil.works/forum/t/copy-to-clipboard-button/1817/10)

You can use the following straight from Python `js.window.navigator.clipboard.writeText(text)`.  Note that you'll need to run the app in a new tab if running from the IDE, to avoid permission issues.

## Showing & Hiding The Sidebar

The sidebar navigation pane in Anvil is very useful, but now and then I had pages where I wanted to not show it.  There are Javascript functions already in Anvil for doing this, but I needed a slight modification to the one to show it to prevent a flickering effect.

Here's the modification:

```
  function showSidebar() {
    var ln = $('.structure > .nav-holder > .left-nav');
    if (ln.is(":visible") || $('.nav-shield').is(".shown")) return;
    $('.nav-shield').addClass("shown");
    ln.addClass("shown").removeClass("hidden").css({left: "-100%"}).css({left: -ln.outerWidth()}).animate({left: 0}, function() {
      ln.removeClass("in-transition");
    });
    $(window).trigger('resize');
  }
```

The addition is that line 3 check to return if it's already visible (meaning there's no work to be done, so we don't want to mess with it).  This prevents the flicker as the resize event gets triggered again.

You can call this from a form_show event like this:

```
self.call_js('showSidebar')
```

To hide the sidebar, use:

```
self.call_js('hideSidebar')
```

## HTTP Endpoints & JSON Requests

When writing an HTTP endpoint that is going to take JSON data in the request, you must enable CORS and return the right values in the pre-flight OPTIONS request.  This is an example:

```
@anvil.server.http_endpoint("/order/total", methods=["POST", "OPTIONS"], enable_cors=True)
def total_order(**kw):
  # This allows JSON data to be sent to this endpoint
  if anvil.server.request.method == 'OPTIONS':
    r = anvil.server.HttpResponse()
    r.headers['access-control-allow-headers'] = 'Content-Type'
    return r
    
  return "whatever"
```

You can return whatever you want (a dictionary, for example) from the endpoint itself.  The OPTIONS request is made by the browser before the actual request, to see if the API call should be allowed.

## [Datetime Comparisons](https://anvil.works/forum/t/time-zone-issues/452/3)

Comparing datetimes generated locally with ones fetched from the server means you have to generate the local ones timezone aware.  Use this code to get the current datetime:

```
datetime.datetime.now(anvil.tz.tzlocal())
```

## Getting The User's Timezone

The following code will get the user's timezone (as reported by their browser):

```
    from anvil.js.window import Intl
    
    time_zone = Intl.DateTimeFormat().resolvedOptions().timeZone
```

The timezone is in the TZ format, the same format used by the PyTZ package, so can be sent to the server and used there.

## Filling Dropdowns with None option

Note that Anvil has made this unnecessary.  Dropdowns now have an Include Placeholder checkbox that allows you to specify what the None option for the dropdown says.

When filling a dropdown from a data table search, there's a standard formula.  In the following assume that results has the results of a data table search:

```
self.dropdown_1.items = [(r['title'], r) for r in results]
```

This gets the title displaying but the entire row returned as the selected value.  Sometimes you want the dropdown to have the option to have none of the results selected.  Use this technique:

```
self.dropdown_1.items = [('', None)] + [(r['title'], r) for r in results]
```

