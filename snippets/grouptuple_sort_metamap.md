# Group based on two keys, sort based on another one

Let's say you have a channel where every element is a tuple that consists of a 
meta map and a FASTQ file. This meta map, which is the first item of your tuple,
 contains multiple key-value pairs, and you want to group the elements in this 
channel based on two of these keys and then sort the grouped tuples based on 
another key of this meta map. Check the channel created below for a better 
understanding of the problem.

```Groovy
Channel
  .of(
    [[id:'testA', end:2, chunk:0, total_chunks:2], file('/home/user/nf/work/04/9488ff844ef80e070e8b538ae553c5/testA_2.0.fastq')],
    [[id:'testA', end:2, chunk:1, total_chunks:2], file('/home/user/nf/work/ff/4b8ebb5a54d0532ef10a61d1642196/testA_2.1.fastq')],
    [[id:'testA', end:1, chunk:0, total_chunks:2], file('/home/user/nf/work/04/9488ff844ef80e070e8b538ae553c5/testA_1.0.fastq')],
    [[id:'testA', end:1, chunk:1, total_chunks:2], file('/home/user/nf/work/ff/4b8ebb5a54d0532ef10a61d1642196/testA_1.1.fastq')],
    [[id:'testB', end:1, chunk:0, total_chunks:2], file('/home/user/nf/work/04/9488ff844ef80e070e8b538ae553c5/testB_1.0.fastq')],
    [[id:'testB', end:1, chunk:1, total_chunks:2], file('/home/user/nf/work/ff/4b8ebb5a54d0532ef10a61d1642196/testB_1.1.fastq')],
    [[id:'testB', end:2, chunk:0, total_chunks:2], file('/home/user/nf/work/04/9488ff844ef80e070e8b538ae553c5/testB_2.0.fastq')],
    [[id:'testB', end:2, chunk:1, total_chunks:2], file('/home/user/nf/work/ff/4b8ebb5a54d0532ef10a61d1642196/testB_2.1.fastq')]
  )
  .view()
  .set { my_ch }
```

If you run the snippet above, you'll see something like the output below:

```console
N E X T F L O W  ~  version 23.09.2-edge
Launching `x.nf` [backstabbing_mahavira] DSL2 - revision: cb93185077
[[id:testA, end:2, chunk:0, total_chunks:2], /home/user/nf/work/04/9488ff844ef80e070e8b538ae553c5/testA_2.0.fastq]
[[id:testA, end:2, chunk:1, total_chunks:2], /home/user/nf/work/ff/4b8ebb5a54d0532ef10a61d1642196/testA_2.1.fastq]
[[id:testA, end:1, chunk:0, total_chunks:2], /home/user/nf/work/04/9488ff844ef80e070e8b538ae553c5/testA_1.0.fastq]
[[id:testA, end:1, chunk:1, total_chunks:2], /home/user/nf/work/ff/4b8ebb5a54d0532ef10a61d1642196/testA_1.1.fastq]
[[id:testB, end:1, chunk:0, total_chunks:2], /home/user/nf/work/04/9488ff844ef80e070e8b538ae553c5/testB_1.0.fastq]
[[id:testB, end:1, chunk:1, total_chunks:2], /home/user/nf/work/ff/4b8ebb5a54d0532ef10a61d1642196/testB_1.1.fastq]
[[id:testB, end:2, chunk:0, total_chunks:2], /home/user/nf/work/04/9488ff844ef80e070e8b538ae553c5/testB_2.0.fastq]
[[id:testB, end:2, chunk:1, total_chunks:2], /home/user/nf/work/ff/4b8ebb5a54d0532ef10a61d1642196/testB_2.1.fastq]
```

