# プルリクエスト

- プルリクエストは自分の変更したコードをリポジトリに取り込んでもらえるよう依頼する機能
    - これがないと勝手にmasterにコードを反映される

```
$ git checkout -b pull_request
Switched to a new branch 'pull_request'

$ git diff
diff --git a/git_tutorial/index.html b/git_tutorial/index.html
index c87f8fa..d88ebf2 100644
--- a/git_tutorial/index.html
+++ b/git_tutorial/index.html
@@ -2,3 +2,4 @@
 <p>git status</p>
 <p>git diff</p>
 <p>conflict</p>
+<p>pull request</p>
\ No newline at end of file

kiyotatakeshi@localhost:(pull_request*) $ git add git_tutorial/index.html

kiyotatakeshi@localhost:(pull_request+) $ git commit -m "add pull request"
[pull_request 25fe824] add pull request
 1 file changed, 1 insertion(+)

$ git push origin pull_request
Enumerating objects: 7, done.
Counting objects: 100% (7/7), done.
Delta compression using up to 4 threads
Compressing objects: 100% (4/4), done.
Writing objects: 100% (4/4), 390 bytes | 390.00 KiB/s, done.
Total 4 (delta 2), reused 0 (delta 0)
remote: Resolving deltas: 100% (2/2), completed with 2 local objects.
remote:
remote: Create a pull request for 'pull_request' on GitHub by visiting:
remote:      https://github.com/kiyotakeshi/git_practice/pull/new/pull_request
remote:
To https://github.com/kiyotakeshi/git_practice.git
 * [new branch]      pull_request -> pull_request

```

- あとはGitHub上でプルリクを作成

---
- GitHubとローカルで差があり過ぎてコンフリクトの修正が大変な時の修正例

```
# 既存のmasterブランチを削除
git branch -D master

# GitHubから取得
git fetch

# GitHubから取得した内容を基に master を作成
git branch master origin/master

# プルリクのコンフリクトを解消する
git checkout pull-request

git merge origin/master # この場合 master でも良い

# テキストエディタで開きコンフリクトを解消する

# GitHubにpush
git add .
git commit -m "fix conflict"
git push origin pull-request

# GitHub上でプルリクを作成、マージ
# GitHubから最新状態を取り込む
git checkout master
git pull origin master

# 不要になったブランチを削除
git branch -d pull_request
```

---
# rebase

- 他のブランチでの変更点を取り込みながら、履歴を整えた形で変更を統合する
    - mergeコマンドと同じことができるが、merge だと履歴が枝分かれする
-

```
kiyotatakeshi@localhost:(master) $ git status                                                                                                      [/Users/kiyotatakeshi/Desktop/GitHub/git_practice]
On branch master
Your branch is up to date with 'origin/master'.

nothing to commit, working tree clean
kiyotatakeshi@localhost:(master) $ git branch                                                                                                      [/Users/kiyotatakeshi/Desktop/GitHub/git_practice]
  feature
* master
  pull_request

```

- masterでファイルを新規作成

```

kiyotatakeshi@localhost:(master) $ git add .                                                                                                       [/Users/kiyotatakeshi/Desktop/GitHub/git_practice]
kiyotatakeshi@localhost:(master+) $ git commit                                                                                                     [/Users/kiyotatakeshi/Desktop/GitHub/git_practice]
[master 5d4c4a9] rebase_practice を新規作成
 1 file changed, 1 insertion(+)
 create mode 100644 git_tutorial/rebase_practice.html

```

- featureでファイルを新規作成

```
$ git checkout feature                                                                                            [/Users/kiyotatakeshi/Desktop/GitHub/git_practice]
Switched to branch 'feature'
kiyotatakeshi@localhost:(feature) $ git add .                                                                                                      [/Users/kiyotatakeshi/Desktop/GitHub/git_practice]
kiyotatakeshi@localhost:(feature+) $ git commit                                                                                                    [/Users/kiyotatakeshi/Desktop/GitHub/git_practice]
[feature aef4f14] rebase_practice_feature.html を追加
 1 file changed, 1 insertion(+)
 create mode 100644 git_tutorial/rebase_practice_feature.html

```

- featureブランチにmasterブランチの内容をrebaseで取り込む

