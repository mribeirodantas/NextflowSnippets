# Run some processes outside containers

As you're probably aware, you can tell Nextflow to run processes instances 
in containers by simply setting the `container` process directive and 
enabling your container technology of choice. For example, in your 
`nextflow.config`, add the following:

```Groovy
docker.enabled = true
```

Then create a pipeline script with the following:

```Groovy
process FOO {
  container 'nextflow/rnaseq-nf'

  output:
  path 'salmon_version'

  """
  salmon --version > salmon_version
  """
}

workflow {
  FOO()
}
```

In the snippet above, we're basically saving the version of the `salmon` 
software to a file named `salmon_version`. We also told Nextflow to run this 
task inside a container created with the image `rnaseq-nf` in the `nextflow` 
namespace. Because we didn't say where, Nextflow will look in [Docker Hub](https://hub.docker.com)
 by default, but you could also specify other container registries such as 
[Quay.io](https://quay.io).

So, in an hypothetical scenario, you want to have your tasks run in containers 
with Docker but there is one specific process whose tasks are very lightweight 
and free of dependencies. How do you tell Nextflow to run these tasks locally? 
You set the `container` process directive to an empty string or `false`. See 
the example below:

```Groovy
process FOO {
  container ''

  output:
  path 'salmon_version'

  """
  salmon --version > salmon_version
  """
}

workflow {
  FOO()
}
```

As you can see, it's basically a matter of setting the `container` process 
directive to an empty string (or `false`).
