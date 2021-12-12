---
title: "Shell script fun"
date: "2021-10-15"
tags: ["git", "Shell"]
---

For a while I've had a .bash_profile that creates a custom prompt which includes the nme of the current git branch. Recently I wanted to reuse that idea for something just slightly different, and it turned into a crash course in shell scripting.

<!--more-->

Here's the relevant part of my .bash_profile:

```
function parse_git_branch() {
    git branch 2> /dev/null | sed -e '/^[^*]/d' -e 's/* \(.*\)/ (\1)/'
}

export PS1="\[\e[7m\] \w\$(parse_git_branch) \[\e[0m\] \n$ "
```

This is based on a code snippet that has been copied far and wide on the internet. Briefly, the function there runs `git branch` and pipes the output to `sed`. Sed executes two commands. First, it filters out all the lines that don't begin with a star (i.e., it returns the current branch). Second, it cleans hat line up, removing the star and space and surrounding it with parentheses.

The function gets called in the last line there, with `$(parse_git_branch)`. This implies that shell script functions have an implicit return from their last line.

I was trying to write a shell script to run an rsync to upload stuff to a server folder based on the current git branch, so I copied the function above and followed it with something along these lines

```
rsync -r dist/* user@server.name:/usr/share/path/html/$(get_git_branch)
```

This works fine, but unfortunately if there is no current git branch, it will overwrite the contents of the html folder. To fix that, we need a simple check. Here's how I finally got it working:

```
function get_git_branch() {
  git branch 2> /dev/null | sed -e '/^[^*]/d' -e 's/* \(.*\)/\1/'
}

if [ `echo $(get_git_branch) | wc -c` -gt 0 ]
then

echo Syncing ...
rsync -r dist/* user@server.name:/usr/share/path/html/$(get_git_branch)

else

  echo 'Sync aborted, could not find a current git branch.'

fi

```

In the if statement, we echo the output of the function call into `wc -c` which returns the number of characters. If the output is greater than (`-gt`) zero, the sync gets executed, otherwise we get the helpful error message. This was my first attempt at this kind of shell scripting, and figuring it out was fun. Fi, indeed.
