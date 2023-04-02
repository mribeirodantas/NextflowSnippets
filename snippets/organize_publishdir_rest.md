# Organize output files according to some pattern, and organize everything else in a different path

The `publishDir` process directive (details 
[here](https://www.nextflow.io/docs/latest/process.html#publishdir)) is very 
useful when you want to organize a subset of the output files of your pipeline 
in a different folder. Even though all the intermediate and final files are in 
the `work` directory, you could want to have some of them in the `results` 
folder, for example. This is fine and `publishDir` solves this just fine. If you
 want to have some files going to a path, and some other files going somewhere 
else, according to patterns, that's also fine. This is easily solved by 
`publishDir` the following way:

```Groovy
process FOO {
  publishDir path: 'results/texts', mode: 'copy', pattern: '*.txt'
  publishDir path: 'results/images', mode: 'copy', pattern: '*.svg'

  ...
}
...
```

In the snippet above, all the output files from the process `FOO` that end with 
`.txt` will go to the `results/texts` folder. All the other output files that 
end with `.svg` will go to the `results/images` folder. Great, isn't it? But 
what if I want text files to go to `results/texts` and everything else to 
`results/rest`? The `pattern` option only accepts default gobbling (not extglob)
 so you can't negate a globbing expression. However, the `saveAs` option of the
 `publishDir` directive accepts closures (details 
[here](https://www.nextflow.io/docs/latest/script.html#closures)) so you can 
pass a closure that does exactly what we want. The snippet below is a solution 
for this problem.

```Groovy
process FOO {
  publishDir path: 'results', mode: 'copy', saveAs: { filename ->
    filename.indexOf(".txt") > 0 ? "texts/$filename" : "rest/$filename" }

  input:
    path ifile

  output:
    path "new${ifile.name}"

  script:
    """
    echo "something else" > new${ifile.name}
    """
}

workflow {
  Channel
    .of(
      file('a.txt'),
      file('b.txt'),
      file('c.jpeg')
    )
    | FOO
}
```

The snippet above will save the files ending in `.txt` to `results/texts` and 
everything else will be saved in `results/rest`. Using the `tree` command, we 
can see the final tree structure:

```console
tree results

results/
├── rest
│   └── newc.jpeg
└── texts
    ├── newa.txt
    └── newb.txt

3 directories, 3 files

```