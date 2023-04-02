# Request the maximum number of available CPU cores for a process

You should be aware that you can request the required resources to be used in 
every process instance, that is, a task. Depending on the executor, this will be
 taken into consideration when you use the corresponding process directive. The
following process will request 2 CPU cores:

```Groovy
process FOO {
  cpus 2

  input:
    path input_file

  output:
    path 'output.csv'

  script:
    """
    my_command $input_file --cpus ${task.cpus}
    """
}
```

But what if you want to use all available CPU cores? How does one get on-the-fly
 the number of available CPU cores? You can use 
`Runtime.getRuntime().availableProcessors()`. The process would look like:

```Groovy
process FOO {
  cpus Runtime.getRuntime().availableProcessors()

  input:
    path input_file

  output:
    path 'output.csv'

  script:
    """
    my_command $input_file --cpus ${task.cpus}
    """
}
```