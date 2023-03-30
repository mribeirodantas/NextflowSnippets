# Piping with multiple processes at the same time

You should know that pipes are very useful in Nextflow! You can chain lots of 
things through pipes, which makes the code much more readable. See an example 
below:

```Groovy
Channel
  .of(1..10)
  | SUM_10_TO_EACH
  | FILTER_OUT_EVEN_NUMBERS
  | view()
```

However, it could be that you're in a situation where you want a channel to be 
fed to two processes and then you want to do something to the output of both 
processes. Let's think simple here: You just want to turn these two output 
channels into one, knowing that each process did a different thing to the 
original channel. There's a long version to do that. You will see it below:

```Groovy
Channel
  .of('a'..'z')
  | FIRST_PROCESS

Channel
  .of('a'..'z')
  | SECOND_PROCESS

FIRST_PROCESS.out
  .mix(SECOND_PROCESS.out)
  | THIRD_PROCESS
```

You don't have to do it like that. There's a much nicer way:

```Groovy
Channel
  of('a'..'z')
  | FIRST_PROCESS & SECOND_PROCESS
  | mix
  | view()
```

If you're a one-liner, you can make it even shorter, but I like it this way :smiley:

In this example, our `THIRD_PROCESS` has a single input and that's why we used the 
`mix` channel operator to put together the two output channels, one from `FIRST_PROCESS`
 and one from the `SECOND_PROCESS`. You can remove the `mix` if you want, but then you'd
 have to change the code of the `THIRD_PROCESS` to expect two input channels, like in 
 the snippet below:
  
```Groovy
process THIRD_PROCESS {
  debug true

  input:
  val x
  val y

  output:
  stdout

  script:
  """
  do_something_here
  """
}
  ```
