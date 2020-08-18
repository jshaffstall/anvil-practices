## Striped Data Rows

Add a role called striped that applies to RepeatingPanel, then add the following CSS to your assets:

`.anvil-role-striped .anvil-data-row-panel:nth-child(even) {
    background:  %color:Gray 200%;;
}
`

## Expanding TextBox

TextBox components don't like to expand when they are inside a FlowPanel.  To get them to do so, add the following roles to apply to FlowPanels:

`.anvil-role-initial-fill .flow-panel-gutter > div:first-child {
  flex-grow: 1 !important;
}`

`.anvil-role-second-fill .flow-panel-gutter > div:nth-child(2) {
  flex-grow: 1 !important;
}
`
The initial-fill role will cause the first component in the FlowPanel to take up the remainder of the space, while the second-fill expands the second component.  

