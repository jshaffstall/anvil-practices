# Tips & Tricks

Very little of this is my work, most came from the Anvil forums.  I've linked to the forum posts where I could find them.

## [Striped Data Rows](https://anvil.works/forum/t/alternate-row-colors-add-timestamp-on-check-event/4651/3)

Add a role called striped that applies to RepeatingPanel, then add the following CSS to your assets:

```
.anvil-role-striped .anvil-data-row-panel:nth-child(even) {
    background:  %color:Gray 200%;;
}
```

## [Expanding TextBox](https://anvil.works/forum/t/expandable-text-boxes/6059/8)

TextBox components don't like to expand when they are inside a FlowPanel.  To get them to do so, add the following roles to apply to FlowPanels:

```
.anvil-role-initial-fill .flow-panel-gutter > div:first-child {
  flex-grow: 1 !important;
}
```

```
.anvil-role-second-fill .flow-panel-gutter > div:nth-child(2) {
  flex-grow: 1 !important;
}
```

The initial-fill role will cause the first component in the FlowPanel to take up the remainder of the space, while the second-fill expands the second component.  

## Triggering Events in Data Grid Rows That Call Form Functions

There are times when you want a function in a data grid row template to call something from the form that the data grid is in.  get_open_form() doesn't always work, depending on how you are structuring your app.  This technique works nicely for me.

In the form's init, we setup an event handler on the repeating panel that calls a method on the form:

```
self.repeating_panel_1.set_event_handler('x-reload', self.reload_mysteries)
```

Now in the row template we can raise that event on the parent (which is the repeating panel):

```
self.parent.raise_event('x-reload')
```

The result is that the form's function gets called, and we don't need to worry about how many levels deep the repeating panel is in the component hierarchy.

## [Copy To Clipboard](https://anvil.works/forum/t/copy-to-clipboard-button/1817/10)

Add the following Javascript funtion to your Native Libraries section of your app:

```
<script>
  function copyText(texttocopy) {
    // Create new element
    var el = document.createElement('textarea');
    // Set value (string to be copied)
    el.value = texttocopy;
    // Set non-editable to avoid focus and move outside of view
    el.setAttribute('readonly', '');
    el.style = {position: 'absolute', left: '-9999px'};
    document.body.appendChild(el);
    // Select text inside element
    el.select();
    // Copy text to clipboard
    document.execCommand('copy');
    // Remove temporary element
    document.body.removeChild(el);
  }    
</script>
```
  
Then from any Anvil form, use code like this to copy something to the clipboard (the item to be copied must be a string):

```
self.call_js('copyText', username.text)
```

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
