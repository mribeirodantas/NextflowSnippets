
# Filter tuples within your channel based on item values

Let's say you have a channel with tuples that are simply multiple items and you 
want to remove every tuple whose any of the items is `null`. The code below does
 that for you, but even if it wasn't a string, substring or anything else, the 
code would still be very similar to the one shown below.

```Groovy
Channel
  .of(
    [file('/a/example/path/file'), null, 'a', 1, true],
    [file('/a/example/path/file'), 100, 'a', 1, null],
    [file('/a/example/path/file'), 100, 'a', 1, false],
    [file('/a/example/path/file'), null, 'a', 1, true],
    [file('/a/example/path/file'), 100, 'a', 1, true]
  )
  .filter { !it.contains(null) }
  .view()
```

Running the snippet above should show the following output:
```console
N E X T F L O W  ~  version 23.02.0-edge
Launching `asd.nf` [agitated_lamport] DSL2 - revision: 6aca277f7c
[/a/example/path/file, 100, a, 1, false]
[/a/example/path/file, 100, a, 1, true]
```