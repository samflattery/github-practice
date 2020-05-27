# GitHub Codelab

## General overview

This codelab will help you get started using GitHub and making changes by
forking and sending PRs against a repository. The basic workflow will look
something like this:

1. Fork the github.com/akonradi/github-practice repository
1. Clone your fork on your development machine
1. Create a branch for development
1. Make some changes
1. Send a pull request to the upstream repository
1. Respond to feedback
1. (if necessary) Merge master into your PR
1. [Your PR is merged]
1. Cleaning up

## Forking the repository

To start making changes against the upstream repository, you'll need a fork of
it that you can play around with. A *fork* is a copy of the original repository
that you own, but that is otherwise disconnected from the original. You can read
more about forking [here](https://guides.github.com/activities/forking/).

To make a fork, navigate to [http://github.com/akonradi/github-practice](http://github.com/akonradi/github-practice) and click
the "Fork" button on the right. You should now have a copy of this repository at
http://github.com/$GITHUB_USERNAME/github-practice, where here and throughout `$GITHUB_USERNAME` is (unsurprisingly) your username on GitHub.

## Cloning your fork

Now that you have a private copy of the repository on GitHub, it's time to clone that to your local machine. To do that, `cd` to wherever you want to put the code (`~/git`, for instance) and use the `git clone` command:

```
git clone git@github.com:$GITHUB_USERNAME/github-practice.git
```

This will create a new directory called `github-practice` in your current working directory. Now
```
cd github-practice
```
to inspect it.

When you clone a remote repository, git automatically adds a reference to the original one. Let's take a look. Run
```
git remote -v
```
to list the configured remote repositories for your local clone. You should see something like this:

```
$ git remote -v
origin  git@github.com:$GITHUB_USERNAME/github-practice (fetch)
origin  git@github.com:$GITHUB_USERNAME/github-practice (push)
```

This means your local clone is set up with one remote repository, which it calls `origin`. When it tries to pull commits from `origin` it will use the top URL, and when pushing it will use the bottom one (though the URLs are identical).

## Create a branch

Git tracks the contents of your repository in a series of diffs called "commits". You can check out a commit to make your working directory reflect the state of the repository at that commit. While each commit can be uniquely identified by its 256-bit hash, it's much more convenient to be able to track development using "branches". A branch is a label that points to a single commit, but which can be moved to point at a different commit. Unless your repository is in a "detached HEAD" state, you'll always be working on a branch. You can see which one by running
```
git status
```
which should show you something like this:
```
$ git status
On branch master
Your branch is up to date with 'origin/master'.

nothing to commit, working tree clean
```

By convention, the default branch that is created in a new repository is called "master", and "origin/master" refers to "where this repository thinks the `master` brach on the remote called `origin` is".

For development, you'll typically want to create a new branch for each feature. To create a new branch, you can use `git switch -c` (or `git checkout -b`, which does the same thing, though `switch` is preferred since the `checkout` command is so overloaded). Do this now:
```
git switch -c feature-branch
```
You should end up with a new branch called "feature-branch" checked out, which you can confirm with `git status`:
```
$ git status
On branch feature-branch
nothing to commit, working tree clean
```
## Making some changes
If you poke around this repository, you'll see a couple files in the `abstracts/` directory. We're going to introduce some edits for the purpose of demonstration. Go ahead and use your favorite editor to change a couple words in one of the files. Then (with substitution of an appropriate message) run
```
git commit --all -m "[YOUR_COMMIT_MESSAGE_HERE]" 
```
Note that you could generally use `git add` to add changes to the index and then `git commit` to type out a real commit message, but we're bypassing that here for brevity.

Now that you've committed your changes, you can go ahead and push them to your fork on GitHub:
```
git push --set-upstream origin feature-branch
```
The `--set-upstream` flag tells git that you want to associate the local branch named "feature-branch" with the branch named "feature-branch" on the remote server called "origin". After doing this once you can use `git push` to push all commits on the current branch.

## Send a pull request

Now that you've pushed your change to your fork, go ahead and create a new pull request. You can do this by clicking through the GitHub UI or going to the URL suggested by the output of your first `git push`. Either way, make sure you're creating a pull request with the "master" branch of https://github.com/akonradi/github-practice/ as the base, and your fork's "feature-branch" as the "compare".

At this point, you should have a PR open and accessible at https://github.com/akonradi/github-practice/pull/$N for some N.

## Respond to feedback

In order for your PR to be merged, it must be approved by one of the repository maintainers. Before approval, your PR will can go through multiple rounds of review and revision. Reviewers will frequently ask for changes through comments, and you can address these by replying or by making commits and pushing changes to your PR branch.

If you'd like, feel free to try out making or replying to a comment on your or some other open PR in the repository.

## Merge master into your PR

Git is really good at merging commits that operate on different areas of the codebase, even different lines in the same file! This makes it easy for developers to work in parallel without stepping on each others' toes. When you make a PR, you're requesting that your changes be merged into some other branch (usually the upstream "master"). To keep your change small, you should always start development off of the tip of the upstream branch. Sometimes, though, by the time your PR is ready to merge, the upstream branch will have been changed in some incompatible way that prevents automatic merging.

To resolve this conflict, you'll need to pull the current state of the upstream branch into your repository, and merge the changes manually. Then you can push the resolved state to update your PR.

To pull the current state of the upstream branch into your development repository, you'll first need to tell your local repository about the upstream one. We can use the `git remote` tool for this:
```
git remote add upstream git@github.com:akonradi/github-practice.git
```
Now, `git remote -v` shows both repositories: "upstream" and "origin":
```
$ git remote -v
origin  git@github.com:$GITHUB_USERNAME/github-practice.git (fetch)
origin  git@github.com:$GITHUB_USERNAME/github-practice.git (push)
upstream        git@github.com:akonradi/github-practice.git (fetch)
upstream        git@github.com:akonradi/github-practice.git (push)
```

To get all commits from the upstream repository, you can run `git fetch`:
```
git fetch upstream
```

And merging master is, unsuprisingly, done using `git merge`:
```
git merge upstream/master
```

If there are no issues, `git merge` will complete successfully and you'll be ready to push. Otherwise, you might see something like this:

```
$ git merge upstream/master
Auto-merging abstracts/gfs.md
CONFLICT (content): Merge conflict in abstracts/gfs.md
Automatic merge failed; fix conflicts and then commit the result.
```
Looking at the affected file(s), you'll see merge conflict markers ("<<<<<<<", "=======", ">>>>>>>") to show you what lines conflicted. Go ahead and use your favorite editor to fix the conflicts to reflect the logical merging of your desired change with the upstream state - this usually involves either keeping one of the bits in between the conflict markers, or taking pieces from each. Don't forget to remove the conflict markers!

When you're done editing the file(s), use `git add` to record the resolution:
```
git add abstracts/*.md
```
and then finish the merge by committing:
```
git commit
```

Now push your changes to your fork so it is ready for another round of review:
```
git push
```

## \[Your PR is merged\]

Once a maintainer merges your code, you're in! Your contribution is permanently recorded in the repository history. Time to celebrate!

## Cleaning up

This step is optional, but highly recommended unless you're never going to use your local repository again. Now that your PR is merged, you should update your local repository with the changes. Go ahead and check out the master branch:
```
git switch master
```

and then pull the latest state into your branch. We did this before using `git fetch` and then `git merge`, but it's easier to use `git pull`:
```
git pull upstream master
```

You can check that your commit is there by looking through the commit log:
```
git log
```

Once you're satisfied, go ahead and delete your local branch:
```
git branch -D feature-branch
```
There's nothing wrong with having it around, but it will clutter other output unnecessarily. Once that's done, you're ready to check out a new branch and start work on a new feature!
