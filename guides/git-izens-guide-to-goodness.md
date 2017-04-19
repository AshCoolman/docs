---
layout: page
type: guide
title: Gitizenʼs guide to goodness
permalink: /guides/git-izens-guide-to-goodness/
---
## Learn git

* Tutorial: http://learngitbranching.js.org/
* Cheat sheet: http://ndpsoftware.com/git-cheatsheet.html#loc=workspace;
* BONUS: https://www.youtube.com/watch?v=idLyobOhtO4

## Guidelines

* Branch early, branch often
* Keep remote clean:

  * Only put something on the remote if you want others to see it
  * Delete branches once they are merged (or you no longer intend them to be merged)

* Branches/PR's tell a story:

  * Branch off often, and with a specific goal in mind
  * Where possible squash your "partial" commits into nice understandable units of functionality `rebase`

* Commit messages:

  * Short, have context, understandable 1 year later
  * fix: when you achieve a branch outcome

## Tips

* Got various changes, and you want to test & commit in isolation?

  1. Select: Stage only the changes you want to commit right now
  2. Isolate: `git stash -u -k` to hide the changes you don't want to commit
  3. Test: run the code now its isolated
  4. Commit
  5. Setup: lay the groundwork for your next commit (checkout new branch, switch to different branch etc)
  6. Revive: `git stash pop` to reveal the uncommited changes

* Find commits with `searchterm` in comment:

 `git log --grep="searchterm"`

* Find all commits that modify a certain fail:

 `git log --follow filename`

* Add files interactively:

 `git add -i`
 
* Forgot to add changes to that last commit? Stage the changes and then:

 `git commit --amend -C HEAD`

* Search all commits messages:

`git log --grep="valid" --all --author=Ashley --pretty=oneline`

* Log commits that touched a line

`git log -L185,10:./<file>`

* Log last change to line

`git blame -L185,+5 -- ./<file>`

* Remove sensitive info from git history:

```
git filter-branch --index-filter \
'git update-index --remove passwords.txt' <introduction-revision-sha1>..HEAD
git push --force --verbose --dry-run
git push --force
```

## Setup

Don't use pager for git log [link](http://stackoverflow.com/a/17078021/225813)

`git config --global core.pager cat`

## Resources

* [Git guide](http://rogerdudler.github.io/git-guide/)
* [Git FAQ](https://git.wiki.kernel.org/index.php/Git_FAQ#Why_the_.27Git.27_name.3F): this is _actually_ good
* [Stackoverflow: Difference between HEAD / Working Tree / Index in Git](http://stackoverflow.com/questions/3689838/difference-between-head-working-tree-index-in-git)
