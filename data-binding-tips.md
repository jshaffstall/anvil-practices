# Data Binding Tips & Tricks

Data binding can get far more complex than the simple examples shown in the docs.  Here are some of my typical usages.

## React to length of multi-table links

There are times when I want a multi table link to appear as text based on whether there are links or not.  I use something like this in the text data binding for a label placed into that column in the template:

```
"Yes" if self.item['mysteries'] and len(self.item['mysteries']) > 0 else "No"
```

That same technique can be used to react to fields that may be None, such as dates.

## Date formatting

The default formatting for dates in data grids is never what I want for user facing forms.  I typically use a label on that column in the template and set the text binding to this:

```
self.item['created_on'].strftime("%b %d %Y %H:%M:%S")
```

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
