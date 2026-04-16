# Git

## Forking workflow

### Setup

1. Fork the official repository
2. Clone my fork
```bash
git clone <my fork's SSH remote URL>
```
3. Add the upstream
```bash
git remote add upstream <official repository's SSH remote URL>
```

### Start new work

````bash
git checkout main
git fetch upstream
git rebase upstream/main
git push origin main
git checkout -b feature/<name>
````

### Daily work

```bash
git add <files>
git commit -m "message"
git push origin feature/<name>
```

### Sync with main before merging

```bash
git fetch upstream
git rebase upstream/main
git push --force-with-lease origin feature/<name>
```

> [!NOTE]
> `--force-with-lease` is safer than `--force` since it refuses if someone else pushed since your last fetch.

#### If rebase hits a conflict

```bash
# fix the conflict in your editor, then:
git add <resolved files>
git rebase --continue

# to bail out entirely:
git rebase --abort
```



