# Data Binding Tips & Tricks

Data binding can get far more complex than the simple examples shown in the docs.  Here are some of my typical usages.

## React to length of multi-table links

There are times when I want a multi table link to appear as text based on whether there are links or not.  I use something like this in the text data binding for a label placed into that column in the template:

```
"Yes" if self.item['mysteries'] and len(self.item['mysteries']) > 0 else "No"
```

That same technique can be used to react to fields that may be None, such as dates.

## Datetime formatting

The default formatting for datetimes in data grids is never what I want for user facing forms.  I typically use a label on that column in the template and set the text binding to this:

```
self.item['created_on'].strftime("%b %d %Y %H:%M:%S")
```

Or, to get AM/PM times:

```
self.item['created_on'].strftime("%b %d %Y %I:%M %p")
```

## Displaying datetimes in datagrids in the user's local timezone

Dates are inconsistent in Anvil right now.  Date pickers will display a date in the user's local timezone, but data grids display them in the server's timezone (UTC).  This inconsistency causes you to see different times in different spots on your app.  

To get a datagrid to display a datetime in the user's local timezone, you must use a label on the column in the template.  But, you cannot use data binding since you have to call anvil.tz.tzlocal(), which isn't available in a data binding.  So you have to set the label's text in the __init__ method of the row template:

```
self.label_2.text = self.item['expires'].astimezone(anvil.tz.tzlocal()).strftime("%b %d %Y %I:%M %p") 
```

This got to be so annyoing to keep doing over and over, I wrote a custom component that retains the ability to data bind the date but also automates display in the user's local timezone: https://anvil.works/build#clone:OITEW55DKQRVGZFI=VBFGNSDV2H5XDWYM6S67HJ5Z

## Optional dates

For a date field that might be optional (such as an expiration date), we can combine the above techniques:

```
self.item['expires'].strftime("%b %d %Y %H:%M:%S") if self.item['expires'] else "Never"
```

## Formatting conditional on other fields

For example, if I have a field called percentage that controls how a number is displayed, I can use something like this:

```
str(self.item['discount']) + "%" if self.item['percentage'] else "${0:.2f}".format(self.item['discount']/100)
```
