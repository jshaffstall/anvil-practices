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

## In a row template, self.parent is None

I get this error all the time when I try to access the repeating panel the row template is in too early.  self.parent will be None until the form_show event for the row template.  Before that, it's None.

Times that are too early:

- In the __init__ method
- In data bindings

