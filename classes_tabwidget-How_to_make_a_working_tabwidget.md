<h1>A Closer Look at the tabwidget class in KVS</h1>


<h2>Structure for Layouts on Tab Pages</h2>

To use a tabwidget you need to build what I call ‘pages’ for it. A page is just another widget. Many examples show the use of hbox or vbox object in use with tabwidgets but they are not needed and it confused me for a long time about how to layout child widgets on each page.

When you want a window with a tabwidget on it, each tab needs a page attached to it that is displayed when the user clicks the tab. A widget object you can imagine as being like a blank piece of paper, or a page. You can create a layout object for each page widget to control the layout of each page’s child widgets.

Therefore, the structure to use to get a tabwidget working is the following: 

main window/class (widget) > main layout (layout) > tab widget (tabwidget) > tab 'page' widget (widget) > page layout (layout) > child widgets (the widgets the user clicks, whatever classes you like)

Pages are ‘attached’ to their corresponding tab using the $addTab() function in the tabwidget class object.
<h2>Code Example - OOP/Class/Object Driven</h2>

We can in fact make a fully working tabwidget in just one piece of code, we don’t need to make additional classes. Paste the following into a new class in KVS and build it, then create the object (in KVIrc input you can usually type: /%G_objectName = $new(classname) and it will create the object in memory and in this case it will display the object on screen for us) for the window to show up. 

```php
# to use a tabwidget, follow the structure of objects shown below:
# main window/class (widget) > main layout (layout) > tab widget (tabwidget) > tab page widget (widget) > page layout (layout) > child widgets (the widgets the user clicks, whatever classes you like)

# the main widget layout ("window" layout)
%l0 = $new(layout, $$)
$$->$setMinimumWidth(500)

# create a tabwidget
%tabwTabs = $new(tabwidget, $$)

# add the tabwidget to the layout of the main window widget
%l0->$addMultiCellWidget(%tabwTabs, 0,0,0,0)

# now create a widget object for each page.
%main = $new(widget)
%options = $new(widget)
%about = $new(widget)

# each page needs a layout, which is the child of the widget, which is the child of the page
%lMain = $new(layout, %main)
%lOptions = $new(layout, %options)
%lAbout = $new(layout, %about)

# now make child widgets that we can lay out using the layout objects
# main tab
%lblMainHello = $new(label, %main)
%lblMainHello->$setText("<b>Program</b><br/><br/>Welcome to our little program<br/><br/>Click the tabs to see the difference on each page.")

# options tab
%lblOptionsTitle = $new(label, %options)
%lblOptionsTitle->$setText("<b>Options</b>")
%lblOptionsValue = $new(label, %options)
%lblOptionsValue->$setText("User Defined Value:")
%lblOptionsValue->$setMinimumWidth(280)
%leOptionsValue = $new(lineedit, %options)
%leOptionsValue->$setMinimumWidth(100)
%chbOptionsToggle = $new(checkbox, %options)
%chbOptionsToggle->$setText("On or off?")

# about tab
%lblAboutTitle = $new(label, %about)
%lblAboutTitle->$setText("<b>About</b>")
%mleAboutUs = $new(multilineedit, %about)
%mleAboutUs->$setText("This small program was written to demonstrate how to use the tabwidget class in KVS.\nIt was written in March 2018.\nI hope you found it useful and that you can make some neat addons for KVIrc! :)")

# now lay the child widgets out on the page widgets

# main - simple, only 1 widget
%lMain->$addMultiCellWidget(%lblMainHello, 0,0,0,0)
# options - more complex, title at top spans 2 columns, value element needs 2 columns, toggle spans 2 columns
%lOptions->$addMultiCellWidget(%lblOptionsTitle, 0,0,0,1)
%lOptions->$addMultiCellWidget(%lblOptionsValue, 1,1,0,0)
%lOptions->$addMultiCellWidget(%leOptionsValue, 1,1,1,1)
%lOptions->$addMultiCellWidget(%chbOptionsToggle, 2,2,0,1)
# about - let's put the multilineedit in the top left corner and the title in the bottom right corner
%lAbout->$addMultiCellWidget(%mleAboutUs, 0,0,0,0)
%lAbout->$addMultiCellWidget(%lblAboutTitle, 1,1,1,1)

# now add the pages to the tabwidget

%tabwTabs->$addTab(%main, "Main")
%tabwTabs->$addTab(%options, "Options")
%tabwTabs->$addTab(%about, "About")

# set some alignment options for some child widgets too
%lMain->$setAlignment(%lblMainTitle, center)
%lOptions->$setAlignment(%lblOptionsTitle, center)

# show the main widget
%tabwTabs->$show()
```
<h2>Code Example - not OOP/Class/Object (for use in aliases; contributed by <a href="https://github.com/alexzulu">alexzulu</a>)</h2>