```
kiyotatakeshi@localhost:(feature) $ git rebase master                                                                                              [/Users/kiyotatakeshi/Desktop/GitHub/git_practice]
First, rewinding head to replay your work on top of it...
Applying: rebase_practice_feature.html を追加

# master ブランチでの変更内容も取り込まれている
kiyotatakeshi@localhost:(feature) $ git log --oneline                                                                                              [/Users/kiyotatakeshi/Desktop/GitHub/git_practice]
c1b0a03 (HEAD -> feature) rebase_practice_feature.html を追加
5d4c4a9 (master) rebase_practice を新規作成

```

- masterブランチにfeatureブランチの内容を fast-forward で取り込む
    - 同じ内容で向き先を変えただけなので、新しいマージコミットは生まれない

```
kiyotatakeshi@localhost:(feature) $ git checkout master                                                                                            [/Users/kiyotatakeshi/Desktop/GitHub/git_practice]
Switched to branch 'master'
Your branch is ahead of 'origin/master' by 1 commit.
  (use "git push" to publish your local commits)

kiyotatakeshi@localhost:(master) $ git merge feature                                                                                               [/Users/kiyotatakeshi/Desktop/GitHub/git_practice]
Updating 5d4c4a9..c1b0a03
Fast-forward
 git_tutorial/rebase_practice_feature.html | 1 +
 1 file changed, 1 insertion(+)
 create mode 100644 git_tutorial/rebase_practice_feature.html

```

- 変更内容をpushしてブランチを削除

```
kiyotatakeshi@localhost:(master) $ git push origin master                                                                                          [/Users/kiyotatakeshi/Desktop/GitHub/git_practice]
Enumerating objects: 10, done.
Counting objects: 100% (10/10), done.
Delta compression using up to 4 threads
Compressing objects: 100% (6/6), done.
Writing objects: 100% (8/8), 792 bytes | 792.00 KiB/s, done.
Total 8 (delta 3), reused 0 (delta 0)
remote: Resolving deltas: 100% (3/3), completed with 1 local object.
To https://github.com/kiyotakeshi/git_practice.git
   40f68e9..c1b0a03  master -> master

kiyotatakeshi@localhost:(master) $ git branch -d feature                                                                                           [/Users/kiyotatakeshi/Desktop/GitHub/git_practice]
Deleted branch feature (was c1b0a03).

kiyotatakeshi@localhost:(master) $ git branch                                                                                                      [/Users/kiyotatakeshi/Desktop/GitHub/git_practice]
* master
  pull_request

```

- **GitHubにpushしたコミットをrebaseしない!!!!**
    - 履歴を整える代わりにローカルとGitHubにある履歴情報に矛盾が生じるため
    - push -f は絶対NG(履歴が完全に壊れるため)

- リベースを使うかマージを使うかはチームの方針次第
    - 作業の履歴を残したいならmerge
    - 履歴を綺麗に残したいならrebase

- マージの場合はコンフリクトは一度しか発生しない

- リベースは各コミットごとにコンフリクトが発生する

- 参考にする開発手法
    - pushしていないローカルの変更にはrebase,pushしたあとはマージを使用
    - コンフリクトしそうならmerge(他の人が何をしているかわかるのが前提)

---

- プルの設定をrebaseに設定する
    - マージコミットが残らないため、GitHubの内容を取得したいだけの場合におすすめ

```
# 一度だけ、rebaseにする場合
git pull --rebase origin master

# masterのブランチでpullするときだけ、rebaseにする場合
$ git config branch.master.rebase true

```

```
kiyotatakeshi@localhost:(master) $ git config --global pull.rebase true


kiyotatakeshi@localhost:(master) $ cat ~/.gitconfig                                                                                                [/Users/kiyotatakeshi/Desktop/GitHub/git_practice]
[user]
	email = kiyotatakeshi0609@gmail.com
	name = kiyotakeshi
[pull]
	rebase = true

```

---
# リベースで複数の履歴を書き換える

- 3回分コミットする

