# Add ending condition to `watchPath`

One very useful channel factory in Nextflow is `watchPath`. The `Channel.watchPath` method watches a folder for one or more files matching a specified pattern. As soon as there is a file that meets the specified condition, it is emitted over the channel that is returned by the watchPath method. See the snippet below.

```console
Channel
  .watchPath( '/path/*.fa' )
  .subscribe { fa -> println "Fasta file: $fa" }
```

As soon as a file ending in `.fa` is created within `/path/`, it will be printed "Fasta file:" followed by the filename. You can specify different events to watch for, such as a file being modified or deleted. You can read more about this channel factory in the Nextflow official documentation [here](https://www.nextflow.io/docs/latest/reference/channel.html#watchpath).

One common ask though is: How to make it stop watching? There are two easy solutions that come to mind. You could limit by a number of file events (channel elements created) or use a dummy filename to tell Nextflow to stop watching for this path. You can find both solutions below.

## Stop watching when an event related to a file named `DONE` takes place
In the code below, taken from [here](https://github.com/SystemsGenetics/GEMmaker/blob/9e69f21f79a38a2e4c6f0ec182ee4647be345754/workflows/GEMmaker.nf#L413-L420), when a file named `DONE` is created, it will stop watching the path.

```console
Channel
  .watchPath("${workflow.workDir}/GEMmaker/process")
  .until { it.name == "DONE" }
  .splitCsv(sep: '\t')
  .branch { sample ->
    local: sample[2] == "local"
    remote: sample[2] == "remote"
  }
  .set { NEXT_SAMPLE }
```

## Stop watching after a specific number of channel elements
In the snippet below, taken from [here](https://github.com/nextflow-io/nextflow/issues/2248#issuecomment-1497981930), when three elements are created in the channel (e.g. three files are created in the path), Nextflow will stop watching.

```console
workflow {
  ch = Channel.watchPath("inputs/*").take(3)
  ch.subscribe(
    onNext: { print "emitted: ${it.name}" },
    onComplete: { print "we're done" }
  )

  ch.collect().view { "emitted: ${it.size()} files in total" }
}
```
