# Get the `publishDir` paths for a specific task

The `publishDir` process directive is very useful if you want to organize the 
output of your process in a place other than the `workDir` (check details 
[here](https://www.nextflow.io/docs/latest/process.html#publishdir)). If you 
want to have your text output files from a process `FOO` saved to a specific 
subfolder of a results folder, and the SVG output files from this very same 
process to a different subfolder, you'd use something like the snippet below at 
the beginning of your process `FOO`:

```Groovy
process FOO {
  debug true
  publishDir 'results/txts/', mode: 'copy', overwrite: false, pattern: '*.txt'
  publishDir 'results/svgs/', mode: 'copy', overwrite: false, pattern: '*.svg'
  ...
```

But what if you wanted to get this value during the task execution and you don't
 want to copy-paste these paths all over the place? The snippet below is a 
solution for this problem.

 ```Groovy
process FOO {
  debug true
  publishDir 'results/txts/', mode: 'copy', overwrite: false, pattern: '*.txt'
  publishDir 'results/svgs/', mode: 'copy', overwrite: false, pattern: '*.svg'

  input:
    val filename

  output:
    path filename

  script:
    publishDir_paths = task.target.publishDir.collect{ it.path }
    """
    touch $filename
    echo $publishDir_paths
    """
}

workflow {
  Channel
    .of('filename_a')
    | FOO
}
```

If you run the snippet above, you should see an output similar to the one below:

```console
N E X T F L O W  ~  version 23.02.0-edge
Launching `get_task.publishDir.nf` [kickass_einstein] DSL2 - revision: de39faf599
executor >  local (1)
[77/17c8b0] process > FOO (1) [100%] 1 of 1 ✔
[results/txts/, results/svgs/]
```