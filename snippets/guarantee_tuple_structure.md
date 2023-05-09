# Guarantee tuple structure in channel

It's reasonably common to have a multi-item element in your channel. You could 
for example have a channel where every element consists of a string id and a 
list of other strings, or paths, or anything like that. Let's work with strings 
to keep it simple. The code to create such a channel would be like:

```Groovy
Channel
  .of(['Marcel', ['apple', 'grapes']],
      ['Allen', ['strawberry', 'orange']],
      ['John', 'watermelon'])
  .set { my_channel }
```

Checking the contents of this channel with `my_channel.view()` would show you 
the following:

```console
N E X T F L O W  ~  version 23.03.0-edge
Launching `example1.nf` [magical_lovelace] DSL2 - revision: a95111e547
[Marcel, [apple, grapes]]
[Allen, [strawberry, orange]]
[John, watermelon]

```

As you can see, the second item in the third element of our channel, 
`watermelon` is a string, not a list with a string or a list with strings. Let's
 say that you actually need all second item of your tuples to be a list, so that
 the code in your `process` will work. In this case, before passing this channel
 to the process we would use the `map` channel operator to guarantee the desired
 structure. The snippet below will do that:

```Groovy
my_channel
  .map { name, fruits ->
    if (fruits !instanceof ArrayList) {
      [name, [fruits]]
    } else {
      [name, fruits]
      }
  }
  .set { my_channel2 }
```

You can get the instance type with `getClass()`, similarly to what is shown in 
[this](snippets/query_object_attributes.md) snippet. In this snippet, we checked
 for array lists as a condition to change the structure, but you could check if 
it's a file, a string, and so on :smiley:

With that done, you can view the content of the final channel with 
`my_channel2.view()`. The output should be similar to the one below:

```console
N E X T F L O W  ~  version 23.03.0-edge
Launching `example2.nf` [condescending_woese] DSL2 - revision: 28ec5dfd7e
[Marcel, [apple, grapes]]
[Allen, [strawberry, orange]]
[John, [watermelon]]
```