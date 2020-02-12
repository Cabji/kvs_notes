<h1>KVS - Widgets - Event Handling Problems and Using privateimpl to Deal With Them</h1>

This note is a rambling about something I found that was problematic for me handling some event's that the widget class in KVS has.

<h2>Scenario</h2>

I had built a program that had a multilineedit widget in it to load text from a file and allow the user to alter the content. I wanted to design the program so that the content of this multilineedit widget would be saved to disk automatically when the multilineedit widget lost focus (keyboard or mouse).

This seemed like a simple task to achieve, since if you look at the `widget` class (https://www.kvirc.net/doc/class_widget.html) in the KVIrc documentation you find that there is a function called `$focusOutEvent()` which "is called when this widget looses keyboard focus". I kind of figured that if this function was triggering when a focusOutEvent was occuring, I could just use `objects.connect` to link the event to a slot, right?

Wrong. No combination of event name would let me just plug this focusOut event into a slot of my choosing.

<h2>Solution</h2>

After a fair bit of mucking around with objects.connect I figured that my last chance at handling this event would be to use `privateimpl` (https://www.kvirc.net/doc/cmd_privateimpl.html) to make what I call an 'overloaded' function to handle the event. 

I really dislike using privateimpl to handle events triggered by objects because I find using objects.connect to be a lot cleaner when it comes to code organization. So the best solution I could come up with is below: 

<h3>Create a Function that Holds Any privateimpl Blocks You Need</h3>

When I first started learning KVS I used to use privateimpl and I would load them all in the class' constructor function. This works (functionality wise) but it ends up making your constructor code really huge, and you end up scrolling through searching manually for code you need to update. So I decided to create a function in the class called `loadPrivateImpls` which holds all the privateimpl blocks I need for my program.

<h3>Make the privateimpl Function Emit a Signal</h3>

In the loadPrivateImpls function, I declare any privateimpl I need: 

<pre><code>privateimpl (@%mleMultiLineEditWidget, "focusOutEvent")
{
	@$emit("lostFocus");
}</code></pre>

The privateimpl code is extremely simple. It handles when the `@%mleMultiLineEditWidget` has a "focusOutEvent". What do I do when I handle that event? I make it `emit` a signal called "lostFocus". 

<h3>Use objects.connect in the Class Constructor to Catch Your Signal</h3>

I decided to make the privateimpl function shoot out a signal instead of actually handling the event. Why? I can use objects.connect in the class `constructor` to declare a slot that will catch that signal! 

Using this technique, I can continue developing my program in the layout that I originally wanted.
