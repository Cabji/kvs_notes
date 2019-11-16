<h1>KVS - Beginner's Tutorial - Event Driven Programming</h1>

<h2>Introduction</h2>

This file is a note that aims to teach beginners about handling events in KVS. 

Some of the terminology in this note may be incorrect, as I do not write code professionally. I apologize for this, but it's the only way I can document what I learn and share with others.

<h2>Event Driven Programming</h2>

I'll define "event driven programming" as any programming language that lets you handle events. If you don't understand what this means, it's a way that we can write a program so that it can react to what a user does with the input methods they have (example: mouse movement, clicks and keyboard presses, or on touch devices swiping, touching, holding etc.) or things that occur in the scope of the software running on the computer. This means anything that the computer does or other programs do can be classed as events, (example: a webpage finishes loading, a file download fails, a file download finishes successfully or some extended function process completes. Literally almost anything can be thought of as an event.)

KVS is a scripting language that exists inside the KVIrc software environment, so there are already many Events that KVS can interact with. You can see a large list of these Events if you open KVIrc and go to Scripting -> Edit Events... 

How can you make your software or script interact with the events that KVIrc deals with or generates? By writing <i>Event Handlers</i>. You can create a handler in the Edit Events... listing. Choose an event and click on it. In the editor panel to the right, you will see some information about the event you selected (name, parameters). If you opposite click the event name in the list on the left, a popup menu will appear that says New Handler. Select New Handler to create a new handler for that Event.

<h2>Code Example: onChannelMessage Event Handler</h2>

Let's make an event handler for the onChannelMessage event. We'll use this event because it's very commonly occuring, so we will see a result immediately assuming of course that you are connected to some active channels on an IRC network! If you aren't just join a random channel where people are chatting (you need people to be chatting for the onChannelMessage event to occur).

Go to Scripting -> Edit Events... -> Opposite click on OnChannelMessage and click New Handler. The event list will drop down and a new handler will be in the list called "default" click on default and the code editor area to the right will be blank. Above the code editor area, a checkbox is ticked "Enabled" and the handler's name is in the text lineedit box. Change the name to something esle and then click Apply button in the bottom right. The Handler name will change in the events list.

Now we are ready to write some code. Let's just make this handler react to <b>any</b> channel message that KVIrc recieves. Don't worry, everything we make it do will only be seen by you. Enter the following into the code editor area: 

<pre><code>debug -c "Hey, this is reacting to any channel message KVIrc is getting!"</code></pre>

Click Apply. You should immediately (or soon enough) see a window in KVIrc appear called "Debug Messages". Click on that and you will see it echoing: 

<pre><code>OnChannelMessage::test: Hey, this is reacting to any channel message KVIrc is getting!</code></pre>

Notice how the debug -c command echoes our message whenever a channel message comes through on IRC. It (debug -c) also tells us where the message is coming from <i>within our code</i>. It's coming from the OnChannelMessage::test handler (I named my handler 'test').

Now let's use some of the parameters the event handler offers us. Click on OnChannelMessage in the events list. It shows us the event handler parameters. $3 is listed as the Message that comes to the channel from other users. Let's make our handler only react if the message contains some common words. Click on the handler name in the event list (mine was called 'test') and then alter the code: 
<pre><code>if ($str.contains($str.stripcolors($3), "the"))
{
	debug -c "Hey, this is reacting to channel messages with 'the' in them!";
}
</code></pre>
Now the debug window will only echo a message back to you <i>if the message contains the string 'the' in it</i>. We stripped the message of any colours too, just to keep things sane. :)

<h2>Conclusion</h2>

So that's how simple it is to write some Event Driven code using KVIrc and KVS. KVIrc is a great environment to learn about this style of programming because it has a whole base that you can interact with in the form of the IRC client. If you continue on and improve your skills, you can also write code so that it <i>generates events</i> and then handles them as well.

For now, play around with the pre-existing events in KVIrc and see what you can do with them.
