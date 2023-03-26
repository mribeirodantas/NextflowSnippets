# Get first N elements from a channel

You may want to consume the first N elements from a channel and don't know how
to do it. There is no operator specifically created for that, as the `first`
channel operator (details [here](https://www.nextflow.io/docs/latest/operator.html#first))
has a different purpose.

The way to solve this, as many other situations that you may run into, is to
combine operators. Here, we'll use the `buffer` channel operator (details 
[here](https://www.nextflow.io/docs/latest/operator.html#buffer)) that splits a
channel into subsets of a specific size and then we will use the `first` channel
operator to consume this first subset which contains the first N elements from
the channel.

```Groovy
Channel
  .of(1..100)
  .buffer(size: 5)
  .first()
  .view()
```

Or

```Groovy
Channel
  .of('a'..'z')
  .buffer(size: 5)
  .first()
  .view()
```