# Flatten multiple-item tuple to 2-item tuple keeping key

It's common to have a channel with tuples that contain two items:
  - The first item is a string that represents an ID;
  - The second item is a list with multiple items that represents things related
   to the ID outside the list.

This is exactly what the `fromFilePairs` channel factory outputs, for example.
 Sometimes you need something slightly different, though. You still want 
something in a tuple together with an ID, but this something is a single item 
and you already have the data in the `fromFilePairs` format. How do you do that?

```Groovy
Channel
  .of(
      ['meta.1', [file('a'), file('b')]],
      ['meta.2', [file('a2'), file('b2'), file('c')]],
      ['meta.3', [file('a3')]],
  )
  .set { my_ch }

my_ch
  .flatMap { meta, files -> files.collect { [meta, it] } }
  .view()
```

The output should look like:

```console
N E X T F L O W  ~  version 23.02.0-edge
Launching `flatten_tuple_w_multiple_items_keeping_key.nf` [furious_curran] DSL2 - revision: 90cacf091e
[meta.1, /Users/mribeirodantas/dev/sandbox/a]
[meta.1, /Users/mribeirodantas/dev/sandbox/b]
[meta.2, /Users/mribeirodantas/dev/sandbox/a2]
[meta.2, /Users/mribeirodantas/dev/sandbox/b2]
[meta.2, /Users/mribeirodantas/dev/sandbox/c]
[meta.3, /Users/mribeirodantas/dev/sandbox/a3]
```