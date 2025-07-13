# git

## commit messages


## concepts

git dir vs working tree
index
stash

head, merge_head

pathspec

commit-ish

## configuration

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
-- https://stackoverflow.com/questions/10725729/see-whats-in-a-stash-without-applying-it

git clone 
- b flag 
- --single-branch command

`$#` is the number of argumente

git tag

git branch

- moving a branch 

## git log

get parents of a commit
```
    git log --pretty=%P -n 1 <commit hash>
```


## specific actions

finding commit that include some line 
```
git log -c -S'self.optimizer' experiment_toptagging.py
```

ammending commit

squash commit

Switch to a previous commit in a detached state
```
    git switch -d <commit hash>
```

get common ancestor of two commitish
```
git merge-base <c1> <c2>
```

git describe

git grep

git worktree add

git filter-branch

git apply

delete remote branch

## submodules


## errors

fatal: the remote end hung up unexpectedly (file size too large)
```
git config http.postBuffer 524288000
```


# references
- [git debug](https://lucasoshiro.github.io/posts-en/2023-02-13-git-debug/)
