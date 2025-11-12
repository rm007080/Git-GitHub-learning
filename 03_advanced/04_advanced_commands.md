# 高度なGitコマンド

## 目次
1. [git stash](#git-stash)
2. [git cherry-pick](#git-cherry-pick)
3. [git reset](#git-reset)
4. [git reflog](#git-reflog)
5. [git bisect](#git-bisect)
6. [その他の便利なコマンド](#その他の便利なコマンド)

---

## git stash

### 概要

作業中の変更を一時的に退避させる機能です。

### 基本的な使い方

```bash
# 変更を退避
git stash

# または、メッセージ付きで退避
git stash save "WIP: working on feature X"

# 退避リストを表示
git stash list

# 最新の退避を復元
git stash pop

# 特定の退避を復元
git stash apply stash@{0}

# 退避を削除
git stash drop stash@{0}

# すべての退避を削除
git stash clear
```

### 実用例

#### シナリオ1: 緊急のバグ修正

```bash
# 機能開発中...
git status
# modified: feature.js

# 緊急バグ修正の依頼が来た
git stash save "WIP: feature development"

# mainに切り替えてバグ修正
git checkout main
git checkout -b hotfix/bug-123
# バグ修正...
git add .
git commit -m "fix: Fix critical bug"
git push origin hotfix/bug-123

# 元の作業に戻る
git checkout feature-branch
git stash pop
```

#### シナリオ2: ブランチを間違えた

```bash
# mainブランチで作業してしまった
git checkout main
# 作業...

# 間違いに気づいた
git stash
git checkout -b feature/correct-branch
git stash pop
```

### 高度な使い方

```bash
# 未追跡ファイルも含めて退避
git stash -u

# .gitignoreファイルも含めて退避
git stash -a

# 特定のファイルだけ退避
git stash push -m "message" path/to/file.txt

# 退避を確認
git stash show stash@{0}
git stash show -p stash@{0}  # 差分を表示

# 退避をブランチとして作成
git stash branch new-branch-name stash@{0}
```

---

## git cherry-pick

### 概要

特定のコミットだけを別のブランチに適用します。

### 基本的な使い方

```bash
# 特定のコミットを現在のブランチに適用
git cherry-pick <commit-hash>

# 複数のコミットを適用
git cherry-pick <commit1> <commit2>

# 範囲指定
git cherry-pick <commit1>..<commit2>
```

### 実用例

#### シナリオ1: バグ修正をmainとdevelopの両方に適用

```bash
# developでバグ修正
git checkout develop
git commit -m "fix: Fix important bug"
# コミットハッシュ: abc1234

# mainにも同じ修正を適用
git checkout main
git cherry-pick abc1234
```

#### シナリオ2: 特定の機能だけを先行リリース

```bash
# featureブランチに複数のコミット
feature: A---B---C---D---E

# Cだけをmainに適用
git checkout main
git cherry-pick C
```

### cherry-pickのオプション

```bash
# コミットせずに適用（確認後にコミット）
git cherry-pick --no-commit <hash>

# コンフリクトが起きたら
git cherry-pick --continue  # 解決後に続行
git cherry-pick --abort     # 中止

# コミットメッセージを編集
git cherry-pick -e <hash>
```

---

## git reset

### 概要

コミットを取り消す強力なコマンドです。3つのモードがあります。

### 3つのモード

#### 1. --soft（最も安全）

```bash
# コミットだけを取り消し、変更はステージングに残す
git reset --soft HEAD~1

Before:
HEAD: A---B---C
Working: clean

After:
HEAD: A---B
Working: Cの変更がステージングに残る
```

**使用例:**
```bash
# コミットメッセージを書き直したい
git reset --soft HEAD~1
git commit -m "Better commit message"
```

#### 2. --mixed（デフォルト）

```bash
# コミットとステージングを取り消し、変更はワーキングに残す
git reset --mixed HEAD~1
# または
git reset HEAD~1

Before:
HEAD: A---B---C
Staging: clean
Working: clean

After:
HEAD: A---B
Staging: clean
Working: Cの変更が残る
```

**使用例:**
```bash
# ステージングから特定のファイルを外す
git reset HEAD file.txt
```

#### 3. --hard（危険）

```bash
# すべてを取り消し、変更は完全に削除
git reset --hard HEAD~1

Before:
HEAD: A---B---C
Working: clean

After:
HEAD: A---B
Working: clean（Cの変更は完全に消える）
```

**警告**: 変更は復元できません（reflogを使えば復元可能な場合もある）

**使用例:**
```bash
# すべての変更を破棄してクリーンな状態に
git reset --hard HEAD
```

### 実用例

```bash
# 最新のコミットを取り消し
git reset --soft HEAD~1

# 最新の3つのコミットを取り消し
git reset --soft HEAD~3

# 特定のコミットまで戻る
git reset --hard abc1234

# ステージングをクリア（変更は保持）
git reset HEAD
```

---

## git reflog

### 概要

すべてのHEADの移動履歴を記録しています。**失われたコミットを復元**できます。

### 基本的な使い方

```bash
# reflogを表示
git reflog

# 出力例:
# abc1234 HEAD@{0}: commit: Add feature
# def5678 HEAD@{1}: checkout: moving from main to feature
# ghi9012 HEAD@{2}: reset: moving to HEAD~1
# jkl3456 HEAD@{3}: commit: Wrong commit
```

### 実用例

#### シナリオ1: reset --hardで消したコミットを復元

```bash
# 間違えて reset --hard してしまった
git reset --hard HEAD~3
# あれ、大事なコミットが消えた...

# reflogで確認
git reflog
# abc1234 HEAD@{0}: reset: moving to HEAD~3
# def5678 HEAD@{1}: commit: Important work  ← これを復元したい

# 復元
git reset --hard def5678
# またはgit reset --hard HEAD@{1}
```

#### シナリオ2: ブランチを削除してしまった

```bash
# ブランチを削除
git branch -D important-feature
# あれ、まだマージしてなかった...

# reflogで確認
git reflog
# abc1234 HEAD@{0}: checkout: moving from important-feature to main
# def5678 HEAD@{1}: commit (important-feature): Add important feature

# ブランチを復元
git branch important-feature def5678
```

### reflogのオプション

```bash
# 特定のブランチのreflog
git reflog show feature-branch

# 期限を指定
git reflog --since="1 week ago"

# 詳細表示
git reflog show --all
```

---

## git bisect

### 概要

バグが混入したコミットを二分探索で特定します。

### 基本的な使い方

```bash
# bisectを開始
git bisect start

# 現在のコミットはバグがある
git bisect bad

# 動作していたコミットを指定
git bisect good <commit-hash>

# Gitが自動的に中間のコミットをチェックアウト
# テストして結果を報告
git bisect good  # または git bisect bad

# 繰り返すと、バグのコミットが特定される
# 終了
git bisect reset
```

### 実用例

```bash
# 1. bisectを開始
git bisect start

# 2. 現在はバグがある
git bisect bad

# 3. v1.0.0は正常だった
git bisect good v1.0.0

# 4. Gitが中間のコミットをチェックアウト
# Bisecting: 50 revisions left to test after this

# 5. テストを実行
npm test

# 6. 結果を報告
git bisect good  # テストが通った
# または
git bisect bad   # テストが失敗した

# 7. 繰り返す
# Bisecting: 25 revisions left...
npm test
git bisect good

# ...繰り返し...

# 8. バグのコミットが特定された
# abc1234 is the first bad commit
# commit abc1234
# Author: Someone
# Date: ...
#     This commit introduced the bug

# 9. 終了
git bisect reset
```

### 自動化

```bash
# テストスクリプトで自動化
git bisect start
git bisect bad
git bisect good v1.0.0

# 自動実行
git bisect run npm test

# 終了（自動的に最初のbadコミットが特定される）
git bisect reset
```

---

## その他の便利なコマンド

### git blame - 各行の最終編集者を表示

```bash
# ファイルの各行を誰が編集したか表示
git blame file.txt

# 特定の行範囲
git blame -L 10,20 file.txt

# より詳細な情報
git blame -w file.txt  # 空白を無視
```

### git tag - タグの管理

```bash
# タグを作成
git tag v1.0.0

# 注釈付きタグ（推奨）
git tag -a v1.0.0 -m "Version 1.0.0"

# タグ一覧
git tag

# タグを削除
git tag -d v1.0.0

# リモートのタグを削除
git push origin --delete v1.0.0

# タグをプッシュ
git push origin v1.0.0
git push origin --tags  # すべてのタグ
```

### git clean - 未追跡ファイルを削除

```bash
# 何が削除されるか確認（dry-run）
git clean -n

# 未追跡ファイルを削除
git clean -f

# ディレクトリも削除
git clean -fd

# .gitignoreファイルも削除
git clean -fdx
```

### git archive - リポジトリをアーカイブ

```bash
# ZIPファイルを作成
git archive --format=zip HEAD > archive.zip

# 特定のブランチ
git archive --format=zip feature-branch > feature.zip

# 特定のディレクトリのみ
git archive --format=zip HEAD:src/ > src.zip
```

### git worktree - 複数の作業ツリー

```bash
# 別のディレクトリで同時に作業
git worktree add ../project-feature feature-branch

# worktree一覧
git worktree list

# worktreeを削除
git worktree remove ../project-feature
```

### git grep - リポジトリ内を検索

```bash
# パターンを検索
git grep "function"

# 特定のブランチ内を検索
git grep "function" feature-branch

# 行番号を表示
git grep -n "function"

# ファイル名のみ表示
git grep -l "function"
```

---

## チェックリスト

- [ ] `git stash`で作業を一時退避できる
- [ ] `git cherry-pick`で特定のコミットを適用できる
- [ ] `git reset`の3つのモードを理解している
- [ ] `git reflog`で失われたコミットを復元できる
- [ ] `git bisect`でバグの混入箇所を特定できる

---

## 参考資料

- [Pro Git - Git Tools](https://git-scm.com/book/ja/v2/Git-%E3%81%AE%E3%81%95%E3%81%BE%E3%81%96%E3%81%BE%E3%81%AA%E3%83%84%E3%83%BC%E3%83%AB)
- [Atlassian - Advanced Git Tutorials](https://www.atlassian.com/git/tutorials/advanced-overview)
