[h1]KVS - Classes - What Is An Internal Function?[/h1]

The class command documentation (https://www.kvirc.net/doc/cmd_class.html) explains what an Internal Function is in KVS: 

***An internal function cannot be called by anyone else but the object instance itself. Note that this is different from the C++ 
"protected" or "private" keywords that refer to the object's class instead of the object instance.***

So this allows us to make a function that can only be called from within the class itself which can be handy for some situations.
