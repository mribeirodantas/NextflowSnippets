# Pass config settings as a command line argument instead of a config file

We all know it's possible to aggregate configuration from multiple places in the
scripts and config files so that Nextflow will resolve the final configuration 
for your workflow. If you're interested to know the order of priority, you can 
check it clicking 
[here](https://www.nextflow.io/docs/latest/config.html#configuration-file).

However, there are situations in which we may be interested in passing some 
settings as a command line parameter, instead of a file. If the setting is 
within the `process` scope, that's easy to do, as Nextflow has a option for 
that. Check the example below:

```Groovy
process MY_ECHO {
  script:
  """
  echo hello
  """
}

workflow {
  MY_ECHO()
}
```

Saving the snippet above as `script.nf` and running it with 
`nextflow run script.nf` will output:

```console
N E X T F L O W  ~  version 23.05.0-edge
Launching `script.nf` [maniac_stonebraker] DSL2 - revision: 8197d81ff7
executor >  local (1)
[cf/2f42ac] process > MY_ECHO [100%] 1 of 1 ✔
```

No echo, as you can see. The message echoed can be found in the work dir, more 
precisely in `cat work/cf/2f42acad81c29a4e3c49269e438581/.command.out`. If we 
want to have it printed to the standard output, the terminal, you have to set a
 process config known as `debug` (`echo`,  in the past) to true. We can type it 
inside the `MY_ECHO` process, set it in a config file or in the command line 
with:

```console
nextflow run script.nf -process.debug true

N E X T F L O W  ~  version 23.05.0-edge
Launching `script.nf` [mighty_almeida] DSL2 - revision: 8197d81ff7
executor >  local (1)
[54/6e2548] process > MY_ECHO [100%] 1 of 1 ✔
hello
```

As you can see, the `revision` didn't change, which means that there was no 
modification to the pipeline script file. However, Nextflow only supports this 
arbitrary settings passing by command line for the process scope. Of course we 
have `-with-docker` and similars, but what if I want `aws.batch.cliPath`, for
example? In this case, we can use a trick (thanks Harshil):

```console
nextflow run script.nf -c <(echo "process.cache = false")
```

You can pass whatever setting you want within the double quotes.