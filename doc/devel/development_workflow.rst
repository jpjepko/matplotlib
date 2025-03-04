.. highlight:: bash

.. _development-workflow:

####################
Development workflow
####################

Workflow summary
================

To keep your work well organized, with readable history, and in turn make it
easier for project maintainers (that might be you) to see what you've done, and
why you did it, we recommend the following:

* Don't use your ``main`` branch for anything.  Consider deleting it.
* Before starting a new set of changes, fetch all changes from
  ``upstream/main``, and start a new *feature branch* from that.
* Make a new branch for each feature or bug fix — "one task, one branch".
* Name your branch for the purpose of the changes - e.g.
  ``bugfix-for-issue-14`` or ``refactor-database-code``.
* If you get stuck, reach out on Gitter or
  `discourse <https://discourse.matplotlib.org>`__.
* When you're ready or need feedback on your code, open a pull request so that the
  Matplotlib developers can give feedback and eventually include your suggested
  code into the ``main`` branch.

.. note::

   It may sound strange, but deleting your own ``main`` branch can help reduce
   confusion about which branch you are on.  See `deleting main on github`_ for
   details.

.. _deleting main on github: https://matthew-brett.github.io/pydagogue/gh_delete_master.html

.. _update-mirror-main:

Update the ``main`` branch
==========================

First make sure you have followed :ref:`installing_for_devs`.

From time to time you should fetch the upstream changes from GitHub::

   git fetch upstream

This will pull down any commits you don't have, and set the remote branches to
point to the right commit.

.. _make-feature-branch:

Make a new feature branch
=========================

When you are ready to make some changes to the code, you should start a new
branch.  Branches that are for a collection of related edits are often called
'feature branches'.

Making an new branch for each set of related changes will make it easier for
someone reviewing your branch to see what you are doing.

Choose an informative name for the branch to remind yourself and the rest of us
what the changes in the branch are for.  For example ``add-ability-to-fly``, or
``bugfix-for-issue-42``.

::

    # Update the main branch
    git fetch upstream
    # Make new feature branch starting at current main
    git branch my-new-feature upstream/main
    git checkout my-new-feature

Generally, you will want to keep your feature branches on your public GitHub
fork of Matplotlib.  To do this, you ``git push`` this new branch up to your
GitHub repo.  Generally (if you followed the instructions in these pages, and by
default), git will have a link to your fork of the GitHub repo, called
``origin``.  You push up to your own fork with::

   git push origin my-new-feature

In git >= 1.7 you can ensure that the link is correctly set by using the
``--set-upstream`` option::

   git push --set-upstream origin my-new-feature

From now on git will know that ``my-new-feature`` is related to the
``my-new-feature`` branch in the GitHub repo.

.. _edit-flow:

The editing workflow
====================

Overview
--------

::

   # hack hack
   git add my_new_file
   git commit -am 'NF - some message'
   git push

In more detail
--------------

#. Make some changes
#. See which files have changed with ``git status``.
   You'll see a listing like this one:

   .. code-block:: none

     # On branch ny-new-feature
     # Changed but not updated:
     #   (use "git add <file>..." to update what will be committed)
     #   (use "git checkout -- <file>..." to discard changes in working directory)
     #
     #	modified:   README
     #
     # Untracked files:
     #   (use "git add <file>..." to include in what will be committed)
     #
     #	INSTALL
     no changes added to commit (use "git add" and/or "git commit -a")

#. Check what the actual changes are with ``git diff``.
#. Add any new files to version control ``git add new_file_name``.
#. To commit all modified files into the local copy of your repo,, do
   ``git commit -am 'A commit message'``.  Note the ``-am`` options to
   ``commit``. The ``m`` flag just signals that you're going to type a
   message on the command line.  The ``a`` flag — you can just take on
   faith — or see `why the -a flag?`_ — and the helpful use-case
   description in the `tangled working copy problem`_. The
   `git commit <https://git-scm.com/docs/git-commit>`_ manual page might also be
   useful.
#. To push the changes up to your forked repo on github, do a ``git
   push``.

.. _why the -a flag?: http://gitready.com/beginner/2009/01/18/the-staging-area.html
.. _tangled working copy problem: http://2ndscale.com/rtomayko/2008/the-thing-about-git


Open a pull request
===================

When you are ready to ask for someone to review your code and consider a merge,
`submit your Pull Request (PR) <https://docs.github.com/pull-requests>`_.

Enter a title for the set of changes, and some explanation of what you've done.
Say if there is anything you'd like particular attention for - like a
complicated change or some code you are not happy with.

If you don't think your request is ready to be merged, just say so in your pull
request message and use the "Draft PR" feature of GitHub. This is a good way of
getting some preliminary code review.

Some other things you might want to do
======================================

Explore your repository
-----------------------

To see a graphical representation of the repository branches and
commits::

   gitk --all

To see a linear list of commits for this branch::

   git log

.. _rebase-on-main:

Rebasing on ``upstream/main``
-----------------------------

Let's say you thought of some work you'd like to do. You
:ref:`update-mirror-main` and :ref:`make-feature-branch` called
``cool-feature``. At this stage, ``main`` is at some commit, let's call it E.
Now you make some new commits on your ``cool-feature`` branch, let's call them
A, B, C. Maybe your changes take a while, or you come back to them after a
while. In the meantime, ``main`` has progressed from commit E to commit (say) G:

.. code-block:: none

          A---B---C cool-feature
         /
    D---E---F---G main

