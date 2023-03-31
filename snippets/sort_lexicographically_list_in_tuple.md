# Sort lexicographically paths in a list within a tuple

You have a process whose output is a channel with tuples, where each tuple 
consists of a sample id and a list of paths. If you `view` the channel, it will 
look something like:

```console
[ERR062323,[/Data/data/rohit/work/f1/54b450d3c8e9223a96ee9c628ba825/delly.vcf, /Data/data/rohit/work/7c/da41005ea068eb213f0d7bd9344b30/manta.vcf, /Data/data/rohit/work/63/fd4cde7d6749cb4940f0dfd2c2ad47/lumpy.vcf, /Data/data/rohit/work/8e/d6755f61d8af33886cb0eb45398a4f/tardis.vcf, /Data/data/rohit/work/00/2e27fea8ea5adc4cbbddf06b64e099/whamg.vcf, /Data/data/rohit/work/5d/f4766c43bb1ec2033327c97820109e/cnvnator.vcf, /Data/data/rohit/work/9e/57a61488fe218bcca5fd716c2e3274/breakdancer.vcf]]
[NA12878, [/Data/data/rohit/work/89/0c8b2ac73a1ba55557e24db343accb/delly.vcf, /Data/data/rohit/work/f6/7c628afeff1173dbccc0690bb70b3d/tardis.vcf, /Data/data/rohit/work/ec/adb46ec7539a31088f9b934f383402/cnvnator.vcf, /Data/data/rohit/work/1f/2f4253c4f8bdd5dcbf9c725c97ea1b/lumpy.vcf, /Data/data/rohit/work/46/8450444b36828466a5f45c1c7243cc/whamg.vcf, /Data/data/rohit/work/d4/1be19634078022be4f48d5124aa772/breakdancer.vcf, /Data/data/rohit/work/7c/affe6a14fd43243527ebf4973f8eb4/manta.vcf]]
```

You will find below an indented version of the output above, so that it's easier
 for you to see what's in this channel:

```console
[ERR062323,
  [/Data/data/rohit/work/f1/54b450d3c8e9223a96ee9c628ba825/delly.vcf,
   /Data/data/rohit/work/7c/da41005ea068eb213f0d7bd9344b30/manta.vcf,
   /Data/data/rohit/work/63/fd4cde7d6749cb4940f0dfd2c2ad47/lumpy.vcf,
   /Data/data/rohit/work/8e/d6755f61d8af33886cb0eb45398a4f/tardis.vcf,
   /Data/data/rohit/work/00/2e27fea8ea5adc4cbbddf06b64e099/whamg.vcf,
   /Data/data/rohit/work/5d/f4766c43bb1ec2033327c97820109e/cnvnator.vcf,
   /Data/data/rohit/work/9e/57a61488fe218bcca5fd716c2e3274/breakdancer.vcf
  ]
],
[NA12878,
  [/Data/data/rohit/work/89/0c8b2ac73a1ba55557e24db343accb/delly.vcf,
   /Data/data/rohit/work/f6/7c628afeff1173dbccc0690bb70b3d/tardis.vcf,
   /Data/data/rohit/work/ec/adb46ec7539a31088f9b934f383402/cnvnator.vcf,
   /Data/data/rohit/work/1f/2f4253c4f8bdd5dcbf9c725c97ea1b/lumpy.vcf,
   /Data/data/rohit/work/46/8450444b36828466a5f45c1c7243cc/whamg.vcf,
   /Data/data/rohit/work/d4/1be19634078022be4f48d5124aa772/breakdancer.vcf,
   /Data/data/rohit/work/7c/affe6a14fd43243527ebf4973f8eb4/manta.vcf
  ]
]
```

As you can see, if we look at the very end of each path, the filename, you will 
notice that we have `breakdancer.vcf`, starting with a b, much later than 
`delly.vcf`, starting with a d. The thing is that, theoretically, the next 
process that will receive this channel as input has a limitation: It expects the
 paths within that list to be sorted lexicographically. How do you do that? You
