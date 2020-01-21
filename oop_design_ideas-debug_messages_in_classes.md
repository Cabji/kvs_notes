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

<h4>The Sub-Par Ways to Read the Debug Toggle Value</h4>

There are some ways of doing debug messaging across classes which I don't recommend. I'm going to show these methods and explain why they are no good.

<h5>Absolute Reference to the Debug Toggle Value</h5>

Let's say we have 2 Classes in our Namespace:

<pre><code>// note: this code won't work as expected in Script Tester
// namespace::main::constructor
{
	@%debug = $true
}

// namespace::main::function1
{
	if (@%debug == $true) {debug -c "This will work";}
}

// namespace::sideClass::function1
{
	if (@%debug == $true) {debug -c "This won't work";}
}

%objMain = $new(namespace::main)
%objSide = $new(namespace::sideClass)
%objMain->$function1()
// outputs "This will work" in debug window
%objSide->$function1()
// outputs nothing! because @%debug in namespace::sideClass::function1 refers to a member value of sideClass not main</code></pre>

As the code comment says, function1 in sideClass is looking at a value that should exist in the sideClass object, not the main object.

<pre><code>// what if we make sideClass a child object of the main object?
%objMain = $new(namespace::main)
<b>%objSide = $new(namespace::sideClass, %objMain)</b>
%objSide->$function1()
// still outputs nothing!</code></pre>
It doesn't matter if the sideClass object is a child of the main object. It still looks at a member called %debug in the sideClass object because we use the @ operator which means "this" object.

At this point, we can easily be tempted to do this: 
<pre><code>// namespace::sideClass::function1
{
	if (<b>%objMain</b>->%debug == $true) {debug -c "This won't work";}
}</code></pre>

But this will also output nothing! Why? Because %objMain in the sideClass::function1 code is a local variable of that function.

The next temptation is to use a global variable: 
<pre><code>// namespace::sideClass::function1
{
	if (<b>%G_</b>objMain->%debug == $true) {debug -c "This won't work";}
}

<b>%G_</b>objMain = $new(namespace::main)</code></pre>

Now the debug output in sideClass::function1 will work but this method is undesirable. We are hard coding the name of the object that holds the debug toggle we're interested in. This will work, but if we ever decide to change the name of the main object class, we have a massive maintenance headache. It also makes our code unrecyclable.

<h5>Using a Seperate Debug Toggle Member in Each Class</h5>

<pre><code>// note: this code won't work as expected in Script Tester
// namespace::main::constructor
{
	@%debug = $true
}

// namespace::main::function1
{
	if (@%debug == $true) {debug -c "This will work";}
}

// namespace::sideClass::constructor
{
	@%debug = $true
}

// namespace::sideClass::function1
{
	if (@%debug == $true) {debug -c "This will work";}
}</code></pre>

This will work as well, but it means we have multiple debug toggle values, which means there is no single 'master switch' to turn all debugging messages on and off. That might work for you, but there is a way to have a single master switch across multiple classes in the same namespace.

<h4>Using Inheritance to Do Your Debug Toggle</h4>

One feature of OOP is that classes can inherit things from other classes. The method I use here is actually not <i>proper</i> inheritance that OOP offers, since we want to pass values <i>across</i> classes that aren't the same structure. To do this, we simply place a if (...) { } conditional in the constructor of our class and use the $parent() function that KVS supplies in its object Class.

<pre><code>//namespace::main::constructor
{
	@%debug = $true
}

//namespace::sideClass::constructor
{
	// inherit values from the parent object if it exists, else use default values
	if (@$parent == $true) 
	{
		@%debug	= @$parent()->%debug;
	}
	else 
	{
		@%debug	= $false;
	}
}

// create the objects
%objMain = $new(namespace::main)
%objSide = $new(namespace::sideClass, %objMain)
</code></pre>

This method solves 2 problems: 
<ol>
	<li>The value of %debug in %objSide is decided on creation, and is pulled from the parent object which in this case is %objMain</li>
	<li>The name of the debug toggle member in both classes <i>doesn't matter</i>. As long as we point to the correct value in the sideClass constructor, that's all that matters.</li>
</ol>

We can also assign default values by using the else { } clause in the constructor.

This method can be used to pass any common values across classes. For example, I like to have some common debug messages that get appended to debug output, just to make the user aware of something if a bug occurs or a forget to switch debugging off before a release of a script.

<pre><code><b>// namespace::main::constructor
{
	@%debug = $true

	// settings

	@%s{msgDebugDef}	= "This is a definitely a bug. You should report it to the software developer."
	@%s{msgDebugForgot}	= "If you see this, it means the developer forgot to comment this line."
	@%s{msgDebugProb}	= "This is probably a bug and should be reported to the software developer."
	@%s{osDirSlash}		= "\\"
	@%s{programName}	= "Main"
	@%s{programNameDev}	= "kvsMain"
	@%s{programVersion}	= "v 0.1"

	// variable aliases - these are aliases of some variable names above to make it easier for development

	@%a{pnd} 		= @%s{programNameDev}
	@%a{msgDebugDef}	= @%s{msgDebugDef}
	@%a{msgDebugForgot}	= @%s{msgDebugForgot}
	@%a{msgDebugProb}	= @%s{msgDebugProb}
}</b>

// namespace::sideClass::constructor
{
	// inherit values from the parent if it exists, else use default values
	if (@$parent == $true) 
	{
		@%debug	= @$parent()->%debug;
		@%a	= @$parent()->%a;
	}
	else 
	{
		@%debug	= $false;
		@%a	= $false;
	}
}</code></pre>

We can now use values from @%a in <i>either</i> class' code and get the same values in our output.

<h2>Conclusion</h2>

Hopefully this makes sense for you and you can use it to improve your knowledge of KVS, OOP and program design. If you have any comments feel free to leave them as I am always trying to improve how I do things and am interested to know other methods of doing things like this.
