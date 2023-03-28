# Get last N elements from a channel

Imagine you want to consume the last N elements from a channel and don't know 
how to do it. Differently from the first N elements that you can use the 
`take` operator (check details 
[here](https://www.nextflow.io/docs/latest/operator.html#take)), there is no 
operator that does this by itself.

Here, we'll use the `buffer` channel operator (details 
[here](https://www.nextflow.io/docs/latest/operator.html#buffer)) that splits a
channel into subsets of a specific size and then we will use the `last` channel
operator to consume this last subset that contains the last N elements from
the channel.

```Groovy
Channel
  .of(1..100)
  .buffer(size: 5)
  .last()
  .flatten()
  .view()
```

Or

```Groovy
Channel
  .of('a'..'z')
  .buffer(size: 5)
  .last()
  .flatten()
  .view()
```

Notice that the `buffer` operator will create subsets of size 5, where each 
subset is a single element. Then, the `last` operator will consume the last 
element, which is this subset with 5 items. The `flatten` operator will turn 
this single element with five items into a channel with five elements. The 
`view` operator will print the channel to the screen :party:
