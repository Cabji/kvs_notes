<h1>KVS - Config Files - Working with Config Files</h1>

KVIrc offers a config module to make interacting with config files on disk easier. The documentation for this can be found 
here: https://www.kvirc.net/doc/module_config.html and the lrealted commands and functions are documented as well. 

<h2>Structuring Your Data To Suit Configs</h2>

Working with configs will be easier if you structure your settings data to logically matych what the config file formate expects. 

The config module has a heirachy like this: 

File -> Section -> Key -> Value

It is probably best and easiest to have your code hold setting data in the following format: 

$hash{Section}{key} => value
