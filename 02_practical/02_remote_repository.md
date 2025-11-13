# リモートリポジトリの操作

## 目次
1. [リモートリポジトリとは](#リモートリポジトリとは)
2. [リモートの管理](#リモートの管理)
3. [Push - 変更をアップロード](#push---変更をアップロード)
4. [Pull - 変更をダウンロード](#pull---変更をダウンロード)
5. [Fetch - 変更を確認](#fetch---変更を確認)
6. [実践演習](#実践演習)

---

## リモートリポジトリとは

### ローカルとリモート

```
ローカルリポジトリ              リモートリポジトリ
（あなたのPC）                 （GitHub等）

┌──────────────┐              ┌──────────────┐
│   ワーキング  │              │              │
│  ディレクトリ │              │   リモート   │
├──────────────┤              │  リポジトリ  │
│ ステージング  │              │              │
├──────────────┤    push →   │   origin    │
│  ローカル     │   ← pull    │              │
│ リポジトリ    │              │              │
│   (.git)     │              │              │
└──────────────┘              └──────────────┘
```

### リモートリポジトリの役割

1. **バックアップ**
   - PCが壊れてもコードが失われない

2. **共同作業**
   - チームメンバーとコードを共有

3. **コードの公開**
   - オープンソースプロジェクトの公開

4. **どこからでもアクセス**
   - 別のPCからもアクセス可能

---

## リモートの管理

### git remote - リモートリポジトリの管理

```bash
# リモートリポジトリの一覧を表示
git remote

# 詳細情報を表示（URLも表示）
git remote -v
```

**出力例：**
```
origin  git@github.com:username/repo.git (fetch)
origin  git@github.com:username/repo.git (push)
```

### リモートの追加

```bash
# リモートリポジトリを追加
git remote add <name> <url>

# 例: originという名前でGitHubのリポジトリを追加
git remote add origin https://github.com/username/repo.git
```

**慣例：**
- `origin`: メインのリモートリポジトリ（デフォルト）
- `upstream`: フォーク元のリポジトリ（フォークの場合）

### リモートの削除

```bash
# リモートリポジトリを削除
git remote remove <name>

# 例
git remote remove origin
```

### リモートURLの変更

```bash
# リモートURLを変更
git remote set-url <name> <new-url>

# HTTPSからSSHに変更する例
git remote set-url origin git@github.com:username/repo.git
```

### リモート名の変更

```bash
# リモート名を変更
git remote rename <old-name> <new-name>

# 例: originをmainに変更
git remote rename origin main
```

### リモートの詳細情報を表示

```bash
# リモートの詳細情報を表示
git remote show origin
```

---

## Push - 変更をアップロード

### git push - ローカルの変更をリモートに送信

```bash
# 基本的なpush
git push <remote> <branch>

# 例: originリモートのmainブランチにpush
git push origin main
```

### 初回のpush

初めてpushする場合は`-u`オプションを使用します。

```bash
# 上流ブランチを設定してpush
git push -u origin main
```

`-u`（または`--set-upstream`）オプションを使うと：
- ローカルブランチとリモートブランチが紐付けられる
- 次回以降は`git push`だけでpushできる

```bash
# 2回目以降は以下だけでOK
git push
```

### すべてのブランチをpush

```bash
# すべてのブランチをpush
git push --all origin
```

### タグをpush

```bash
# 特定のタグをpush
git push origin v1.0.0

# すべてのタグをpush
git push --tags
```

### 強制push（注意が必要）

```bash
# 強制push（リモートの履歴を上書き）
git push -f origin main
# または
git push --force origin main

# より安全な強制push（他の人の変更がある場合は失敗）
git push --force-with-lease origin main
```

**警告**:
- `--force`は危険です。他の人の変更を消してしまう可能性があります
- チーム開発では`--force-with-lease`を使用してください
- mainブランチへの強制pushは避けてください

---

## Pull - 変更をダウンロード

### git pull - リモートの変更をローカルに取り込む

`git pull`は以下の2つのコマンドを実行します：

```bash
git fetch    # リモートの変更を取得
+
git merge    # ローカルブランチにマージ
```

### 基本的な使い方

```bash
# 基本的なpull
git pull <remote> <branch>

# 例
git pull origin main

# 上流ブランチが設定されている場合
git pull
```

### pullの挙動を設定

```bash
# マージ戦略（デフォルト）
git pull

# リベース戦略
git pull --rebase

# デフォルトの挙動を設定
git config pull.rebase false  # マージ（デフォルト）
git config pull.rebase true   # リベース
git config pull.ff only       # Fast-forwardのみ
```

### pullの種類

#### 1. Fast-forward merge（早送りマージ）

リモートが進んでいて、ローカルに新しいコミットがない場合：

```
Before:
main: A---B---C (local)
origin/main: A---B---C---D---E (remote)

After pull:
main: A---B---C---D---E (local)
origin/main: A---B---C---D---E (remote)
```

#### 2. Merge commit（マージコミット）

両方に新しいコミットがある場合：

```
Before:
main: A---B---C---F (local)
origin/main: A---B---C---D---E (remote)

After pull:
main: A---B---C---F---G (local, Gはマージコミット)
              \     /
               D---E
origin/main: A---B---C---D---E (remote)
```

#### 3. Rebase

リモートの変更の上に、ローカルの変更を再適用：

```
Before:
main: A---B---C---F (local)
origin/main: A---B---C---D---E (remote)

After pull --rebase:
main: A---B---C---D---E---F' (local, F'は再適用されたコミット)
origin/main: A---B---C---D---E (remote)
```

---

## Fetch - 変更を確認

### git fetch - リモートの情報を取得（マージしない）

`git fetch`は、リモートの変更を取得するだけで、ローカルブランチには影響しません。

```bash
# すべてのリモートから取得
git fetch --all

# 特定のリモートから取得
git fetch origin

# 特定のブランチを取得
git fetch origin main
```

### fetchとpullの違い

```bash
# fetch: 取得のみ（安全）
git fetch origin
git log origin/main  # リモートの変更を確認
git merge origin/main  # 手動でマージ

# pull: 取得 + マージ（便利だが注意が必要）
git pull origin main
```

### fetchの活用

```bash
# 1. リモートの変更を取得
git fetch origin

# 2. 変更内容を確認
git log HEAD..origin/main

# 3. 差分を確認
git diff HEAD origin/main

# 4. 問題なければマージ
git merge origin/main
```

---

## clone - リポジトリを複製

### git clone - リモートリポジトリをローカルにコピー

```bash
# 基本的なclone
git clone <url>

# 例
git clone https://github.com/username/repo.git

# ディレクトリ名を指定
git clone https://github.com/username/repo.git my-project

# 特定のブランチをclone
git clone -b develop https://github.com/username/repo.git

# 浅いclone（履歴を制限、高速）
git clone --depth 1 https://github.com/username/repo.git
```

### cloneで起こること

1. リモートリポジトリの完全なコピーを作成
2. `origin`という名前でリモートが自動設定される
3. デフォルトブランチがチェックアウトされる

```bash
# cloneすると自動的に設定される
git remote -v
# origin  https://github.com/username/repo.git (fetch)
# origin  https://github.com/username/repo.git (push)
```

---

## 実践演習

### 演習1：基本的なワークフロー

```bash
# 1. リポジトリをclone
git clone git@github.com:username/practice-repo.git
cd practice-repo

# 2. ファイルを編集
echo "New content" >> file.txt

# 3. 変更をコミット
git add file.txt
git commit -m "Add new content"

# 4. リモートにpush
git push origin main

# 5. 他の場所からpull（別のディレクトリで実行）
cd ../practice-repo-2
git pull origin main
```

### 演習2：fetch, diff, merge

```bash
# 1. リモートの変更を取得（マージしない）
git fetch origin

# 2. ローカルとリモートの差分を確認
git diff main origin/main

# 3. ログを確認
git log main..origin/main

# 4. 問題なければマージ
git merge origin/main
```

### 演習3：複数のリモート

```bash
# 1. 現在のリモートを確認
git remote -v

# 2. 別のリモートを追加（例: フォーク元）
git remote add upstream https://github.com/original/repo.git

# 3. upstreamから変更を取得
git fetch upstream

# 4. upstreamの変更をローカルにマージ
git merge upstream/main

# 5. 自分のリモートにpush
git push origin main
```

---

## よくあるシナリオ

### シナリオ1：pushが拒否される

```bash
$ git push origin main
! [rejected]        main -> main (fetch first)
error: failed to push some refs to 'origin'
```

**原因**: リモートに新しいコミットがある

**解決方法**:
```bash
# 方法1: pull してからpush
git pull origin main
git push origin main

# 方法2: fetch, merge, push
git fetch origin
git merge origin/main
git push origin main

# 方法3: rebase してからpush（履歴をきれいに保つ）
git pull --rebase origin main
git push origin main
```

### シナリオ2：間違えてpushしてしまった

```bash
# 直前のコミットを修正
git commit --amend

# 強制push（注意：他の人が作業していない場合のみ）
git push --force-with-lease origin main
```

### シナリオ3：リモートブランチを削除

```bash
# リモートブランチを削除
git push origin --delete branch-name
```

### シナリオ4：ローカルをリモートに合わせる

```bash
# ローカルの変更を破棄してリモートに合わせる
git fetch origin
git reset --hard origin/main
```

**警告**: `reset --hard`はローカルの変更をすべて破棄します

---

## トラブルシューティング

### Q: "Permission denied" エラー

```bash
Permission denied (publickey).
```

**解決方法**:
1. SSH鍵が正しく設定されているか確認
2. SSH接続をテスト: `ssh -T git@github.com`
3. リモートURLがSSH形式か確認: `git remote -v`

### Q: "fatal: refusing to merge unrelated histories"

```bash
fatal: refusing to merge unrelated histories
```

**解決方法**:
```bash
# 無関係な履歴を強制的にマージ
git pull origin main --allow-unrelated-histories
```

### Q: pushが遅い

大きなファイルがある場合：
```bash
# 浅いpush（最新のコミットのみ）
git push --depth=1
```

Git LFSの使用を検討:
```bash
# Git LFSをインストール
git lfs install
git lfs track "*.psd"
```

---

## ベストプラクティス

### 1. こまめにpull

```bash
# 作業前に必ずpull
git pull origin main
# 作業...
git push origin main
```

### 2. pushする前にローカルでテスト

```bash
# テストを実行してからpush
npm test
git push origin main
```

### 3. わかりやすいコミットメッセージ

```bash
# 良い例
git commit -m "feat: ユーザー認証機能を追加"
git commit -m "fix: ログイン時のバリデーションを修正"
git commit -m "docs: READMEにインストール手順を追加"

# 悪い例
git commit -m "update"
git commit -m "fix"
```

### 4. 機能ごとにブランチを分ける

```bash
# 新機能用のブランチを作成
git checkout -b feature/user-auth
# 作業...
git push origin feature/user-auth
```

---

## チェックリスト

- [ ] `git remote`でリモートを管理できる
- [ ] `git push`でローカルの変更をリモートに送信できる
- [ ] `git pull`でリモートの変更をローカルに取り込める
- [ ] `git fetch`でリモートの情報を取得できる
- [ ] `git clone`でリポジトリを複製できる
- [ ] pushが拒否された時の対処法を理解している

---

## 次のステップ

リモートリポジトリの操作をマスターしたら、ブランチを使った開発を学びましょう！

👉 [03. ブランチとマージ](./03_branches_and_merge.md)

---

## 参考資料

- [Pro Git - リモートでの作業](https://git-scm.com/book/ja/v2/Git-%E3%81%AE%E5%9F%BA%E6%9C%AC-%E3%83%AA%E3%83%A2%E3%83%BC%E3%83%88%E3%81%A7%E3%81%AE%E4%BD%9C%E6%A5%AD)
- [GitHub Docs - リポジトリの管理](https://docs.github.com/ja/repositories)
