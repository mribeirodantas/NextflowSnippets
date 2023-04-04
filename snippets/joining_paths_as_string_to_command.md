# Provide multiple paths from a channel as a string to a command

If you're still not used to the Nextflow way of doing things, you'll be very 
often tempted to "extract" elements from channels to do something. Nextflow is 
intentionally built to prevent that, to a degree that in many cases doing this 
will be literally impossible. Except when we're creating regular variables in 
the script file, or when dealing with pipeline parameters (`params.outdir`, for 
example), regular variables are only accessible in two situations:

  - Inside processes
  - Within closures (they'll still be within a channel at the end)

Having said that, let's say you have created a channel with multiple paths to be
provided as input to a process `PROGRAM_FOO` that, basically, runs a 
command-line software named `foo` that expects the names of files separated by 
a single space. Something like:

```console
foo file1.fastq file2.fastq file3.fastq
```

You used the `fromPath` channel factory to get these files into a channel but 
knowing that you gotta have a string with all of these files separated by a 
space, you're now lost on what to do. You may try to convert it to a list with 
`toList` or `collect` and be surprised when you see you can't do much with that,
as they're still channels (all channel operators return a channel). We'll solve 
this situation with two approaches. Let's say we have already created the 
channel the following way:

```Groovy
Channel
  .fromPath('test/*.fastq')
  .set { my_ch }
````

## Within a closure
We will use `collect` to convert our channel of paths to a channel of a single 
element, which is a list that consists of several items. Every path is an item.
Instead of:

```console
/my/path/to/test/file1.fastq
/my/path/to/test/file2.fastq
/my/path/to/test/file3.fastq
```

when applying the `view` operator to this channel, after `collect` we will see:

```console
[/my/path/to/test/file1.fastq, /my/path/to/test/file2.fastq, /my/path/to/test/file3.fastq]
```

Then, we will use the `map` operator to apply a function to each element of our 
channel, but because the channel consists of a single element, which is a list, 
we will apply a function to the entire list. The function here is the `join` 
function that joins the elements of a channel as strings and separates them by a
provided character.

```Groovy
my_ch
  .collect()
  .map { it -> it.join(' ') }
  .set { my_new_ch }

// With that, we can pass it to our process
my_new_ch
  | PROGRAM_FOO
```

In fewer lines, we could have:

```Groovy
my_ch
  .collect()
  .map { it -> it.join(' ') }
  | PROGRAM_FOO
```

## Inside a process

If we want to handle this within a process, we still need the `collect` channel 
operator, so that the process receives all the elements of the channel (all the 
paths) at once. Then, within the process, we will use Groovy to join the items 
in this list. See the snippet below:

```Groovy
process PROGRAM_FOO {
  debug true

  input:
    val list_of_paths

  output:
    stdout

  script:
    string_of_paths = list_of_paths.join(' ')
    """
    echo "foo ${string_of_paths}"
    """
}

workflow {
  Channel
    .fromPath('test/*.nf')
    .collect()
    | PROGRAM_FOO
}
```

The purpose of the `debug` process directive is so that we can see at our screen
the output of the `echo` command in the `script` block.

If you ever think again about bringing channel elements out of it, remember this
 thumb rule: Either handle it in closures with channel operators, and be aware 
 that at the end it'll be in a channel again, or do it within a process :smiley: