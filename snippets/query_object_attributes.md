# Query attributes of objects

Imagine you've run into this object that you're not fully aware of what it 
really is, or maybe you're even sure about it, but don't know what attributes 
you can use to get more info about each object. One option is to randomly try 
different names and see which of them returns some interesting information.

Don't fret, as it can be much easier than that. The snippet below will print all
 attributes with their values for each item in the channel.

 ```Groovy
nums_ch = Channel.of(file("/Users/mribeirodantas/dag.dot"), "some random string")
nums_ch.view { it.properties.each{ println it } }
 ```

 The output should look like:

 ```console
N E X T F L O W  ~  version 23.02.0-edge
Launching `object_methods.nf` [sleepy_hodgkin] DSL2 - revision: 3162605d01
absolute=true
class=class sun.nio.fs.UnixPath
fileSystem=sun.nio.fs.MacOSXFileSystem@58be0fd4
nameCount=3
fileName=dag.dot
root=/
parent=/Users/mribeirodantas
[absolute:true, class:class sun.nio.fs.UnixPath, fileSystem:sun.nio.fs.MacOSXFileSystem@58be0fd4, nameCount:3, fileName:dag.dot, root:/, parent:/Users/mribeirodantas]
blank=false
empty=false
class=class java.lang.String
bytes=[B@4f4a5ab1
[blank:false, empty:false, class:class java.lang.String, bytes:[115, 111, 109, 101, 32, 114, 97, 110, 100, 111, 109, 32, 115, 116, 114, 105, 110, 103]]
 ```

 The second item is a string, so not a lot of stuff, but as you can see, there 
is a lot of information you can retrieve about the first object, which is a file
.