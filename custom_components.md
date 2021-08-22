## [Custom Component Properties](https://anvil.works/forum/t/error-binding-second-property-on-custom-component-to-a-label/9528/3)

That post shows a shortcut for creating properties in custom components.  

```
def component_binding_prop(prop_name):
  def fget(self)
    return self._props[prop_name]
  def fset(self, value):
    self._props[prop_name] = value
    self.refresh_data_bindings()
  return property(fget, fset)

class LabelTextBox(LabelTextBoxTemplate):
  def __init__(self, **properties):
    properties = default_props | properties
    self._props = properties
    self.init_components(**properties)
  
  title = component_binding_prop('title')
  error = component_binding_prop('error')
```

## [Custom Components & Data Bindings](https://anvil.works/forum/t/error-binding-second-property-on-custom-component-to-a-label/9528)

This post is a reminder that if you have data bindings to a property, you need to refresh data bindings in the setters.  The above simplifying code also shows doing this.

