Most of these programs have intelligible usage messages, or comments
in the source code that explain the usage, or both.  My favorites are
`git-vee` and `git-re-edit`.  Here is a brief summary of what they do.

`git-addq` is a simple wrapper around `git-dirtyfiles` and
[`menupick`](https://github.com/mjdominus/util/blob/master/bin/menupick)
that gives a menu of dirty files and asks which ones you want added to
the index.  Options are passed to `git-dirtyfiles`, so `-q` includes
unknown files in the menu.  `menupick` is available in my [util
repository](https://github.com/mjdominus/util).

`git-command` is a shell alias. If you make a symbolic link, say
`commit`, that points to `git-command`, then you now have a `commit`
command that actually runs `git-commit`.

`git-dirtyfiles` lists the files in the repository that have been
edited since the last commit, ignoring unknown files by default, but
including them if you pass `-q`.  With a commit argument, lists the
files that were changed in that commit.  This exists mainly to be a
back-end for `git-re-edit`; see below.

`git-fetchs` fetches all branches from all the remotes that you have
previously fetched from.  This is primarily useful if you have a bunch
of remotes that you don't normally fetch.  At a previous employer, we
had a repo setup script that would add one remote for each coworker.
But in most repos I would only want to fetch from the coworkers with
whom I was actually collaborating.

`git-ff-branch` fast-forwards a branch that might not be checked out.
For example, suppose you do `git fetch remote`, find that
`remote/master` has updated, and you would like to move `master` to
point to `remote/master`. You can use `git ff-branch master` (if
`master` is set up to track `remote/master`, or `git ff-branch master
remote/master` (if not).  Or you can just use `git ff-branch -r
remote` to fast-forward all refs that track branches on `remote`.  The
command will not modify any head except by fast-forwarding.

`git-forcepush` is for pushing non-fast-forward updates to branches in
repositories that normally forbid that.  It does it by deleting the
old branch first.

`git-get` retrieves miscellaneous information about the repository.
For example, how does a program retrieve the name of the
currently-checked-out branch?  One common answer is the ridiculous
`git branch | grep '^\*' | cut -c3-`.  Another is the equally
ridiculous `git rev-parse --symbolic-full-name --abbrev-ref
HEAD`. With `git-get` you don't have to remember that nonsense: you
use `git get current-branch-name`.  To check if the repository is
dirty, you use `git get is-working-tree-dirty` and it exits
successfully if so, and unsuccessfully if not. It is easy to drop in
new subcommands; every time I find out there is no easy way to
determine some bit of git information, I drop it in here. `git get`
with no argument prints a list of available information.

`git-git` is the reverse of `git-command`.  If you accidentally type
`git git commit`, then `git-git` will silently run `git commit` for
you.

`git-lst` lists the files in the specified directory, from
most-recently to least-recently committed, with the last commit times.
It is very much a work in progress; it should have many options, but
doesn't. Unlike
`[git-ls-date](https://pypi.python.org/pypi/git-ls-date)` it is fast
and doesn't fail completely in large repos. The name `lst` is supposed
to remind you of `ls -t` and also of "LaST changes".

`git-quickpush` is useful when you want to push commits to a branch
that is frequenty updated by many other people: You fetch the branch,
rebase your commits onto the branch, then push the branch---and the
push fails because someone else pushed to the branch after you fetched
and before you pushed.  `git-quickpush` does the three operations in
quick succession, and if it fails it's easy to rerun.
`git-quickmerge` is similar.  You specify a branch to merge to, and
some additional options to the `git-merge` command, and it fetches the
target branch, merges to it, and pushes it back, again in quick
succession.
                
`git-re-edit` runs `git-dirtyfiles` and invokes the editor on all the
currently-dirty files, or if there are no dirty files, the files that
changed in the last commit.

1. Suppose you left your working directory dirty when you went home,
and for some reason the editor has exited, and you want to open up the
editor again to edit the same batch of files. That is the basic use
case. You invoke "git re-edit" and it runs the editor on the dirty
files.
2. After `git reset HEAD^`, use `git re-edit` to invoke the editor on the files that were changed in the last commit.
3. During an interactive rebase that results in a complicated merge, you would like to run the editor on the conflicted files to resolve the conflicts. `git re-edit` will do that.
4. You or a coworker refactors some code.  Later, you'd like to refactor the same code some more.  Use `git re-edit` *commit* to edit the files that were changed in that commit.

`git-todays-commits` is supposed to list the commits that I made today.

`git-treehash` compares the object IDs of the *trees* of two or more
commits. This tells you if the commits have identical contents.  For
example, after a rebase in which commits are reordered, the new commit
should have an identical tree to the original commit.  `git treehash`
with no arguments will compare the trees of `HEAD` and `ORIG_HEAD`.
If they are identical, it will print out the single hash of the tree.
Otherwise, it will print out both hashes, labeled.  Given multiple
commits, it will print out all the tree hashes, each labeled with the
ref names to which it applies, so that you can see which commits have
identical trees and which don't.

`git-vee` compares two branches, showing the history back to the point
at which they diverged.  By default it compares the current `HEAD`
with its remote-tracked branch:

    git vee                 # compare current head branch and its remote tracking branch
    git vee remote          # compare current head branch and its analog on the remote
    git vee remote branch   # compare branch and remote/branch
    git vee branch          # compare HEAD and branch
    git vee branch1 branch2 # compare branch1 and branch2

Commits are indicated with stars, except that commits that are present
in both branches are indicated with `=` signs.

`git-what-changed` gets options and arguments like those for
`git-log`, and then emits the filenames, and only the filenames, that
have been changed in the selected commits.  By default this lists the
changed files in reverse chronological order.  I have an alias, `git
myfiles`, which means `git what-changed --author=mjd`, which lists the 
files I have recently touched. 

`git-whats` records a description for a branch, or prints a previously
recorded description.

`trim` is not really a git command.  It trims trailing whitespace from
the files named in its arguments.

`conf/dot-gitconfig` and `conf/dot-gitignore` contain my personal
`.gitconfig` and `.gitignore` files.

`bash_completion.d` contains completion scripts, which, if sourced,
will enable bash command-line completion for some of these commands.

-------

A discussion of how I use some of these tools is available at [My Git Habits](http://blog.plover.com/prog/git-habits.html).
