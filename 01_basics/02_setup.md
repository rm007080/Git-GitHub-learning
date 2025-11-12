# 環境設定

## 学習時間の目安：20分

## 目次
1. [Gitのインストール](#gitのインストール)
2. [初期設定](#初期設定)
3. [エディタの設定](#エディタの設定)
4. [設定の確認](#設定の確認)
5. [演習](#演習)

---

## Gitのインストール

### Windows

#### 方法1：公式インストーラー（推奨）

1. [Git for Windows](https://git-scm.com/download/win)からインストーラーをダウンロード
2. インストーラーを実行
3. 基本的にデフォルト設定でOK（以下のオプションは確認推奨）
   - **エディタの選択**: Visual Studio Codeを推奨（使っているエディタを選択）
   - **PATH環境変数**: "Git from the command line and also from 3rd-party software"を選択
   - **改行コードの扱い**: "Checkout Windows-style, commit Unix-style"を推奨

#### 方法2：Chocolatey（パッケージマネージャー）

```bash
choco install git
```

### macOS

#### 方法1：Homebrew（推奨）

```bash
# Homebrewがない場合は先にインストール
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"

# Gitをインストール
brew install git
```

#### 方法2：Xcodeコマンドラインツール

```bash
xcode-select --install
```

#### 方法3：公式インストーラー

[Git for macOS](https://git-scm.com/download/mac)からダウンロード

### Linux

#### Ubuntu/Debian

```bash
sudo apt update
sudo apt install git
```

#### Fedora

```bash
sudo dnf install git
```

#### CentOS/RHEL

```bash
sudo yum install git
```

### インストールの確認

```bash
git --version
```

以下のような出力が表示されればOKです：
```
git version 2.40.0
```

---

## 初期設定

Gitを使い始める前に、ユーザー情報を設定する必要があります。これはすべてのコミットに記録されます。

### ユーザー名とメールアドレスの設定

```bash
# ユーザー名を設定
git config --global user.name "あなたの名前"

# メールアドレスを設定
git config --global user.email "your.email@example.com"
```

**重要なポイント：**
- `--global`オプションをつけると、システム全体で使用される設定になります
- GitHubを使う場合、メールアドレスはGitHubアカウントと同じものを推奨
- 日本語の名前も使用可能ですが、英語表記が一般的

**例：**
```bash
git config --global user.name "Tanaka Taro"
git config --global user.email "tanaka@example.com"
```

### デフォルトブランチ名の設定

最近のGitでは、デフォルトブランチ名を`main`にすることが推奨されています。

```bash
git config --global init.defaultBranch main
```

### 日本語ファイル名の文字化け対策

日本語のファイル名が文字化けしないように設定します。

```bash
git config --global core.quotepath false
```

### 改行コードの設定

#### Windows

```bash
git config --global core.autocrlf true
```

#### macOS/Linux

```bash
git config --global core.autocrlf input
```

**改行コードについて：**
- Windows: CRLF (`\r\n`)
- macOS/Linux: LF (`\n`)
- この設定により、リポジトリ内は常にLFで統一されます

---

## エディタの設定

Gitが使用するデフォルトエディタを設定します。コミットメッセージの編集などに使用されます。

### Visual Studio Code

```bash
git config --global core.editor "code --wait"
```

### Vim

```bash
git config --global core.editor "vim"
```

### nano

```bash
git config --global core.editor "nano"
```

### Emacs

```bash
git config --global core.editor "emacs"
```

---

## 設定の確認

### すべての設定を表示

```bash
git config --list
```

### 特定の設定を確認

```bash
# ユーザー名を確認
git config user.name

# メールアドレスを確認
git config user.email

# エディタを確認
git config core.editor
```

### 設定ファイルの場所

Gitの設定は以下のファイルに保存されます：

1. **システム全体**: `/etc/gitconfig`
   - `git config --system`で設定

2. **ユーザー全体**: `~/.gitconfig`または`~/.config/git/config`
   - `git config --global`で設定

3. **リポジトリ固有**: `.git/config`
   - `git config --local`で設定（デフォルト）

**優先順位**: リポジトリ固有 > ユーザー全体 > システム全体

### 設定ファイルを直接編集

```bash
# グローバル設定ファイルを開く
git config --global --edit
```

---

## 便利な追加設定

### カラー表示を有効化

```bash
git config --global color.ui auto
```

### エイリアス（ショートカット）の設定

よく使うコマンドを短縮できます：

```bash
# git status を git st で実行
git config --global alias.st status

# git commit を git cm で実行
git config --global alias.cm commit

# git checkout を git co で実行
git config --global alias.co checkout

# git branch を git br で実行
git config --global alias.br branch

# きれいなログ表示
git config --global alias.lg "log --graph --oneline --decorate --all"
```

使用例：
```bash
git st        # git status と同じ
git cm -m "メッセージ"  # git commit -m "メッセージ" と同じ
git lg        # きれいなグラフ表示
```

### プルのデフォルト動作を設定

```bash
git config --global pull.rebase false
```

---

## 演習

以下の設定を実際に行ってみましょう：

### ステップ1：基本設定

```bash
# 1. ユーザー名を設定
git config --global user.name "Your Name"

# 2. メールアドレスを設定
git config --global user.email "your.email@example.com"

# 3. デフォルトブランチ名を設定
git config --global init.defaultBranch main
```

### ステップ2：設定の確認

```bash
# すべての設定を表示
git config --list

# 特定の設定を確認
git config user.name
git config user.email
```

### ステップ3：エイリアスの設定（オプション）

```bash
# よく使うコマンドのエイリアスを設定
git config --global alias.st status
git config --global alias.lg "log --graph --oneline --decorate --all"
```

### ステップ4：設定の動作確認

```bash
# エイリアスが動作するか確認
git st
```

---

## トラブルシューティング

### Q: `git config`コマンドが見つからない

**A:** Gitが正しくインストールされていない可能性があります。
```bash
# Gitがインストールされているか確認
which git    # macOS/Linux
where git    # Windows
```

### Q: 設定が反映されない

**A:** スコープ（global/local）を確認してください。
```bash
# グローバル設定を確認
git config --global --list

# ローカル設定を確認（リポジトリ内で実行）
git config --local --list
```

### Q: 設定を間違えた

**A:** 再度同じコマンドで上書きするか、設定を削除できます。
```bash
# 設定を上書き
git config --global user.name "Correct Name"

# 設定を削除
git config --global --unset user.name
```

---

## チェックリスト

設定が完了したら、以下を確認しましょう：

- [ ] Gitがインストールされている（`git --version`で確認）
- [ ] ユーザー名が設定されている
- [ ] メールアドレスが設定されている
- [ ] デフォルトブランチが`main`に設定されている
- [ ] エディタが設定されている（オプション）
- [ ] 日本語ファイル名の文字化け対策が済んでいる

---

## 次のステップ

環境設定が完了したら、いよいよGitの基本コマンドを学びましょう！

👉 [03. 基本コマンド](./03_basic_commands.md)

---

## 参考資料

- [Git公式ドキュメント - 最初のGitの構成](https://git-scm.com/book/ja/v2/%E4%BD%BF%E3%81%84%E5%A7%8B%E3%82%81%E3%82%8B-%E6%9C%80%E5%88%9D%E3%81%AEGit%E3%81%AE%E6%A7%8B%E6%88%90)
- [GitHub Docs - Git の設定](https://docs.github.com/ja/get-started/getting-started-with-git/setting-your-username-in-git)
