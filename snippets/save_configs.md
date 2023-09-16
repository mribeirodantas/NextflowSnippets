# Save configuration files used in a specific run

Individuals using Nextflow are usually very concerned with reproducibility, and 
at the same time, they like their output files well organized. The 
[publishDir](https://www.nextflow.io/docs/latest/process.html#publishdir) 
process directive is very useful for that, as you can choose what files you 
want to store, how, and where. However, if you're really worried about 
reproducibility, it'd be interesting to know what configurations were used in 
the specific run of the pipeline that generated these output files. The snippet
 below which is a solution to this problem will make use of an internal 
workflow variable of Nextflow: `workflow.configFiles`, that consists of a list 
of all the configuration files used.


```Groovy
params.outdir = 'results'

process FOO {
  publishDir "${params.outdir}/FOO/", mode: 'copy'

  output:
  path 'my_output_file'

  """
  echo FOO > my_output_file
  """
}

workflow {
  FOO()
  Channel
    .fromList(workflow.configFiles)
    .collectFile(storeDir: "${params.outdir}/configs")
}
```

Save the snippet above as `save_conf_files.nf` and run the command line below to
 run the pipeline:

```console
nextflow run save_conf_files.nf
```

If you have a `nextflow.config` file in your current directory, the `tree` 
command below will show you the following output:

```console
tree results
results
├── FOO
│   └── my_output_file
└── configs
    └── nextflow.config

3 directories, 2 files
```

Let's say you have another file called `another_conf.config` and you provided 
it with the `-c` option of nextflow, as in the command line below:

```console
nextflow run save_conf_files.nf -c another_conf.config
```

The tree command should then show the following:

```console
tree results
results
├── FOO
│   └── my_output_file
└── configs
    ├── another_conf.config
    └── nextflow.config

3 directories, 3 files
```

The trick here is to use the `workflow.configFiles` variable and the 
[collectFile](https://www.nextflow.io/docs/latest/operator.html#collectfile) 
channel operator to store them in the same path you provided to the publishDir 
directive.
