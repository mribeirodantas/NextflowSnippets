# Set operations with channels

Let's say we have two channels `ch1` and `ch2` like the following:

```Groovy
Channel
  .of(1,2,3,4)
  .set { ch1 }
Channel
  of.(3,4,5,6)
  .set { ch1 }
```

There are a few things we can immediately think about these two channels if we 
think in terms of set theory. The intersection of `ch1` and `ch2` is `[3,4]`, 
and the symmetric difference, or the disjunctive union (the set of elements 
that are in either of the sets, but not in their intersection) is `[1,2,5,6]`. 
Also, the difference between `ch1` and `ch2` is `[1,2]`.

With this in mind, you may be asking yourself how to achieve these results with 
Nextflow. There are no channel operators for these operations, but as you've 
probably seen in the other snippets, we can combine Nextflow operators to 
achieve this (and so much more :star_struck:).

Let's start with the symmetric difference. Assuming the channels do not contain 
exactly repeated elements, the idea is basically to:
  - Create a new channel containing all the elements from `ch1` and `ch2`
  - Count how many times each element appears in this new channel
  - Remove the elements that appeared more than once

We can create a new channel with all elements from `ch1` and `ch2` with the 
`mix` channel operator. The `countBy` channel operator has been deprecated, but 
as you can see 
[here](https://github.com/mribeirodantas/NextflowSnippets/blob/main/snippets/countBy.md)
 we can recreate its functionality through the `reduce` channel operator. It 
creates a channel with a dictionary-like format, e.g. `3:2`, where `3` occurred 
`2` times. With that done, we can check for each element of the channel how many
 times it occurred, and keep only those that appeared once. Then, keep only the 
key of the dictionary, which is the actual value (`3`, in the example above). 
Check the solution below:

```Groovy
Channel
  .of(1,2,3,4)
  .set { ch1 }
Channel
  .of(3,4,5,6)
  .set { ch2 }

ch1.
  mix(ch2)
  | reduce([:]) { counts, v ->
    counts[v] = (counts[v] ?: 0) + 1
    counts
  } // Remove intersection between two channels
  | map { it.findAll { key, value -> value == 1 } }
  | map { it -> it.keySet() }
  | view()
```

The output of the snippet above should look like:

```console
N E X T F L O W  ~  version 23.02.0-edge
Launching `asd.nf` [fervent_monod] DSL2 - revision: dc5d1b236a
[1, 2, 5, 6]
```

The figure below illustrates the concept of symmetric difference in set theory.
![Symmetric difference](https://upload.wikimedia.org/wikipedia/commons/4/46/Venn0110.svg)

As you've probably realized now, it's trivial to convert the code above to keep 
the intersection instead. Instead of filtering for those that appear only once, 
filter those that appear more than once.

```Groovy
Channel
  .of(1,2,3,4)
  .set { ch1 }
Channel
  .of(3,4,5,6)
  .set { ch2 }

ch1.
  mix(ch2)
  | reduce([:]) { counts, v ->
    counts[v] = (counts[v] ?: 0) + 1
    counts
  } // Keep only the intersection betewen the two channels
  | map { it.findAll { key, value -> value > 1 } }
  | map { it -> it.keySet() }
  | view()
```

Output:

```console
N E X T F L O W  ~  version 23.02.0-edge
Launching `asd.nf` [wise_legentil] DSL2 - revision: 52d71d6c1d
[3, 4]
```
The figure below illustrates the concept of intersection in set theory.
![Intersection](https://upload.wikimedia.org/wikipedia/commons/9/99/Venn0001.svg)

But what if you want the difference? You don't care about the elements that 
exist only in `ch2`. In this case, a trick is to mix `ch1` with `ch2`, and then 
with `ch2` again. This way, the elements in `ch2` will appear at least `2` times
 and the filter for elements that occurred only once will remove these. See 
below for the solution:

```Groovy
Channel
  .of(1,2,3,4)
  .set { ch1 }
Channel
  .of(3,4,5,6)
  .set { ch2 }

ch1.
  mix(ch2,ch2)
  | reduce([:]) { counts, v ->
    counts[v] = (counts[v] ?: 0) + 1
    counts
  }
  | map { it.findAll { key, value -> value == 1 } }
  | map { it -> it.keySet() }
  | view()
```

The output should look like below:

```console
N E X T F L O W  ~  version 23.02.0-edge
Launching `asd.nf` [kickass_archimedes] DSL2 - revision: b924344052
[1, 2]
```

The figure below illustrates the concept of difference in set theory.
![Set difference](https://upload.wikimedia.org/wikipedia/commons/e/e6/Venn0100.svg)

This is not the only way that you can do that. Check below for two different 
solutions that arrive at the same result. These solutions were written by 
[Julian Libiseller-Egger](https://nextflow.slack.com/archives/C02T98A23U7/p1678891785508629?thread_ts=1678886372.934369&cid=C02T98A23U7) and solve a slightly more complex 
situation when you have tuples and want to subtract some elements based on 
elements from another channel.

```Groovy
Channel
  .of(
    [[id:'SRR14022735'], "test/SRR14022735_T1.scaffolds.fa"],
    [[id:'SRR14022764'], "test/SRR14022764_T1.scaffolds.fa"],
    [[id:'sample_to_keep'], "test/sample_to_keep.scaffolds.fa"]
  )
  .set { sample_channel }

Channel
  .of(
    'SRR14022735',
    'SRR14022764'
  )
  .set { genomes_to_remove }

sample_channel
  | map {[it[0]["id"], *it]}
  | join(genomes_to_remove | map {[it, "remove"]}, remainder: true)
  | filter { !it[-1] }
  | map { it[1, 2] }
  | view
```

You should see an output similar to the one below:

```console
N E X T F L O W  ~  version 23.02.0-edge
Launching `asd2.nf` [adoring_visvesvaraya] DSL2 - revision: 742eac4ba8
[[id:sample_to_keep], test/sample_to_keep.scaffolds.fa]
```

A different solution that arrives at the same outcome:

```Groovy
Channel
  .of(
    [[id:'SRR14022735'], "test/SRR14022735_T1.scaffolds.fa"],
    [[id:'SRR14022764'], "test/SRR14022764_T1.scaffolds.fa"],
    [[id:'sample_to_keep'], "test/sample_to_keep.scaffolds.fa"]
  ).set { sample_channel }

Channel
  .of(
    'SRR14022735',
    'SRR14022764'
  )
  .set { genomes_to_remove }

sample_channel
  | combine (genomes_to_remove | collect | map { [it] })  // use map call to wrap in extra list
  | filter { meta, path, to_remove -> !(meta["id"] in to_remove) }
  | map { it[0, 1] }
  | view
```

Output:

```console
N E X T F L O W  ~  version 23.02.0-edge
Launching `asd3.nf` [ecstatic_lorenz] DSL2 - revision: 9402e0a96c
[[id:sample_to_keep], test/sample_to_keep.scaffolds.fa]
```