At this stage you consider merging ``main`` into your feature branch, and you
remember that this here page sternly advises you not to do that, because the
history will get messy. Most of the time you can just ask for a review, and not
worry that ``main`` has got a little ahead.  But sometimes, the changes in
``main`` might affect your changes, and you need to harmonize them.  In this
situation you may prefer to do a rebase.

``rebase`` takes your changes (A, B, C) and replays them as if they had been
made to the current state of ``main``.  In other words, in this case, it takes
the changes represented by A, B, C and replays them on top of G. After the
rebase, your history will look like this:

.. code-block:: none

                  A'--B'--C' cool-feature
                 /
    D---E---F---G main

See `rebase without tears`_ for more detail.

.. _rebase without tears: https://matthew-brett.github.io/pydagogue/rebase_without_tears.html

To do a rebase on ``upstream/main``::

    # Fetch changes from upstream/main
    git fetch upstream
    # go to the feature branch
    git checkout cool-feature
    # make a backup in case you mess up
    git branch tmp cool-feature
    # rebase cool-feature onto main
    git rebase --onto upstream/main upstream/main cool-feature

In this situation, where you are already on branch ``cool-feature``, the last
command can be written more succinctly as::

    git rebase upstream/main

When all looks good you can delete your backup branch::

   git branch -D tmp

If it doesn't look good you may need to have a look at
:ref:`recovering-from-mess-up`.

If you have made changes to files that have also changed in ``main``, this may
generate merge conflicts that you need to resolve - see the `git rebase`_ man
page for some instructions at the end of the "Description" section. There is
some related help on merging in the git user manual - see `resolving a merge`_.

.. _git rebase: https://git-scm.com/docs/git-rebase
.. _resolving a merge: https://schacon.github.io/git/user-manual.html#resolving-a-merge


.. _recovering-from-mess-up:

Recovering from mess-ups
------------------------

Sometimes, you mess up merges or rebases. Luckily, in git it is
relatively straightforward to recover from such mistakes.

If you mess up during a rebase::

   git rebase --abort

If you notice you messed up after the rebase::

   # reset branch back to the saved point
   git reset --hard tmp

If you forgot to make a backup branch::

   # look at the reflog of the branch
   git reflog show cool-feature

   8630830 cool-feature@{0}: commit: BUG: io: close file handles immediately
   278dd2a cool-feature@{1}: rebase finished: refs/heads/my-feature-branch onto 11ee694744f2552d
   26aa21a cool-feature@{2}: commit: BUG: lib: make seek_gzip_factory not leak gzip obj
   ...

   # reset the branch to where it was before the botched rebase
   git reset --hard cool-feature@{2}

.. _rewriting-commit-history:

Rewriting commit history
------------------------

.. note::

   Do this only for your own feature branches.

There's an embarrassing typo in a commit you made? Or perhaps the you
made several false starts you would like the posterity not to see.

This can be done via *interactive rebasing*.

Suppose that the commit history looks like this::

    git log --oneline
    eadc391 Fix some remaining bugs
    a815645 Modify it so that it works
    2dec1ac Fix a few bugs + disable
    13d7934 First implementation
    6ad92e5 * masked is now an instance of a new object, MaskedConstant
    29001ed Add pre-nep for a copule of structured_array_extensions.
    ...

and ``6ad92e5`` is the last commit in the ``cool-feature`` branch. Suppose we
want to make the following changes:

* Rewrite the commit message for ``13d7934`` to something more sensible.
* Combine the commits ``2dec1ac``, ``a815645``, ``eadc391`` into a single one.

We do as follows::

    # make a backup of the current state
    git branch tmp HEAD
    # interactive rebase
    git rebase -i 6ad92e5

This will open an editor with the following text in it::

    pick 13d7934 First implementation
    pick 2dec1ac Fix a few bugs + disable
    pick a815645 Modify it so that it works
    pick eadc391 Fix some remaining bugs

    # Rebase 6ad92e5..eadc391 onto 6ad92e5
    #
    # Commands:
    #  p, pick = use commit
    #  r, reword = use commit, but edit the commit message
    #  e, edit = use commit, but stop for amending
    #  s, squash = use commit, but meld into previous commit
    #  f, fixup = like "squash", but discard this commit's log message
    #
    # If you remove a line here THAT COMMIT WILL BE LOST.
    # However, if you remove everything, the rebase will be aborted.
    #

To achieve what we want, we will make the following changes to it::

    r 13d7934 First implementation
    pick 2dec1ac Fix a few bugs + disable
    f a815645 Modify it so that it works
    f eadc391 Fix some remaining bugs

This means that (i) we want to edit the commit message for
``13d7934``, and (ii) collapse the last three commits into one. Now we
save and quit the editor.

Git will then immediately bring up an editor for editing the commit
message. After revising it, we get the output::

    [detached HEAD 721fc64] FOO: First implementation
     2 files changed, 199 insertions(+), 66 deletions(-)
    [detached HEAD 0f22701] Fix a few bugs + disable
     1 files changed, 79 insertions(+), 61 deletions(-)
    Successfully rebased and updated refs/heads/my-feature-branch.

and the history looks now like this::

     0f22701 Fix a few bugs + disable
     721fc64 ENH: Sophisticated feature
     6ad92e5 * masked is now an instance of a new object, MaskedConstant

If it went wrong, recovery is again possible as explained :ref:`above
<recovering-from-mess-up>`.
