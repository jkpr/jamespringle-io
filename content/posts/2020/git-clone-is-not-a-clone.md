+++
title = "Git clone is not a clone"
date = "2020-11-10T21:15:14-06:00"
author = "James K. Pringle"
authorTwitter = "" #do not include @
cover = ""
tags = ["git"]
keywords = ["", ""]
description = ""
showFullContent = false
+++

Let's say you perusing [github.com](https://github.com) and you find a shiny, new repository that catches your developer's eye. Take, for
example, [_Black_](https://github.com/psf/black), the uncompromising code formatter for Python. You want it. You want
all of its branches.

{{< image src="/static/img/github-black-branches.png" position="center" alt="Repository Black on Github with branches" >}}

Naturally, you perform a `git clone` to make a copy of the repository on your own (local) computer.

{{< image src="/static/img/git-clone-result.png" position="center" alt="Git clone console output" >}}

But after running `git branch` to admire all of your branches, you are greeted with a lonely `master` branch :confused:.

{{< image src="/static/img/git-clone-branch.png" position="center" alt="Git branch console output" >}}

You thought you were getting a clone, an exact copy of the repository on Github. But clearly, that isn't so! You only
have the `master` branch, right?

At times like these, we need to get oriented. We need to get grounded in truth. Let's read the docs! The description of
`git clone` from [https://git-scm.com/docs/git-clone](https://git-scm.com/docs/git-clone#_description):

> DESCRIPTION
>
> Clones a repository into a newly created directory, creates remote-tracking branches for each branch in the cloned repository (visible using `git branch --remotes`), and creates and checks out an initial branch that is forked from the cloned repository’s currently active branch.

This explains why there is only a `master` branch after a `git clone`.

So there is something called a "remote-tracking branch" and I can see all of them with `git branch --remotes`.

{{< image src="/static/img/git-clone-branch-remotes.png" position="center" alt="Git branch remotes console output" >}}

From `git branch --help`, we learn option `-r` or `--remotes` causes the remote-tracking branches to be listed, and
option `-a` or `--all` shows both local and remote branches. So now we know there is a distinction between a _local_ and
a _remote_ branch. Perhaps another day we will discuss that distinction. In the meantime, see the [official Git book
section on remote branches](https://git-scm.com/book/en/v2/Git-Branching-Remote-Branches) for further reading.

Now that we have seen we have all the remote-tracking branches with `git branch --remotes` (or `git branch -r` for
short) we are a little more reassured that we have everything. We have one remote-tracking branch for each branch on
Github. But how do we work on those branches?

Remote branches are referenced in the form `<remote-name>/<branch>`, so in our Black example, we could say `git checkout
origin/dirty-docs` and our `HEAD` would be moved over there. This is useful for looking at files or reviewing logs. But
we cannot add commits to remote-tracking branches. What we would rather do is make a local branch "be the same" as a
branch from the remote repository.

```
git checkout -b <branch> <remote>/<branch>
```

is the natural way to do that. We create our own branch starting from the commit at `<remote>/<branch>`. That is from Git 101.

Git is kind enough that there is a shortcut for this:

```
git checkout --track <remote>/<branch>
```

In fact, this is _so_ common that the shortcut has a shortcut.

```
git checkout <branch>
```

is for the common scenario that the `<branch>` does not exist yet and `<branch>` matches only one remote (a requirement
since we can have multiple remotes and we can have remote-tracking branches for each of them). Notice that the
`<remote>` part is not there. In our example, we have only cloned Black, so there is only one remote, called `origin`,
and the only local branch so far is `master`. Therefore, we can simply `git checkout dirty-docs`&mdash;using the shortcut to
the shortcut&mdash;and then we have it locally to work on.

{{< image src="/static/img/git-clone-checkout.png" position="center" alt="Git checkout console output" >}}

This checkout automatically sets up the upstream information for our new `dirty-docs` branch, so that a simple `git
pull` works.

# Conclusion

Does `git clone` make a clone? "No", in the sense that you do not get an exact working copy of the remote repository.
But "yes", in the sense that all the information is there.

If you want to clone a repository and work on a branch, use:

```
git clone <repo>
git branch -r          # to see all the remote branches
git checkout <branch>  # to create a local branch from a remote-tracking branch
```

And voila!