```
kiyotatakeshi@localhost:(master) $ git status                                                                                                      [/Users/kiyotatakeshi/Desktop/GitHub/git_practice]
On branch master
Your branch is up to date with 'origin/master'.

nothing to commit, working tree clean

kiyotatakeshi@localhost:(master) $ touch git_tutorial/first.html                                                                                   [/Users/kiyotatakeshi/Desktop/GitHub/git_practice]

kiyotatakeshi@localhost:(master) $ git add .                                                                                                       [/Users/kiyotatakeshi/Desktop/GitHub/git_practice]

kiyotatakeshi@localhost:(master+) $ git commit -m "add first.html"                                                                                 [/Users/kiyotatakeshi/Desktop/GitHub/git_practice]
[master 868138f] add first.html
 1 file changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 git_tutorial/first.html

kiyotatakeshi@localhost:(master) $ touch git_tutorial/second.html                                                                                  [/Users/kiyotatakeshi/Desktop/GitHub/git_practice]

kiyotatakeshi@localhost:(master) $ git add .                                                                                                       [/Users/kiyotatakeshi/Desktop/GitHub/git_practice]

kiyotatakeshi@localhost:(master+) $ git commit -m "add second.html"                                                                                [/Users/kiyotatakeshi/Desktop/GitHub/git_practice]
[master 4a9909f] add second.html
 1 file changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 git_tutorial/second.html

kiyotatakeshi@localhost:(master) $ touch git_tutorial/third.html                                                                                   [/Users/kiyotatakeshi/Desktop/GitHub/git_practice]

kiyotatakeshi@localhost:(master) $ git add .                                                                                                       [/Users/kiyotatakeshi/Desktop/GitHub/git_practice]

kiyotatakeshi@localhost:(master+) $ git commit -m "add third.html"                                                                                 [/Users/kiyotatakeshi/Desktop/GitHub/git_practice]
[master ee9dea2] add third.html
 1 file changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 git_tutorial/third.html

# 確認
kiyotatakeshi@localhost:(master) $ git log --oneline -n 3                                                                                          [/Users/kiyotatakeshi/Desktop/GitHub/git_practice]
ee9dea2 (HEAD -> master) add third.html
4a9909f add second.html
868138f add first.html

```

- 修正する

```
# -i は対話的(interactive)
# HEAD~1 は一番目の親を指定(HEADを起点とした数値分の親コミットを指定)
# HEAD~3 とすると、HEAD~3を起点としてそれ以降のコミットをrebaseする

kiyotatakeshi@localhost:(master) $ git rebase -i HEAD~3                                                                                            [/Users/kiyotatakeshi/Desktop/GitHub/git_practice]

# エディタで修正したいコミットを edit に変更する
  1 pick 868138f add first.html
  2 pick 4a9909f add second.html
  3 edit ee9dea2 add third.html

# エディタを閉じると次にするべきことが表示される

Stopped at ee9dea2...  add third.html
You can amend the commit now, with

  git commit --amend

Once you are satisfied with your changes, run

  git rebase --continue

```

- コミットメッセージを修正してコミットしなおす
    - ファイルを追加等する場合は、修正後してステージングに追加してコミット

```
kiyotatakeshi@localhost:(master(rebase-i)) $ git commit --amend                                                                                    [/Users/kiyotatakeshi/Desktop/GitHub/git_practice]
[detached HEAD a815246] third.html を追加
 Date: Sun Nov 10 19:49:46 2019 +0900
 1 file changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 git_tutorial/third.html

# 次の修正に移る(今回は pick にしているためこれで終わり)
kiyotatakeshi@localhost:(master(rebase-i)) $ git rebase --continue                                                                                 [/Users/kiyotatakeshi/Desktop/GitHub/git_practice]
Successfully rebased and updated refs/heads/master.

```

- 確認
    - コミットメッセージを変更できている

```
kiyotatakeshi@localhost:(master) $ git log --oneline -n 3                                                                                          [/Users/kiyotatakeshi/Desktop/GitHub/git_practice]
a815246 (HEAD -> master) third.html を追加
4a9909f add second.html
868138f add first.html

```

---
- コミットを並び替え、削除

```
# 順番を確認

kiyotatakeshi@localhost:(master) $ git log --oneline -n 3                                                                                          [/Users/kiyotatakeshi/Desktop/GitHub/git_practice]
a815246 (HEAD -> master) third.html を追加
4a9909f add second.html
868138f add first.html

# 並び替える
kiyotatakeshi@localhost:(master) $ git rebase -i HEAD~3                                                                                            [/Users/kiyotatakeshi/Desktop/GitHub/git_practice]

# rebaseを適用するコミット順に並んでいるため git log とは表示が逆

  1 pick 4a9909f add second.html
  2 pick 868138f add first.html
  3 pick a815246 third.html を追加

Successfully rebased and updated refs/heads/master.

# 確認
kiyotatakeshi@localhost:(master) $ git log --oneline -n 3                                                                                          [/Users/kiyotatakeshi/Desktop/GitHub/git_practice]
d9f3540 (HEAD -> master) third.html を追加
724dfb3 add first.html
190cbc0 add second.html

```