```php
# to use a tabwidget, follow the structure of objects shown below:
# main window/class (widget) > main layout (layout) > tab widget (tabwidget) > tab page widget (widget) > page layout (layout) > child widgets (the widgets the user clicks, whatever classes you like)

# the main widget layout ("window" layout)
# if you want to use tabWidget class in aliases

# create a tabwidget
%tabwTabs = $new(tabwidget)
%tabwTabs->$setMinimumWidth(500)

# now create a widget object for each page.
%main = $new(widget)
%options = $new(widget)
%about = $new(widget)

# each page needs a layout, which is the child of the widget, which is the child of the page
%lMain = $new(layout, %main)
%lOptions = $new(layout, %options)
%lAbout = $new(layout, %about)

# now make child widgets that we can lay out using the layout objects
# main tab
%lblMainHello = $new(label, %main)
%lblMainHello->$setText("<b>Program</b><br/><br/>Welcome to our little program<br/><br/>Click the tabs to see the difference on each page.")

# options tab
%lblOptionsTitle = $new(label, %options)
%lblOptionsTitle->$setText("<b>Options</b>")
%lblOptionsValue = $new(label, %options)
%lblOptionsValue->$setText("User Defined Value:")
%lblOptionsValue->$setMinimumWidth(280)
%leOptionsValue = $new(lineedit, %options)
%leOptionsValue->$setMinimumWidth(100)
%chbOptionsToggle = $new(checkbox, %options)
%chbOptionsToggle->$setText("On or off?")

# about tab
%lblAboutTitle = $new(label, %about)
%lblAboutTitle->$setText("<b>About</b>")
%mleAboutUs = $new(multilineedit, %about)
%mleAboutUs->$setText("This small program was written to demonstrate how to use the tabwidget class in KVS.\nIt was written in March 2018.\nI hope you found it useful and that you can make some neat addons for KVIrc! :)")

# now lay the child widgets out on the page widgets

# main - simple, only 1 widget
%lMain->$addMultiCellWidget(%lblMainHello, 0,0,0,0)
# options - more complex, title at top spans 2 columns, value element needs 2 columns, toggle spans 2 columns
%lOptions->$addMultiCellWidget(%lblOptionsTitle, 0,0,0,1)
%lOptions->$addMultiCellWidget(%lblOptionsValue, 1,1,0,0)
%lOptions->$addMultiCellWidget(%leOptionsValue, 1,1,1,1)
%lOptions->$addMultiCellWidget(%chbOptionsToggle, 2,2,0,1)
# about - let's put the multilineedit in the top left corner and the title in the bottom right corner
%lAbout->$addMultiCellWidget(%mleAboutUs, 0,0,0,0)
%lAbout->$addMultiCellWidget(%lblAboutTitle, 1,1,1,1)

# now add the pages to the tabwidget

%tabwTabs->$addTab(%main, "Main")
%tabwTabs->$addTab(%options, "Options")
%tabwTabs->$addTab(%about, "About")

# show the main widget
%tabwTabs->$show()
```
