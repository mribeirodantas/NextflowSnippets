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

There are two other interesting things you can also do that are similar to this.
 You can compare a number to the number of your available CPU cores and choose 
the smaller. Check the snippet below for one example:

```Groovy
process FOO {
  cpus Math.min(6, Runtime.getRuntime().availableProcessors())

  ...
```

If you have 8 CPU cores, this process will request 6, as 6 is smaller than 8. If
 you have only 4, it will pick 4, as 4 is smaller than 6. You could also create 
a function called `check_max` (or any other valid name that you want) and apply 
it to your resources requests in the process scope (be it in the script file, or
 in configuration files) so that regardless of the final value that will be 
requested (check details about dynamic requests 
[here](https://www.nextflow.io/docs/latest/process.html?highlight=attempt#dynamic-computing-resources)),
 it will always be capped by the maximum available resources.