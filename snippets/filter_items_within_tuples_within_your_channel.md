# Filter items within a list in tuples within your channels

Let's say you have a channel with tuples whose second items of every tuple 
element is a list of files. The channel looks like this:

```Groovy
['Sample 1', [file('path/to/folderA/DATA_FOLDER'),
              file('path/to/folderA/SOMETHING'),
              file('path/to/folderA/FILE.txt'),
              file('path/to/folderA/SOMETHING')]],
['Sample 2', [file('path/to/folderB/DATA_FOLDER'),
              file('path/to/folderB/SOMETHING'),
              file('path/to/folderB/FILE.txt'),
              file('path/to/folderB/SOMETHING')]]
```

Here comes the tricky situation. Usually people want to filter out a few 
elements of a channel, and the `filter` channel operator (details 
[here](https://www.nextflow.io/docs/latest/operator.html#filter)) is very good 
at it. But you've run into something more challenging! You want to keep all the 
elements of your channel, but you want to filter some of the items within the 
list that is within every tuple. You want to remove all paths whose file names 
don't match either `FILE.txt` or `DATA_FOLDER`. How do you do it? You will need 
to use closures (details 
[here](https://www.nextflow.io/docs/latest/script.html#closures)). The code 
below does what you're looking for:

```Groovy
Channel
  .of(['Sample 1', [file('path/to/folderA/DATA_FOLDER'),
                  file('path/to/folderA/SOMETHING'),
                  file('path/to/folderA/FILE.txt'),
                  file('path/to/folderA/SOMETHING')
                  ]
      ],
      ['Sample 2', [file('path/to/folderB/DATA_FOLDER'),
                  file('path/to/folderB/SOMETHING'),
                  file('path/to/folderB/FILE.txt'),
                  file('path/to/folderB/SOMETHING')
                  ]
      ]
     )
  .map { sample_id, files_list ->
    [sample_id,
     files_list.findAll {
       it.toString().contains("FILE.txt") || it.toString().contains("DATA_FOLDER")
     }
    ]
  }
 .view()
 ```

 The output of the code above is:
```console
N E X T F L O W  ~  version 23.02.0-edge
Launching `filter_tuple_ch_elements_for_N_strings.nf` [lethal_newton] DSL2 - revision: 4bedd15b64
[Sample 1, [/Users/mribeirodantas/dev/sandbox/path/to/folderA/DATA_FOLDER, /Users/mribeirodantas/dev/sandbox/path/to/folderA/FILE.txt]]
[Sample 2, [/Users/mribeirodantas/dev/sandbox/path/to/folderB/DATA_FOLDER, /Users/mribeirodantas/dev/sandbox/path/to/folderB/FILE.txt]]
```