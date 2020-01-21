<h1>OOP Design Ideas - Debug Messages in Classes</h1>

In this note I discuss my current (Jan 2020) design idea for controlling debug messages in KVS.

This design is for OOP which means you will be using classes and objects in your design and code.

<h2>The Story</h2>

Over the years I have had to work out ways to debug code as I make it. In KVS there's not really any decent debug system so you kind of have to design your own set up to track what's going on. At first, I used to use echo to output debug messages but it quickly becomes unusable as echo will output its message to whichever window is considered the current active window. you can control which window echo outputs to but then you're just doing a whole lot of work that isn't really needed.

<h2>The debug Command</h2>

The first thing you should know about is the debug command. debug outputs a message directly to the Debug Messages windows in KVIrc. The debug command also has a switch: -c which shows you the function name that the debug call was made from. This is ideal for tracking where messages are coming from and removes the need to deal with this yourself. See more: https://www.kvirc.net/doc/cmd_debug.html

<h2>Flow Control and Error Catching</h2>

A simple design for handling problems in your code's logic is to use else statements when conditions for the logic are not met: 

<pre><code>%argumentValue = $0

if (%argumentValue == $true)
{
	if (%optionDoThis == $true)
	{
		// ... do whatever the user wants the porogram to do
	}
	else
	{
		debug -c "Option DoThis was not ticked";
	}
}
else
{
	debug -c "Argument 0 was not found. Check data and try again.";
}
</code></pre>
If you follow this method, your program will only advance further into the nested levels of if (...) { } statements when all the needed data is avaiable, and/or when the user wants specific things to happen (done by changing settings). Any time that data is missing, the program will output a debug message telling you what's gone wrong and where it went wrong.

<h2>The Debug Toggle</h2>

Next, you probably want to employ a variable to toggle debug output on and off. This is simply done by creating the toggle value and then using an if () conditional with the debug command: 

<pre></code>%debug = $true

//... 

else
{
	if (%debug == $true) {debug -c "Argument 0 was not found. Check data and try again.";}
}</code></pre>

Notice that I have used a semi-colon at the end of the debug command. If you don't use it in a single-line statement in KVS, it will throw red errors at you, so you need to use it in these instances, even though KVS doesn't required semi-colons strictly!

<h2>Debugging in Class Objects</h2>

Using a debug variable like this works OK in Script Tester and simple scripts, but what about when you start building more advanced scripts using classes, objects and namespaces? It makes more sense to create a debug toggle variable as a class member rather than using some external, (probably) global variable. Inside the constructor of your top-level class for your project, you would declare a debug toggle member: 

<pre><code>@%debug = $true</code></pre>

Anywhere you need to check the debug toggle value inside your class code, you can simply do: 

<pre></code>else
{
	if (@%debug == $true) {debug -c "Your debug message here.";}
}</code></pre>

You can also check the debug value from <i>outside</i> the class, but you need to reference it in absolute form: 

<pre><code>%kvsCustomObject = $new(myCustomClass)
if (%kvsCustomObject->%debug == $true) {debug -c "This debug message was generated from outside the class object.";}</code></pre>

<h3>Debugging Across Multiple Classes in the Same Namespace</h3>

Namespaces in KVS let you group Classes together into something like a single project space. If you were scripting a project called "Cabji's KVIrc Improvement Scripts" you might make a Namespace called "ckis". Under this Namespace in the Class Editor in KVIrc, you would add/create all the classes you want and the code to do all the improved things.

When I start a new project, first I create an appropriately named namespace, then I create a class called "main" (because I originally learned C++ years ago). This main class is always the basis of my script/program and every builds out from there. In the constructor of the main class, the debug toggle member is made: 

<pre><code>// projectnamespace::main::constructor
@%debug = $true</code></pre>

As the project grows, additional Classes will or may be made. Once we make these additional Classes, we run into a <i>scoping</i> problem with our debug messaging system. When we execute a function that is from a different Class in our project's Namespace, if we want it to output debug messages we have to decide how to access the debug toggle member value.

<h4>The Sub-Par Ways to Read teh Debug Toggle Value</h4>

There are some ways of doing this debug messaging which 