- 複数のコミットを束ねる

```
kiyotatakeshi@localhost:(master) $ git rebase -i HEAD~3                                                                                            [/Users/kiyotatakeshi/Desktop/GitHub/git_practice]

# 直前のコミットと一つにまとめるものを squash にする
# 以下のようにすると add second.thml にまとめられる
  1 pick 190cbc0 add second.html
  2 squash 724dfb3 add first.html
  3 squash d9f3540 third.html を追加

# このあとさらにコミットメッセージを編集するエディタが立ち上がる

[detached HEAD faefc39] add second.html
 Date: Sun Nov 10 19:49:26 2019 +0900
 3 files changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 git_tutorial/first.html
 create mode 100644 git_tutorial/second.html
 create mode 100644 git_tutorial/third.html
Successfully rebased and updated refs/heads/master.

```

- 確認

```
kiyotatakeshi@localhost:(master) $ git log --oneline -n 3                                                                                          [/Users/kiyotatakeshi/Desktop/GitHub/git_practice]

faefc39 (HEAD -> master) add second.html
c1b0a03 (origin/master) rebase_practice_feature.html を追加
5d4c4a9 rebase_practice を新規作成

commit faefc399da0cf8f3169cfbe5d65716b68545fef5 (HEAD -> master)
Author: kiyotakeshi <kiyotatakeshi0609@gmail.com>
Date:   Sun Nov 10 19:49:26 2019 +0900

    add second.html

    add first.html

    third.html を追加
```

- コミットを分割する

```
# 確認
kiyotatakeshi@localhost:(master) $ git log --oneline -n 3                                                                                          [/Users/kiyotatakeshi/Desktop/GitHub/git_practice]
faefc39 (HEAD -> master) add second.html
c1b0a03 (origin/master) rebase_practice_feature.html を追加
5d4c4a9 rebase_practice を新規作成

# 直前のコミット(分割したいコミット)を修正する
# pick を edit に変更
kiyotatakeshi@localhost:(master) $ git rebase -i HEAD~                                                                                             [/Users/kiyotatakeshi/Desktop/GitHub/git_practice]

Stopped at faefc39...  add second.html
You can amend the commit now, with

  git commit --amend

Once you are satisfied with your changes, run

  git rebase --continue

  1 edit faefc39 add second.html

```

- コミットを取り消して分割

```
# コミットを取り消し
$ git reset HEAD^

# 取り消しできている
$ git status

Untracked files:
  (use "git add <file>..." to include in what will be committed)

	git_tutorial/first.html
	git_tutorial/second.html
	git_tutorial/third.html

```

- 分割してコミット

```
$  git add git_tutorial/first.html git_tutorial/second.html                                             [/Users/kiyotatakeshi/Desktop/GitHub/git_practice]

kiyotatakeshi@localhost:(master(rebase-i)+) $ git commit -m "add first.html,second.html"                                                           [/Users/kiyotatakeshi/Desktop/GitHub/git_practice]

kiyotatakeshi@localhost:(master(rebase-i)) $ git add git_tutorial/third.html                                                                       [/Users/kiyotatakeshi/Desktop/GitHub/git_practice]

kiyotatakeshi@localhost:(master(rebase-i)+) $ git commit -m "add third.html"                                                                       [/Users/kiyotatakeshi/Desktop/GitHub/git_practice]

# 次の rebase に進む(完了)
kiyotatakeshi@localhost:(master(rebase-i)) $ git rebase --continue                                                                                 [/Users/kiyotatakeshi/Desktop/GitHub/git_practice]
Successfully rebased and updated refs/heads/master.

```

- 分割できていることを確認

```
kiyotatakeshi@localhost:(master) $ git log --oneline -n 3                                                                                          [/Users/kiyotatakeshi/Desktop/GitHub/git_practice]
2be9285 (HEAD -> master) add third.html
572a4d3 add first.html,second.html
c1b0a03 (origin/master) rebase_practice_feature.html を追加

```
