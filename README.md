# A Git Challenge

The Foo family are highly technical and organize their family life using Git. There are three members of the family: Mom, Dad, and Timmy. They share two files: `todo.txt` and `shopping.txt`. The family each add items to these files using their own branch, and the branches are merged to synchronize the lists.

Mom and Dad have each updated `shopping.txt` with some items they want. Those changes were then merged into the `shopping` branch. Mom and Dad's changes conflicted since they'd both modified the same (empty) file in different ways, and the conflict was resolved by simply including all of both Mom and Dad's items in the list.

Timmy, meanwhile, doesn't do the shopping. He updated `todo.txt` with some things he wanted to get done. But now he wants to see the shopping list to make sure his parents are buying his favorite ice cream.

## The task

We have two relevant branches - `timmy` and `shopping`. Both branches start at the same, initial commit of this repo (`7b32913`), in which both `todo.txt` and `shopping.txt` are empty. The `shopping` branch is at the tip of a sequence of *changes* to the file state, which can be seen by checking out the branch and executing `git log`:

```
707eb32 (HEAD -> shopping) Merge branch 'dad'
e34b17a (dad) Dad's shopping
d9ddef6 (mom) Mom's shopping
7b32913 First commit
```

These changes all involve only the file `shopping.txt`.

`timmy`, meanwhile, is a branch currently pointing at a different file state, in which only `todo.txt` has been modified relative to the initial commit. Your task is to **replay the sequence of changes corresponding to the `shopping` branch starting at the file state corresponding to the `timmy` branch**. In other words, when you're done the `timmy` branch should include Timmy's `todo.txt` and the final `shopping.txt` with all four items in it.

Note that simply merging `shopping` into `timmy` does not accomplish this, since rather than replaying the `shopping` changes on top of Timmy's commit, this will replay Timmy's changes on top of the tip of the `shopping` branch, resulting in a commit history like this:

```
1737812 (HEAD -> timmy) Merge branch 'shopping' into timmy
707eb32 (shopping, master) Merge branch 'dad'
2b823f4 Timmy's todo
e34b17a (dad) Dad's shopping
d9ddef6 (mom) Mom's shopping
7b32913 First commit
```

In this commit history, the oldest three commits do not have Timmy's changes to `todo.txt`, so we are not fulfilling the requirement of replaying the `shopping` changes *after* Timmy's commit.

Finally, **the git command or sequence of commands you use to accomplish this must be able to run without human supervision**, in particular without manual merge conflict resolution. A naive `git rebase` or `cherry-pick` does not fulfill this requirement.

## Why would you want to do this?

Imagine that `todo.txt` represents some file, and `shopping.txt` represents the entire rest of the codebase. The `shopping` branch with commits and merges from many contributors over a long period of time contains possibly hundreds of commits, and somewhere in there a bug was introduced. You would like to `git bisect` the branch to find this bug. The problem is, it turns out that every single commit in this entire branch is completely broken on your development machine: you can't even build the project. You can fix this by modifying `todo.txt` (maybe it's part of the build system), a file which was not touched in the entire `shopping` branch, and you commit this fix in the `timmy` branch. In order to git bisect to the bug you're actually interested in, you'll need to create an artificial branch identical to `shopping`, but in which every single commit has your fix. What you want is simple to express, yet determining how to make Git do it may put your mental model of how Git works to the test. To be honest, I still don't know how to do it.
