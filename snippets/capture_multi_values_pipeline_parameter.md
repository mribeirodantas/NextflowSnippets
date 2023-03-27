# Capture multiple values provided with a pipeline parameter

As you're probably aware, you can capture the content of pipeline parameters 
(those provided with two dashes, `--`) provided through the CLI. If you run a 
pipeline with `nextflow run example.nf --name Marcel`, you can access the value 
`Marcel` within your pipeline through the variable `params.name`. You can even 
set a default value for this parameter, in case it's not used in the CLI call, 
by simply setting `params.name = 'Any other name'` in your pipeline script. If 
you don't set the parameter in the CLI, `Any other name` will be the value it 
contains. If you set it, it'll be rewritten to whatever you wrote, e.g. `Marcel`

But what if you have multiple values for the same pipeline parameter? One option
is to have a single value that can be later separated into multiple values. For 
example:

```console
nextflow run example.nf --values Marcel,Brazilian,Rock
```

Within your pipeline, you could separate this single string into three strings. 
But what if you want to have three different variables that by default you could
 use to access these three values? You can do that with `args`. For the whole 
 CLI, `args[0]` will be the first extra value, `args[1]` will be the second 
 extra value, and so on. See the example below to understand this better.

 ```Groovy
println params.example1
println params.example2
println "0: ${args[0]}"
println "1: ${args[1]}"
println "2: ${args[2]}"
 ```

 Save the snippet above as `example.nf` and run it with: 
 `nextflow run example.nf --example1 a b c --example2 d e`. The output should 
 look like:

 ```console
N E X T F L O W  ~  version 23.02.0-edge
Launching `args.nf` [amazing_knuth] DSL2 - revision: 8efa4114da
a
d
0: b
1: c
2: e
 ```
