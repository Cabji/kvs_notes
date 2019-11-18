<h1>KVS Beginners</h1>
<h2>Chapter 2: Script Tester</h2>
<h3>Introduction</h3>
KVIrc offers a powerful scripting engine which allows you to interact with not only the IRC client and its data but also lets you build your own user interface. In effect, you can build fully functioning GUI software using KVS.

To get a start on KVS, you are best to open up the Script Tester in KVIrc and also the KVIrc Documentation. Press Ctrl+Shirt+S in KVIrc to open the Script Tester window, and press F1 to open the KVIrc documentation. Additionally, you can view a customized version of th KVIrc documentation in roboirc's github: https://github.com/roboirc/kvirc_docs/tree/master/kvirc%20docs
<h3>Script Tester</h3>
The script tester is a quick and easy way to create and then test KVS code. Enter the code on the left hand side of the window, and any code output will be displayed on the right hand side when the Execute button is clicked.
<h4>Code Example: Hello World</h4>
No beginner's tutorial is complete without a Hello World.
<pre><code>clear
echo "Hello World"</code></pre>
Press the Execute button and in the output pane you will see: 
<pre>Hello World</pre>
Now let's use a for loop to do some repetitive echoing. 
Note: code text in <b>bold</b> signifies you should alter your previous code to make it look like this now.
<pre><code>clear
<b>for (%i = 0; %i < 3; %i++)
{
	</b>echo<b> $(%i + 1):</b> "Hello World"
<b>}</b></code></pre>
This code will give you the following output: 
<pre>1: Hello World
2: Hello World
3: Hello World</pre>
There's a few things about this small piece of code that I will point out so you understand what we do here, and some things that KVS has that are different to other programming languages.
<ol>
	<li><b>clear</b> - When using script tester, it's nice to clear the output each time the code runs.</li>
	<li><b>for (%i = 0 ...)</b> - KVS expects variables to start with percent symbol %. %i is local to the for loop as you would expect if you are familiar with C or C++. I also set %i to 0 for the for loop, not to 1 even though I want to output the number 1 on the first loop iteration.<br/>Why? For two reasons: the first is because <i>usually</i> when you use a for loop you will want the first number to be 0 because you will likely be dealing with an array and the first index of an array is 0.<br/>The second reason I did this in this example was to show you the $() function, officially called the <i>Expression evaluation identifier</i>. This allows you to evaluate an expression and return its result. To perform arithmetic on variables you must use the $() to surround the expression otherwise KVS will get confused about what you expect to happen.</li>
	<li><b>for (... %i < 3 ...)</b> - Personally I prefer to use "less than" and the number of times I want the for loop to iterate.<br/>Why? Because it's easy to see how many times the loop will occur (3 times, the number 3 is written there) and because we initialize %i to 0, we use "less than" 3 so it will iterate through 0, 1 and 2. Hence, why I used $() to get the _output_ I desired.<br/>This "less than" usage convention also works perfectly for arrays. In KVS you can obtain the number of elements in an array either using the $length() function or using the shorthand: %array[]# so the for loop definition to loop through an entire array becomes: for (%i = 0; %i < %array[]#; %i)</li>
	<li><b>echo $(%i + 1): "Hello World"</b> - Strings in KVS are somewhat odd compared to most languages. KVS assumes mostly that everything is a string. So the colon : in this line is actually displayed as a string in the output. In fact, you don't even need the double quotes around "Hello World". You can remove the double quotes and get the same output. Remember this and decide what you like to do when it comes to strings. The longer I use KVS the more I have tended toward including double quotes just for clarity and convention.</li></ol>
<h3>Conditionals</h3>
We can use conditionals to decide how the program logic should flow.
<pre><code>clear
<b>%advance = $false
if (%advance)
{</b>
	for (%i = 0; %i < 3; %i++)
	{
		echo $(%i + 1): "Hello World"
	}
<b>}
else
{
	echo We didn't get to advance.
}</b></code>
</pre>
The output for this will be: 
<pre>We didn't get to advance.</pre>
If you alter the value of %adavnce to $true, the output becomes: 
<pre>1: Hello World
2: Hello World
3: Hello World</pre>
The $true and $false functions returns the boolean true and false values. If you use "true" and "false" without double quotes like in C++, it will always evaluate to $true because KVS sees "false" as a string, which means it is a valid value in the boolean sense and therefore equates to boolean $true.
<h3>Conclusion</h3>
This was a simple introduction to the KVS Script Tester.

Read through the first 5 sections of the section titled <i>Scripting Concepts: The KVS Manual</i> in the KVIrc documentation. It will go over a lot of the elementary information you need to know to write a program or script in KVS.
