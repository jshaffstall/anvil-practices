## In a row template, self.parent is None

I get this error all the time when I try to access the repeating panel the row template is in too early.  self.parent will be None until the form_show event for the row template.  Before that, it's None.

Times that are too early:

- In the __init__ method
- In data bindings
