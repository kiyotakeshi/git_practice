# stash

- ワークツリーとステージの作業を一時的に stash に退避する

- ファイルの内容を変更

```
$ git status
On branch master
Your branch is up to date with 'origin/master'.

nothing to commit, working tree clean

$ vi git_tutorial/home.txt                                                [/Users/
$ git status                                                             [/Users/

modified:   git_tutorial/home.txt

```

- stash に逃す

```
$ git stash
Saved working directory and index state WIP on master: 56f134f add tag

# 変更がないことになっている
$ git status
On branch master
Your branch is up to date with 'origin/master'.

nothing to commit, working tree clean

```

- 避難した作業の一覧を確認

```
$ git stash list
stash@{0}: WIP on master: 56f134f add tag

```

- 最新の作業の復元

```
# --index をつけるとステージング内容も復元
$ git stash apply --index

On branch master
Your branch is up to date with 'origin/master'.

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

	modified:   git_tutorial/home.txt

no changes added to commit (use "git add" and/or "git commit -a")

# 確認
$ git status                                                             [/Users/

	modified:   git_tutorial/home.txt

```

- 特定の作業を復元する場合

```
# {1} は git stash list で確認した戻したい点を使う
$ git stash apply stash@{1}

```

- 避難した作業を削除する

```
# 確認
$ git stash list
stash@{0}: WIP on master: 56f134f add tag
stash@{1}: WIP on master: 56f134f add tag

# 削除
$ git stash drop
Dropped refs/stash@{0} (01712e185d124fbcc274c308a2fb60034f388d19)

$ git stash drop
Dropped refs/stash@{0} (7e82ba74e392131ef19e2c9fef2393f22a876552)

```

