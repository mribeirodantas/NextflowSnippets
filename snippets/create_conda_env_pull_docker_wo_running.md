# Create conda environments and pull Docker images without running the pipeline

Sometimes, you want to create all conda environments and pull all Docker images 
that will be used by your pipeline before actually running it. One way to do 
that is through stubs (details 
[here](https://www.nextflow.io/docs/latest/process.html?highlight=stub#stub)). 
A stub is a block within your process scope that provides an alternate command 
to be run when the pipeline is run with the `-stub` Nextflow parameter. This 
provides us with a nice opportunity to solve the aforementioned issue, as you 
can simply leave the stub block empty and Nextflow will prepare what is required
but won't run anything. Two examples below illustrate what one can do:

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
`nextflow run create_conda.nf -stub -with-conda`. Check below for an example
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