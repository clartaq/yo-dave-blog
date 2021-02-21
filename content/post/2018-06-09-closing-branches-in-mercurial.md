---
author: david
title: Closing Branches in Mercurial
date: 2018-06-09T16:47:43.545463-04:00
modified: 2018-06-09T16:47:56.111606-04:00
tags:
  - workflow
  - Mercurial
  - source control
---

As a lone developer, I have a workflow that I believe is pretty typical.

Most of my development work happens on the "tip" of the "default" branch. It's just easier to do work on fixing small bugs or adding minor features there.

For more significant, more difficult pieces of work, I usually create a "feature branch." I do this with the expectation that if things get messed up too badly, I can delete the branch and start over without affecting the readiness of the default branch.

Typically, after merging branches back into the main trunk of development, I leave them alone. They are inactive, but still there. But recently I decided that leaving them in place made the output of the `hg heads` command too messy. I needed to do some tidying. Now some of these branches were created years ago. I was curious about just how difficult it would be to close a branch from such a long time ago. It turns out it wasn't hard at all.

The sequence of steps is straightforward.

```bash
    $ hg up branch-name    # update to branch you want to close
    $ hg ci -m "Closing branch 'branch-name.'" --close-branch 
    $ hg up default        # back to default branch
    $ hg merge branch-name # merge the closed branch
    $ hg ci -m merge       # commit the merge of the closed branch
```

While closing about a dozen branches using this method, the only problem that I ran into was a case where an auto-generated file had once been added to the repository and was later removed. The solution was to delete the current version of that file and proceed as usual.
