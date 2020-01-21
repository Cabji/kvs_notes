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