will find the solution below:

```Groovy
// I'm manually creating a channel to resemble the situation that you're in,
// where in reality you probably built this channel using a channel factory.
Channel
  .of(
    ['ERR062323',
      [file('/Data/data/rohit/work/f1/54b450d3c8e9223a96ee9c628ba825/delly.vcf'),
       file('/Data/data/rohit/work/7c/da41005ea068eb213f0d7bd9344b30/manta.vcf'),
       file('/Data/data/rohit/work/63/fd4cde7d6749cb4940f0dfd2c2ad47/lumpy.vcf'),
       file('/Data/data/rohit/work/8e/d6755f61d8af33886cb0eb45398a4f/tardis.vcf'),
       file('/Data/data/rohit/work/00/2e27fea8ea5adc4cbbddf06b64e099/whamg.vcf'),
       file('/Data/data/rohit/work/5d/f4766c43bb1ec2033327c97820109e/cnvnator.vcf'),
       file('/Data/data/rohit/work/9e/57a61488fe218bcca5fd716c2e3274/breakdancer.vcf')]
      ],
    ['NA12878',
      [file('/Data/data/rohit/work/89/0c8b2ac73a1ba55557e24db343accb/delly.vcf'),
       file('/Data/data/rohit/work/f6/7c628afeff1173dbccc0690bb70b3d/tardis.vcf'),
       file('/Data/data/rohit/work/ec/adb46ec7539a31088f9b934f383402/cnvnator.vcf'),
       file('/Data/data/rohit/work/1f/2f4253c4f8bdd5dcbf9c725c97ea1b/lumpy.vcf'),
       file('/Data/data/rohit/work/46/8450444b36828466a5f45c1c7243cc/whamg.vcf'),
       file('/Data/data/rohit/work/d4/1be19634078022be4f48d5124aa772/breakdancer.vcf'),
       file('/Data/data/rohit/work/7c/affe6a14fd43243527ebf4973f8eb4/manta.vcf')
      ]
    ]
  )
  .map { sample_id, files ->
    [sample_id, files.sort { a, b ->
        def filenameA = file(a).name
        def filenameB = file(b).name
        return filenameA.compareTo(filenameB)
      }
    ]
  }
  .view()
```

The output:

```console
N E X T F L O W  ~  version 23.02.0-edge
Launching `asd.nf` [small_einstein] DSL2 - revision: 3474c5d347
[ERR062323, [/Data/data/rohit/work/9e/57a61488fe218bcca5fd716c2e3274/breakdancer.vcf, /Data/data/rohit/work/5d/f4766c43bb1ec2033327c97820109e/cnvnator.vcf, /Data/data/rohit/work/f1/54b450d3c8e9223a96ee9c628ba825/delly.vcf, /Data/data/rohit/work/63/fd4cde7d6749cb4940f0dfd2c2ad47/lumpy.vcf, /Data/data/rohit/work/7c/da41005ea068eb213f0d7bd9344b30/manta.vcf, /Data/data/rohit/work/8e/d6755f61d8af33886cb0eb45398a4f/tardis.vcf, /Data/data/rohit/work/00/2e27fea8ea5adc4cbbddf06b64e099/whamg.vcf]]
[NA12878, [/Data/data/rohit/work/d4/1be19634078022be4f48d5124aa772/breakdancer.vcf, /Data/data/rohit/work/ec/adb46ec7539a31088f9b934f383402/cnvnator.vcf, /Data/data/rohit/work/89/0c8b2ac73a1ba55557e24db343accb/delly.vcf, /Data/data/rohit/work/1f/2f4253c4f8bdd5dcbf9c725c97ea1b/lumpy.vcf, /Data/data/rohit/work/7c/affe6a14fd43243527ebf4973f8eb4/manta.vcf, /Data/data/rohit/work/f6/7c628afeff1173dbccc0690bb70b3d/tardis.vcf, /Data/data/rohit/work/46/8450444b36828466a5f45c1c7243cc/whamg.vcf]]
```