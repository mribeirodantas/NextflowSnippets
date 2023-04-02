# Prepare assets without running pipeline

Sometimes, you want to create all conda environments, and/or pull all Docker 
images, and/or pull the remote pipeline, and everything else that will be used 
by your pipeline before actually running it. One way to do that is through stubs
 (details 
 [here](https://www.nextflow.io/docs/latest/process.html?highlight=stub#stub)). 
A stub is a block within your process scope that provides an alternate command 
to be run when the pipeline is run with the `-stub` Nextflow parameter. This 
provides us with a nice opportunity to solve the aforementioned issue, as you 
can simply leave the stub block empty and Nextflow will prepare what is required
but won't run anything. Three examples below illustrate what one can do. Be 
aware that you could have them combined in the same pipeline, such as pulling my
 remote pipeline and Docker images.

## Creating conda environemnts

```Groov
process FOO {
  debug true
  conda 'multiqc'

  script:
    """
    multiqc --help
    """

  stub:
    """
    """
}

workflow {
  FOO()
}
```

Save the snippet above as `create_conda.nf` and run it with:
`nextflow run create_conda.nf -stub -with-conda`. You can check for the conda 
environment inside your `work/` directory in the current folder.

## Pulling Docker images
Check below for an example
with Docker:

```Groovy
process FOO {
  debug true
  container 'quay.io/biocontainers/multiqc:1.0--py36_1'

  script:
    """
    multiqc --help
    """

  stub:
    """
    """
}

workflow {
  FOO()
}
```

Create a file named `nextflow.config` with `docker.enabled = true` within it.
Then save the snippet above as `pull_image.nf`and run it with:
`nextflow run pull_image.nf -stub`. In these two examples, the script block will
be ignored and only the conda environment will be created (in the first example)
or the required container image will be pulled (in the second example). If you
see the multiqc version output, it means you forgot to add `-stub` to your
Nextflow command line.

## Pull pipelines
This one is much simpler. I created a pipeline which is a fork of the famous 
[Nextflow hello world pipeline](https://github.com/nextflow-io/hello/). The 
difference [in my version](https://github.com/nextflow-io/hello/) is that we 
have a stub block added in the process scope. You can test this by simply typing
 `nextflow run mribeirodantas/hello -stub`. You won't see any hello messages 
 printed to the screen, even though the pipeline was pulled and is now local in
 your machine. If you run it without the `-stub`, you will see the hello 
 messages.