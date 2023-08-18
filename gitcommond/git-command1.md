# Practise Git Command

## Restore other's Git repo

- Cancel the remote link with other's git repo

```
git remote rm origin
```

- Link the local library with my git repo

```
git remote add origin ****
```
- Push

```
git push -u origin <new_branch>
```

## Create a branch based on the tag.

- Get the latest

```
git fetch --tags
```

- Create a new branch based on the tag

```
git branch <new-branch-name> <tag-name>
```

- Switch and push new branch

```
git checkout newbranch
git push origin newbranch
```
