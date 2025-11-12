# Git/GitHub チートシート

## 初期設定

```bash
# ユーザー情報の設定
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"

# デフォルトブランチ名を設定
git config --global init.defaultBranch main

# エディタの設定
git config --global core.editor "code --wait"

# 設定確認
git config --list
```

---

## リポジトリの作成・クローン

```bash
# 新しいリポジトリを作成
git init

# 既存のリポジトリをクローン
git clone <url>
git clone <url> <directory-name>
```

---

## 基本的な変更の記録

```bash
# 状態を確認
git status

# ファイルをステージング
git add <file>
git add .                # すべてのファイル
git add *.js             # パターンマッチ

# コミット
git commit -m "message"
git commit -am "message" # add + commit（追跡済みファイルのみ）

# 最後のコミットを修正
git commit --amend
git commit --amend -m "new message"
```

---

## 履歴の確認

```bash
# コミット履歴
git log
git log --oneline
git log --graph --oneline --all
git log -n 5                    # 最新5件
git log --since="2024-01-01"
git log --author="Your Name"

# 差分を確認
git diff                 # ワーキング vs ステージング
git diff --staged        # ステージング vs HEAD
git diff <commit>        # HEADと特定のコミット
git diff <commit1> <commit2>

# 特定のコミットを表示
git show <commit>
```

---

## ブランチ操作

```bash
# ブランチ一覧
git branch                # ローカルブランチ
git branch -a             # すべてのブランチ
git branch -r             # リモートブランチ

# ブランチ作成
git branch <branch-name>

# ブランチ切り替え
git checkout <branch-name>
git switch <branch-name>        # Git 2.23+

# 作成 + 切り替え
git checkout -b <branch-name>
git switch -c <branch-name>     # Git 2.23+

# ブランチ削除
git branch -d <branch-name>     # マージ済みのみ
git branch -D <branch-name>     # 強制削除

# ブランチ名変更
git branch -m <old-name> <new-name>
```

---

## マージ

```bash
# ブランチをマージ
git merge <branch-name>

# マージコミットを必ず作成
git merge --no-ff <branch-name>

# Squashマージ
git merge --squash <branch-name>

# マージを中止
git merge --abort
```

---

## リモート操作

```bash
# リモートを追加
git remote add origin <url>

# リモート一覧
git remote -v

# リモートURLを変更
git remote set-url origin <new-url>

# プッシュ
git push origin <branch-name>
git push -u origin <branch-name>  # 上流を設定
git push --all origin              # すべてのブランチ

# プル
git pull origin <branch-name>
git pull --rebase origin <branch-name>

# フェッチ
git fetch origin
git fetch --all
```

---

## Rebase

```bash
# 現在のブランチをrebase
git rebase <branch-name>

# Interactive rebase
git rebase -i HEAD~3
git rebase -i <commit>

# Rebaseを続行/中止
git rebase --continue
git rebase --abort
git rebase --skip
```

---

## 変更の取り消し

```bash
# ワーキングディレクトリの変更を取り消し
git restore <file>
git checkout -- <file>      # 古い方法

# ステージングを取り消し
git restore --staged <file>
git reset HEAD <file>       # 古い方法

# コミットを取り消し
git reset --soft HEAD~1     # コミットのみ取り消し
git reset --mixed HEAD~1    # コミット+ステージング取り消し
git reset --hard HEAD~1     # すべて取り消し（危険）

# 特定のコミットに戻る
git reset --hard <commit>

# 作業ディレクトリをクリーン
git clean -f                # 未追跡ファイルを削除
git clean -fd               # ディレクトリも削除
```

---

## Stash

```bash
# 変更を退避
git stash
git stash save "message"

# 退避リスト
git stash list

# 退避を復元
git stash pop               # 最新を復元して削除
git stash apply stash@{0}   # 復元のみ

# 退避を削除
git stash drop stash@{0}
git stash clear             # すべて削除

# 未追跡ファイルも退避
git stash -u
```

---

## Cherry-pick

```bash
# 特定のコミットを適用
git cherry-pick <commit>

# 複数のコミットを適用
git cherry-pick <commit1> <commit2>

# コミットせずに適用
git cherry-pick --no-commit <commit>

# Cherry-pickを中止
git cherry-pick --abort
```

---

## タグ

