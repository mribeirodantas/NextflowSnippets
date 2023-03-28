# Get first N elements from a channel

You may want to consume the first N elements from a channel and don't know how
to do it. Even though there is an operator for that (`take`, check details 
[here](https://www.nextflow.io/docs/latest/operator.html#take)), I think this
is a nice opportunity to try to reproduce its behavior by combining other 
operators. It's also important to mention that the `first` channel operator 
(details [here](https://www.nextflow.io/docs/latest/operator.html#first))
has a different purpose.

Here, we'll use the `buffer` channel operator (details 
[here](https://www.nextflow.io/docs/latest/operator.html#buffer)) that splits a
channel into subsets of a specific size and then we will use the `first` channel
operator to consume this first subset which contains the first N elements from
the channel.

```Groovy
Channel
  .of(1..100)
  .buffer(size: 5)
  .first()
  .flatten()
  .view()
```

Or

```Groovy
Channel
  .of('a'..'z')
  .buffer(size: 5)
  .first()
  .flatten()
  .view()
```

Notice that the `buffer` operator will create subsets of size 5, where each 
subset is a single element. Then, the `first` operator will consume the first 
element, which is this subset with 5 items. The `flatten` operator will turn 
this single element with five items into a channel with five elements. The 
`view` operator will print the channel to the screen :smiley:
