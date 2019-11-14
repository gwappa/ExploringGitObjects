# Exploring Git

This repository is to explore what Git does behind the scene.
For the moment, it is a wrap-up of [this section in the Git book](https://git-scm.com/book/en/v2/Git-Internals-Git-Objects).

## Git objects

- all the data (blobs, trees, commits) goes to `.git/objects`, sorted by its SHA hash.
- a "blob object" is where data is stored. It is a zlib-compressed binary data.
  its original contents starts with "blob {# of bytes that follows} \\0", and then the original data
  to be stored.
- a "tree object" refers to a node structure in a git repository. It can contain a blob or a tree.
  any snapshot of a file system is represented as a tree-based structure.
- a "commit object" has a reference to the root-tree of a certain snapshot, as well as other information
  (e.g. commit message, committer info).

## Procedures

The first two procedures below are the essence of what `git add` does.

### Writing blobs

`git hash-object -w <file>` creates a blob object, write into `.git/objects` (the `-w` switch),
and returns the hash for it.

### Writing trees

For creation of a tree structure, it does the following:

 1. `git update-index --add --cacheinfo <mode> <SHA-1> <filename>` adds the blob or an existing
    tree to the staging area. Here, `--add` seems to create a new staging area.
    It seems it returns nothing.
 2. `git write-tree` writes the staging area out to a tree object, and returns the SHA-1 hash of
    the newly generated tree.

Note that, for representation of a directory structure, one would have to:

- Either refer to an existing tree, or
- Create a new tree.

### Writing commits

As stated above, a commit must refer to a specific root-tree.
`git commit-tree <hash of the tree> < <source of the commit message>` does the commit.
The command seems to read the commit message from the standard input, and adds the committer
info based on the config info.