```bash
# タグ作成
git tag v1.0.0
git tag -a v1.0.0 -m "Version 1.0.0"  # 注釈付き

# タグ一覧
git tag

# タグを削除
git tag -d v1.0.0
git push origin --delete v1.0.0

# タグをプッシュ
git push origin v1.0.0
git push origin --tags      # すべてのタグ
```

---

## 情報の確認

```bash
# 各行の最終編集者
git blame <file>

# リポジトリ内を検索
git grep "pattern"

# ファイルの履歴
git log -- <file>

# Reflog（HEADの履歴）
git reflog
```

---

## .gitignore

```gitignore
# コメント

# 特定のファイル
secret.txt

# 特定の拡張子
*.log
*.tmp

# 特定のディレクトリ
node_modules/
dist/

# パターン
**/temp/
*.cache

# 例外（無視しない）
!important.log
```

---

## よく使うエイリアス

```bash
# エイリアス設定
git config --global alias.st status
git config --global alias.co checkout
git config --global alias.br branch
git config --global alias.cm commit
git config --global alias.lg "log --graph --oneline --decorate --all"
git config --global alias.unstage "reset HEAD --"
git config --global alias.last "log -1 HEAD"

# 使用例
git st                   # git status
git co main              # git checkout main
git lg                   # きれいなログ表示
```

---

## GitHub特有の操作

```bash
# SSHキー生成
ssh-keygen -t ed25519 -C "your.email@example.com"

# SSH接続テスト
ssh -T git@github.com

# HTTPSからSSHに変更
git remote set-url origin git@github.com:username/repo.git
```

---

## 緊急時のコマンド

```bash
# 間違えてハードリセットした
git reflog
git reset --hard HEAD@{1}

# マージを取り消したい
git reset --hard ORIG_HEAD

# 公開済みコミットを取り消したい（新しいコミットで打ち消す）
git revert <commit>

# すべてをリモートに合わせる
git fetch origin
git reset --hard origin/main

# ローカルの変更を一時的に退避
git stash
```

---

## トラブルシューティング

```bash
# プッシュが拒否される
git pull --rebase origin main
git push origin main

# コンフリクトを解決
# 1. ファイルを編集
# 2. git add <file>
# 3. git commit または git rebase --continue

# ブランチが追跡されていない
git branch --set-upstream-to=origin/main main

# 古いリモートブランチ情報をクリーンアップ
git fetch --prune

# 巨大なファイルを間違えてコミットした
git rm --cached large-file.zip
echo "large-file.zip" >> .gitignore
git commit --amend
```

---

## パフォーマンス最適化

```bash
# リポジトリの最適化
git gc

# リポジトリのサイズを確認
git count-objects -vH

# 浅いクローン（高速）
git clone --depth 1 <url>

# 特定のブランチのみクローン
git clone -b <branch-name> --single-branch <url>
```

---

## 便利なGitHub CLI（gh）コマンド

```bash
# PRを作成
gh pr create

# PR一覧
gh pr list

# PRをチェックアウト
gh pr checkout <pr-number>

# Issue作成
gh issue create

# リポジトリを開く
gh repo view --web
```

---

## GitFlowクイックリファレンス

```bash
# GitFlowを初期化
git flow init

# 機能開発
git flow feature start <name>
git flow feature finish <name>

# リリース
git flow release start <version>
git flow release finish <version>

# Hotfix
git flow hotfix start <version>
git flow hotfix finish <version>
```

---

## コミットメッセージの規約

```
<type>: <subject>

<body>

<footer>
```

**Type:**
- `feat`: 新機能
- `fix`: バグ修正
- `docs`: ドキュメント
- `style`: フォーマット
- `refactor`: リファクタリング
- `test`: テスト
- `chore`: 雑務

**例:**
```
feat: Add user authentication

Implement JWT-based authentication system
- Add login endpoint
- Add logout endpoint
- Add token validation middleware

Closes #123
```

---

## 覚えておくべきショートカット

```bash
# 直前のブランチに戻る
git checkout -

# HEADの別名
HEAD      # 現在のコミット
HEAD~1    # 1つ前のコミット
HEAD~3    # 3つ前のコミット
HEAD^     # 1つ前のコミット（マージコミットの場合は最初の親）

# 範囲指定
<commit1>..<commit2>   # commit1からcommit2まで
<commit1>...<commit2>  # commit1とcommit2の対称差分
```

---

このチートシートを印刷または PDF として保存しておくと便利です！
