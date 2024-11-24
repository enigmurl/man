# git

## concepts

git dir vs working tree
index
stash

head, merge_head

pathspec

commit-ish

## general actions

rebase (onto)
- we can think of it as upstream -> branch represents the range of commits to rebase. By default, we put these commits back onto the upstream, but we can change that to some other field with onto.

- interactive rebase

git cherry-pick

merge (from)

pull

pull (rebase)

revert commit

solve merge conflict

git log

git blame

git bisect

git switch

git show

git diff

git reset

git rm

git stash

git tag

git branch

## specific actions

Switch to a previous commit in a detached state
```
    git switch -d <commit hash>
```

git grep

git worktree add

git filter-branch

# references
- [git debug](https://lucasoshiro.github.io/posts-en/2023-02-13-git-debug/)
