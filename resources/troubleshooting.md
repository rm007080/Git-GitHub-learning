# トラブルシューティング

## 目次
1. [インストール・設定の問題](#インストール設定の問題)
2. [リモートリポジトリの問題](#リモートリポジトリの問題)
3. [ブランチ・マージの問題](#ブランチマージの問題)
4. [コミットの問題](#コミットの問題)
5. [パフォーマンスの問題](#パフォーマンスの問題)
6. [その他の一般的な問題](#その他の一般的な問題)

---

## インストール・設定の問題

### Q: `git`コマンドが見つからない

```bash
bash: git: command not found
```

**解決方法:**

```bash
# macOS
brew install git

# Ubuntu/Debian
sudo apt-get update
sudo apt-get install git

# Windows
# Git for Windows をインストール: https://git-scm.com/download/win

# インストール確認
git --version
```

### Q: ユーザー名とメールアドレスが設定されていない

```bash
*** Please tell me who you are.
```

**解決方法:**

```bash
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"

# 確認
git config user.name
git config user.email
```

### Q: 改行コードの警告が出る

```bash
warning: LF will be replaced by CRLF
```

**解決方法:**

```bash
# Windows
git config --global core.autocrlf true

# macOS/Linux
git config --global core.autocrlf input

# 警告を無視（非推奨）
git config --global core.autocrlf false
```

---

## リモートリポジトリの問題

### Q: pushが拒否される

```bash
! [rejected] main -> main (fetch first)
error: failed to push some refs to 'origin'
```

**原因:** リモートに新しいコミットがある

**解決方法:**

```bash
# オプション1: pullしてからpush
git pull origin main
git push origin main

# オプション2: rebaseしてからpush
git pull --rebase origin main
git push origin main

# オプション3: 強制push（注意: チームメンバーに確認）
git push --force-with-lease origin main
```

### Q: 認証エラー

```bash
remote: Support for password authentication was removed...
fatal: Authentication failed
```

**原因:** GitHubはパスワード認証を廃止

**解決方法:**

```bash
# オプション1: SSH認証（推奨）
ssh-keygen -t ed25519 -C "your.email@example.com"
# 公開鍵をGitHubに登録
# URLをSSHに変更
git remote set-url origin git@github.com:username/repo.git

# オプション2: Personal Access Token
# GitHubで生成: Settings → Developer settings → Personal access tokens
# HTTPSのURLでトークンをパスワードとして使用
```

### Q: SSH接続ができない

```bash
Permission denied (publickey).
```

**解決方法:**

```bash
# 1. SSH鍵が作成されているか確認
ls -la ~/.ssh/id_ed25519.pub

# 2. SSH Agentが起動しているか確認
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_ed25519

# 3. GitHubに公開鍵が登録されているか確認
# https://github.com/settings/keys

# 4. 接続テスト
ssh -T git@github.com

# 5. デバッグ
ssh -vT git@github.com
```

### Q: リモートが見つからない

```bash
fatal: 'origin' does not appear to be a git repository
```

**解決方法:**

```bash
# リモートを確認
git remote -v

# リモートがない場合は追加
git remote add origin <url>

# リモートURLが間違っている場合は変更
git remote set-url origin <correct-url>
```

---

## ブランチ・マージの問題

### Q: マージでコンフリクトが発生

```bash
CONFLICT (content): Merge conflict in file.txt
Automatic merge failed; fix conflicts and then commit the result.
```

**解決方法:**

```bash
# 1. コンフリクトファイルを確認
git status

# 2. ファイルを編集してコンフリクトマーカーを削除
# <<<<<<< HEAD
# =======
# >>>>>>> branch

# 3. 解決済みとしてマーク
git add <file>

# 4. コミット
git commit

# マージを中止したい場合
git merge --abort
```

### Q: ブランチが削除できない

```bash
error: The branch 'feature' is not fully merged.
```

**解決方法:**

```bash
# マージされていないブランチを強制削除
git branch -D feature

# または、先にマージしてから削除
git checkout main
git merge feature
git branch -d feature
```

### Q: ブランチが追跡されていない

```bash
There is no tracking information for the current branch.
```

**解決方法:**

```bash
# 上流ブランチを設定
git branch --set-upstream-to=origin/main main

# または、pushで設定
git push -u origin main
```

---

## コミットの問題

### Q: 間違えてコミットした

**解決方法:**

```bash
# 最新のコミットを取り消し（変更は保持）
git reset --soft HEAD~1

# コミットメッセージを修正
git commit --amend -m "Correct message"

# ファイルを追加し忘れた
git add forgotten-file.txt
git commit --amend --no-edit
```

### Q: 間違えて大きなファイルをコミットした

**解決方法:**

```bash
# まだプッシュしていない場合
git reset --soft HEAD~1
git rm --cached large-file.zip
echo "large-file.zip" >> .gitignore
git add .
git commit -m "Remove large file"

# すでにプッシュした場合（履歴から完全に削除）
git filter-branch --tree-filter 'rm -f large-file.zip' HEAD
# または BFG Repo-Cleaner を使用
```

### Q: コミットが見つからない（消えた）

**解決方法:**

```bash
# reflogで履歴を確認
git reflog

# 消えたコミットを復元
git checkout <commit-hash>
git branch recovery-branch
```

### Q: `.git`ディレクトリを削除してしまった

**解決方法:**

残念ながら、`.git`ディレクトリを削除するとすべての履歴が失われます。

```bash
# リモートからクローンし直す（リモートがある場合）
git clone <url>

# バックアップから復元

# 今後のために:
# - 定期的にリモートにプッシュ
# - バックアップを取る
```

---

## パフォーマンスの問題

### Q: Git操作が遅い

**解決方法:**

```bash
# リポジトリの最適化
git gc --aggressive

# 古いオブジェクトを削除
git prune

# インデックスを再構築
git update-index --really-refresh
```

### Q: クローンが遅い

**解決方法:**

```bash
# 浅いクローン（履歴を制限）
git clone --depth 1 <url>

# 特定のブランチのみ
git clone -b main --single-branch <url>

# 後で履歴を取得
git fetch --unshallow
```

### Q: リポジトリサイズが大きすぎる

**解決方法:**

```bash
# サイズを確認
git count-objects -vH

# 大きなファイルを特定
git rev-list --objects --all | \
  git cat-file --batch-check='%(objecttype) %(objectname) %(objectsize) %(rest)' | \
  sed -n 's/^blob //p' | \
  sort --numeric-sort --key=2 | \
  tail -n 10

# Git LFSの使用を検討
git lfs install
git lfs track "*.psd"
```

---

## その他の一般的な問題

### Q: `detached HEAD`状態になった

```bash
You are in 'detached HEAD' state.
```

**解決方法:**

```bash
# 変更を保存したい場合
git branch temp-branch
git checkout main
git merge temp-branch

# 変更を破棄したい場合
git checkout main
```

### Q: ファイルが無視されない

**解決方法:**

```bash
# キャッシュをクリア
git rm -r --cached .
git add .
git commit -m "Fix gitignore"

# 特定のファイル
git rm --cached file.txt
echo "file.txt" >> .gitignore
git add .gitignore
git commit -m "Ignore file.txt"
```

### Q: 日本語ファイル名が文字化けする

```bash
"\343\203\206\343\202\271\343\203\210.txt"
```

**解決方法:**

```bash
git config --global core.quotepath false
```

### Q: 間違えたブランチで作業してしまった

**解決方法:**

```bash
# 変更を退避
git stash

# 正しいブランチに切り替え
git checkout correct-branch

# 変更を復元
git stash pop
```

### Q: プルリクエストが古くなった

**解決方法:**

```bash
# オプション1: mainの変更をマージ
git checkout feature-branch
git merge main
git push origin feature-branch

# オプション2: rebase（きれいな履歴）
git checkout feature-branch
git rebase main
git push --force-with-lease origin feature-branch
```

### Q: コミットにシークレットを含めてしまった

**緊急対応:**

```bash
# 1. まだプッシュしていない場合
git reset --soft HEAD~1
# ファイルを編集してシークレットを削除
git add .
git commit -m "Remove secrets"

# 2. すでにプッシュした場合
# - すぐにシークレットを無効化（最優先）
# - GitHubのサポートに連絡
# - 履歴から削除（BFG Repo-Cleaner等）

# 3. 予防策
echo ".env" >> .gitignore
git secrets --install  # git-secrets ツールを使用
```

### Q: `fatal: refusing to merge unrelated histories`

**解決方法:**

```bash
# 無関係な履歴を強制的にマージ
git pull origin main --allow-unrelated-histories
```

### Q: ファイルのパーミッションが変更される

**解決方法:**

```bash
# パーミッション変更を無視
git config core.fileMode false
```

---

## デバッグ方法

### 一般的なデバッグステップ

```bash
# 1. 状態を確認
git status

# 2. ログを確認
git log --oneline -n 10

# 3. reflogを確認
git reflog

# 4. リモートを確認
git remote -v

# 5. 設定を確認
git config --list

# 6. 詳細ログを有効化
GIT_TRACE=1 git <command>
```

### 助けを求める

```bash
# Gitのヘルプ
git help <command>
git <command> --help

# 例
git help merge
git rebase --help
```

---

## 予防策

### ベストプラクティス

1. **定期的にプッシュ**
   ```bash
   # 1日の終わりに必ずプッシュ
   git push origin feature-branch
   ```

2. **こまめにコミット**
   ```bash
   # 小さな変更単位でコミット
   git add .
   git commit -m "Small change"
   ```

3. **ブランチを使う**
   ```bash
   # main で直接作業しない
   git checkout -b feature/new-feature
   ```

4. **危険なコマンドの前に確認**
   ```bash
   # reset --hard の前に
   git stash
   # または
   git branch backup
   ```

5. **`.gitignore`を活用**
   ```bash
   # プロジェクト開始時に設定
   echo "node_modules/" >> .gitignore
   echo ".env" >> .gitignore
   ```

---

## さらにサポートが必要な場合

- [GitHub Community](https://github.community/)
- [Stack Overflow](https://stackoverflow.com/questions/tagged/git)
- [Pro Git Book](https://git-scm.com/book/ja/v2)
- [Git公式ドキュメント](https://git-scm.com/docs)

---

それでも解決しない場合は、エラーメッセージをそのまま検索エンジンで検索すると、多くの場合解決策が見つかります！
