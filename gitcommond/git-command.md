# Practise Git Command

- Clone Your Code

```
git clone <your forked repo>
```

- Add upstream

```
git remote add upstream https://github.com/multicloudlab/mygit
```
- Cut Branch

```
git checkout -b mygit upstream/master
```

- How to update PR
  - update commit with --amend

  ```
  git commit --amend
  ```
  ```
  git push -f origin mygit
  ```
  - add new commit
  ```
  git commit -a
  ```
  ```
  git push -f origin mygit
  ```
- How to squash your commits
  - Refer to https://www.devroom.io/2011/07/05/git-squash-your-latests-commits-into-one/ for detail.
- How to rebase
```
git fetch upstream
```
```
git rebase upstream/master
```
- How to resolve conflict
If `git rebase` has some conflict, then we need to resolve conflict first.
```
git rebase upstream/master
```

```
Update files to resolve conflict
```

```
git rebase --continue
```

```
git add .
```

```
git commit --amend
```
