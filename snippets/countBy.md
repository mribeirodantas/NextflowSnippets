# Recreate `countBy` operator for channels consisting of single elements or tuples

In DSL1, the first version of the Domain-Specific Language of Nextflow, there 
used to be a channel operator called `countBy`. It'd basically count the number 
of occurrences of an element in a channel, among other more interesting things. 
If you want to know more about it, you can see the source code before it was 
deprecated clicking [here](https://github.com/nextflow-io/nextflow/commit/c0b164f2448629225ea047332e8c00be3e2d5162#diff-54ae590f3c4f2bc1e5014f991db7b3c3176acdd34d98d5dd4dd863321ebe77e5L545).

DSL doesn't have an equivalent channel operator, so if you want to do something 
similar, you have to use the remaining operators. Let's start with a simple 
example where I have a channel of letters and I want to create a new channel 
with these letters and the number of times they occurred. Check the source code 
below for a solution.

```Groovy
Channel
  .of('x', 'y', 'x', 'x', 'z', 'y')
  | reduce([:]) { counts, v ->
    counts[v] = (counts[v] ?: 0) + 1
    counts
  }
  | view()
```

If you run the snippet above, you should see something like the following:

```console
N E X T F L O W  ~  version 23.02.0-edge
Launching `countby_with_reduce.nf` [stupefied_poisson] DSL2 - revision: fd06b3d145
[x:3, y:2, z:1]
```

Let's now think of something slightly trickier. What if I have a channel with 
tuples? Very often the elements will be different, but the first item in these 
elements will be an identification value in this example I'm proposing. Think of
 the following:

```Groovy
Channel
  .of(['Batman', 'DC'],
      ['Batman', 'Night knight'],
      ['Wolverine', 'Marvel'],
      ['Wolverine', 'Canadian']
  )
```

See what I mean? I have multiple elements that are adjectives related to a super
 hero. If I simply count the unique values, I will get 1 occurrence of each of 
the four. That's not what I want. I want the number of times we have elements 
whose first item is Batman, or Wolverine, and so on. See the solution below:

```Groovy
Channel
  .of(['Batman', 'DC'],
      ['Batman', 'Night knight'],
      ['Wolverine', 'Marvel'],
      ['Wolverine', 'Canadian']
  )
  | reduce([:]) { counts, v ->
    counts[v[0]] = (counts[v[0]] ?: 0) + 1
    counts
  }
  | view()
```

If you run the snippet above, you should see something like the following:

```console
N E X T F L O W  ~  version 23.02.0-edge
Launching `countby_with_key_reduce.nf` [pedantic_joliot] DSL2 - revision: 5b2305a907
[Batman:2, Wolverine:2]
```