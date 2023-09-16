# Run containers of specific processes without root access

Containers with superuser privileges (root) can be [dangerous](https://docs.bitnami.com/tutorials/why-non-root-containers-are-important-for-security). Because of that, 
some container images are built with a non-root user. However, if you mount a 
volume to this container so that it can write to your disk, you'll run into 
issues as the container user will have no permission to do so. You may choose 
to globally run all containers with the same user outside the container through
 the `runOptions` settings in the `docker` scope of the Nextflow configuration 
(more info [here](https://www.nextflow.io/docs/latest/config.html#scope-docker)).

You can do this with the following line added to your `nextflow.config` file:

```Groovy
docker.runOptions = '-u $(id -u):$(id -g)'
```

However, what if you want this only for a few processes? For these situations, 
you can use the `containerOptions` process directive. See the example below:

```Groovy
process FOO {
  container 'ewels/multiqc'

  output:
  path 'multiqc_version'

  """
  multiqc --version > multiqc_version
  """
}

workflow {
  FOO()
}
```

> [!IMPORTANT]
> Make sure you have `docker.enabled = true` in your `nextflow.config`

If you save the snippet above as `multiqc.nf` and run it with 
`nextflow run multiqc.nf` you will see the following output:

```console
N E X T F L O W  ~  version 23.04.3
Launching `test.nf` [tiny_sammet] DSL2 - revision: 5eaa709932
executor >  local (1)
[31/406055] process > FOO [100%] 1 of 1, failed: 1 ✘
ERROR ~ Error executing process > 'FOO'

Caused by:
  Process `FOO` terminated with an error exit status (1)

Command executed:

  multiqc --version > file.txt

Command exit status:
  1

Command output:
  (empty)

Command error:
  .command.sh: line 2: file.txt: Permission denied

Work dir:
  /workspace/gitpod/nf-training/work/31/4060551731724e810c5be177330771

Tip: you can try to figure out what's wrong by changing to the process work dir and showing the script file named `.command.sh`

 -- Check '.nextflow.log' file for details
```

The important part is the *.command.sh: line 2: file.txt: Permission denied*. 
In the snippet below, you can see how we can run the `multiqc` container with 
the same user we're running Nextflow specifically for the `FOO` process. First,
 add the following to your `nextflow.config`:

```Groovy
process {
    withName: FOO {
        containerOptions = '-u $(id -u):$(id -g)'
    }
}
```

Now run it again and you'll see something similar to the output below:

```console
N E X T F L O W  ~  version 23.04.3
Launching `test.nf` [ecstatic_franklin] DSL2 - revision: 5eaa709932
executor >  local (1)
[c1/2fdb77] process > FOO [100%] 1 of 1 ✔
```

It worked! 😉 Click [here](https://www.nextflow.io/docs/latest/config.html#process-selectors) to know more about process selectors, so that 
you know how to pick this for many processes, use expressions, and so on.
