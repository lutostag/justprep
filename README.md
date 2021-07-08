# justprep
A pre-processor experiment for the "just" command line utility.
https://github.com/casey/just

## Experiment
The purpose of this experiment is to see how well the potential integration of "modules" might be accomplished using inclusionary keywords to reference other files containing "just" recipes, aliases and variables.  The keywords supported are:

* include
* import
* require
* with

Each keyword has the same meaning - to replace itself with the contents of a file denoted by an absolute or relative path which is provided as a parameter to the keywork.  For example ...

```
include ~/.justfile
```

... will replace itself with the content from the file `~/.justfile`

This is accomplished by auto-generation of the `justfile` dynamically from a seed file.  That seed file in this experiment is `main.just`

A keywork that is not followed by a spece will not be processed.  It will be passed on to `just` as is.

## Relative Paths

When a relative inclusionary filename/path is provided its full absolute path will be calculated based upon the location of the `main.just` file within which it is designated.  For example consider this `main.just` file in the directory `~/tmp/just_playing`

```bash
# ~/tmp/just_playing/main.just

include help.just
...
```

The path to the file `help.just` is relative to the location of the `main.kust` file in which it is designated.  So that makes its absolute path `~/tmp/just_playing/help.just`

To designate an absolute path start the filename/path with the `'/'` character.  You may also use the `'~'` character as the first character of an absolute path as a shortcut to the user's home directory.

## Process

`justprep` looks for a file named `main.just` in the current working directory.  If none is found it proceeds up the directory hierarchy until it finds a `main.just` file.  If none is found, nothing is done.

In the directory where the `main.just` file is found, `justprep` will create a `justfile` file by processing the `main.just` file replacing every inclusionary keyword line with the contents of the file designated on the line.

If a `justfile` already exists in this location, it will be over-written by the auto-generated version out of the `justprep` program.

## Use of System Environment Variables

The filename/path designated on an inclusionary line can make use of the `'~'` character as a short cut to the user's HOME directory.  System environment variables are also supported in both `$VAR` and `${VAR}` forms.

## Experiment Limitations

* filenames may not contain `just` variables
* the keyword must start in column 1
* included files may not contain inclusionary keywords - only single-level inclusions are allowed
* duplicate recipes do NOT over-ride previous definitions - an ERROR message is generated by `just` when it finds duplicates within its `justfile`

## Error Messages

When a filename does not exist, an ERROR message will be written to STDERR.

# Results

I created an alias in my bashrc file

```bash
alias jj='justprep && just'
```

which invokes the `justprep` program to generate a `justfile` from a `main.just` file before invoking the `just` utility.

After several days of using `justprep` in my own environment I've concluded that it is very useful in establishing a standard set of recipes that can be reused within multiple project environments.

The keyword I used most often is "with" primarily I believe because its short and easily typed.

## Advantages

* provides for smaller more easily maintained files
* more cohesive file content
* shared recipes between projects

# Next Steps

An inclusionary keyword could be added to the `just` project making the need for a pre-processor unnecessary.

A command-line option to allow duplicate definitions to over-ride previous definitions could be added to the `just` project to expand its capability.  For example `--allow-over-ride` or `--allow-duplicates` With this option enabled, the last definition of a thing is used as its current definttion when processing a `justfile`
