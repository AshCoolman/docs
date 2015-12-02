# A Git-izens guide to goodness

## Guidelines

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
