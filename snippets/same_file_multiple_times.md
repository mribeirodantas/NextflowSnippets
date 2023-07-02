# Multiple instances of the same file provided to the same task

It could be that you wish to have the same file provided twice to a task. If you
 try to do that, you should get the following message:

 ```console
Process `YOUR_PROCESS_NAME` input file name collision -- There are multiple 
input files for each of the following file names: example_file.txt
```

Let's say your process code looks like the one below:

```groovy
input:
  path(filename_a)
  path(filename_b)
```

When a a process instance is created, a task, the input files are staged in the 
task directory, which means a symbolic link is created pointing to the original 
location fo the file. However, if the two files that are input to a task have 
the same name, a file name collision will occur. A solution to this is to use 
more than a simple placeholder (mng1, mng2 or whatever you want to call it) for 
the input, but a filename like in the example below:

```groovy
input:
  path('example_file_a.txt')
  path('example_file_b.txt')
```

Check the full example below. First, create a file in the current directory 
called `input.txt` with some text inside. Then save the snippet below as 
`foo.nf` and run it with `nextflow run foo.nf`.

```groovy
process FOO {
  debug true

  input:
    path('my_file_a.txt')
    path('my_file_b.txt')
  output:
    stdout
  script:
    """
    cat my_file_a.txt
    cat my_file_b.txt
    """
}

workflow {
  Channel
    .fromPath('input.txt')
    .set { my_ch }
  FOO(my_ch, my_ch)
}
```

You should see as output something like the output below.

```console
Launching `foo.nf` [backstabbing_archimedes] DSL2 - revision: ec30173187
executor >  local (1)
[70/63a936] process > FOO (1) [100%] 1 of 1 ✔
oi
oi
```