Based on the example above, you want to group these elements by the `id` and 
`chunk`, and then sort by `end`. You're probably aware of the `groupTuple` 
channel operator that.. groups tuples 😬. It also has a `sort` option (Read 
more about the operator [here](https://github.com/nextflow-io/nextflow/blob/master/docs/operator.md#grouptuple)).
However, the `by` option of `groupTuple` can't access values inside a map. The 
solution is to extract the information outside the map and then pass its 
position to `groupTuple`, like below:

```Groovy
my_ch
  .map { metadata, fastq ->
    tuple(metadata.id, metadata.chunk, [metadata, fastq])
  }
  .groupTuple(
    by: [0, 1],
  )
  .view()
```

Output:
```console
N E X T F L O W  ~  version 23.09.2-edge
Launching `x.nf` [insane_mendel] DSL2 - revision: 07c0453c47
[testA, 0, [[[id:testA, end:2, chunk:0, total_chunks:2], /home/user/nf/work/04/9488ff844ef80e070e8b538ae553c5/testA_2.0.fastq], [[id:testA, end:1, chunk:0, total_chunks:2], /home/user/nf/work/04/9488ff844ef80e070e8b538ae553c5/testA_1.0.fastq]]]
[testA, 1, [[[id:testA, end:2, chunk:1, total_chunks:2], /home/user/nf/work/ff/4b8ebb5a54d0532ef10a61d1642196/testA_2.1.fastq], [[id:testA, end:1, chunk:1, total_chunks:2], /home/user/nf/work/ff/4b8ebb5a54d0532ef10a61d1642196/testA_1.1.fastq]]]
[testB, 0, [[[id:testB, end:1, chunk:0, total_chunks:2], /home/user/nf/work/04/9488ff844ef80e070e8b538ae553c5/testB_1.0.fastq], [[id:testB, end:2, chunk:0, total_chunks:2], /home/user/nf/work/04/9488ff844ef80e070e8b538ae553c5/testB_2.0.fastq]]]
[testB, 1, [[[id:testB, end:1, chunk:1, total_chunks:2], /home/user/nf/work/ff/4b8ebb5a54d0532ef10a61d1642196/testB_1.1.fastq], [[id:testB, end:2, chunk:1, total_chunks:2], /home/user/nf/work/ff/4b8ebb5a54d0532ef10a61d1642196/testB_2.1.fastq]]]
```

It's grouped as we want, but it's not sorted yet, and there's too much redundant
 information. The next step is to sort, which we will do through a closure that
 accesses the value of the `end` key.

```Groovy
my_ch
  .map { metadata, fastq ->
    tuple(metadata.id, metadata.chunk, [metadata, fastq])
  }
  .groupTuple(
    by: [0, 1],
    sort: { e1, e2 -> e1[0].end <=> e2[0].end }
  )
```

And then, a `map` to apply some cleaning to every element of the channel.

```Groovy
my_ch
  .map { metadata, fastq ->
    tuple(metadata.id, metadata.chunk, [metadata, fastq])
  }
  .groupTuple(
    by: [0, 1],
    sort: { e1, e2 -> e1[0].end <=> e2[0].end }
  )
  .map {
    tuple(it[0], [it[2][0][1], it[2][1][1]])
  }
  .view()
```

You can find the complete snippet below, followed by the output:

```Groovy
Channel
  .of(
    [[id:'testA', end:2, chunk:0, total_chunks:2], file('/home/user/nf/work/04/9488ff844ef80e070e8b538ae553c5/testA_2.0.fastq')],
    [[id:'testA', end:2, chunk:1, total_chunks:2], file('/home/user/nf/work/ff/4b8ebb5a54d0532ef10a61d1642196/testA_2.1.fastq')],
    [[id:'testA', end:1, chunk:0, total_chunks:2], file('/home/user/nf/work/04/9488ff844ef80e070e8b538ae553c5/testA_1.0.fastq')],
    [[id:'testA', end:1, chunk:1, total_chunks:2], file('/home/user/nf/work/ff/4b8ebb5a54d0532ef10a61d1642196/testA_1.1.fastq')],
    [[id:'testB', end:1, chunk:0, total_chunks:2], file('/home/user/nf/work/04/9488ff844ef80e070e8b538ae553c5/testB_1.0.fastq')],
    [[id:'testB', end:1, chunk:1, total_chunks:2], file('/home/user/nf/work/ff/4b8ebb5a54d0532ef10a61d1642196/testB_1.1.fastq')],
    [[id:'testB', end:2, chunk:0, total_chunks:2], file('/home/user/nf/work/04/9488ff844ef80e070e8b538ae553c5/testB_2.0.fastq')],
    [[id:'testB', end:2, chunk:1, total_chunks:2], file('/home/user/nf/work/ff/4b8ebb5a54d0532ef10a61d1642196/testB_2.1.fastq')]
  )
  .set { my_ch }

my_ch
  .map { metadata, fastq ->
    tuple(metadata.id, metadata.chunk, [metadata, fastq])
  }
  .groupTuple(
    by: [0, 1],
    sort: { e1, e2 -> e1[0].end <=> e2[0].end }
  )
  .map {
    tuple(it[0], [it[2][0][1], it[2][1][1]])
  }
  .view()
```

Output:
```console
N E X T F L O W  ~  version 23.09.2-edge
Launching `x.nf` [stupefied_church] DSL2 - revision: dad4f10db1
[testA, [/home/user/nf/work/04/9488ff844ef80e070e8b538ae553c5/testA_1.0.fastq, /home/user/nf/work/04/9488ff844ef80e070e8b538ae553c5/testA_2.0.fastq]]
[testA, [/home/user/nf/work/ff/4b8ebb5a54d0532ef10a61d1642196/testA_1.1.fastq, /home/user/nf/work/ff/4b8ebb5a54d0532ef10a61d1642196/testA_2.1.fastq]]
[testB, [/home/user/nf/work/04/9488ff844ef80e070e8b538ae553c5/testB_1.0.fastq, /home/user/nf/work/04/9488ff844ef80e070e8b538ae553c5/testB_2.0.fastq]]
[testB, [/home/user/nf/work/ff/4b8ebb5a54d0532ef10a61d1642196/testB_1.1.fastq, /home/user/nf/work/ff/4b8ebb5a54d0532ef10a61d1642196/testB_2.1.fastq]]
```

Special thanks to [Jordi Camps](https://nextflow.slack.com/archives/C02T98A23U7/p1696157256425859?thread_ts=1696063828.674549&cid=C02T98A23U7) for the final version of this snippet 😉
