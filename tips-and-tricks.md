# Tips & Tricks

Very little of this is my work, most came from the Anvil forums.  I've linked to the forum posts where I could find them.

## Striped Data Rows

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

## Copy To Clipboard

Add the following Javascript funtion to the bottom of your standard-page.html:

```
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
```
  
Then from any Anvil form, use code like this to copy something to the clipboard (the item to be copied must be a string):

```
self.call_js('copyText', username.text)
```

