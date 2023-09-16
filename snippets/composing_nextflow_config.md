# Composing Nextflow configurations files in `nextflow.config`

Preferrabily, people should organize their pipeline configuration in different 
files. The [nf-core](https://nf-co.re) project, that works on many of the best 
practices for Nextflow usage and pipeline development, does this in all their 
pipelines. Evidently, if you have such a tiny and simple configuration file you
 shouldn't worry about that, but in real life things can get quite complex.

You can see a real life example [here](https://github.com/nf-core/rnaseq/blob/master/nextflow.config).
 343 lines 🤯! And yet, if you search for `includeConfig` you will find *12* 
occurrences. `includeConfig` is our rock star here, instructing Nextflow to 
include extra configuration for other files in some circumstances. If you're 
not running a test, you shouldn't bother Nextflow with configurations related 
to testing, right? Exactly! That's what you can find in the snippet below:

```Groovy
profiles {
...
    test          { includeConfig 'conf/test.config'       }
    test_cache    { includeConfig 'conf/test_cache.config' }
    test_full     { includeConfig 'conf/test_full.config'  }
...

```

If you run this pipeline ([nf-core/rnaseq](https://nf-co.re/rnaseq)) with the 
test profile, like in the command below, the the configuration in 
`conf/test.config` will be taken into consideration. Otherwise, no.

```console
nextflow run nf-core/rnaseq -profile test --outdir results
```

There are many other profiles in there, among other situations with 
`includeConfig`. Take some time to read [this nextflow.config]((https://github.com/nf-core/rnaseq/blob/master/nextflow.config))
file. I guarantee it's a nice learning experience 😉
