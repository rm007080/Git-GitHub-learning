# GitHubの基礎

## 目次
1. [GitHubとは](#githubとは)
2. [GitHubアカウントの作成](#githubアカウントの作成)
3. [リポジトリの作成](#リポジトリの作成)
4. [SSH認証の設定](#ssh認証の設定)
5. [README・ライセンス・.gitignore](#readmeライセンスgitignore)
6. [実践演習](#実践演習)

---

## GitHubとは

### GitHubの役割

**GitHub**は、Gitリポジトリをホスティング（保管）するWebサービスです。

```
ローカル環境              GitHub（リモート）
┌──────────┐              ┌──────────┐
│   .git   │   push →    │リポジトリ│
│リポジトリ│   ← pull    │          │
└──────────┘              └──────────┘
```

### GitHubでできること

1. **コードのホスティング**
   - リポジトリをクラウドに保存
   - どこからでもアクセス可能

2. **コラボレーション**
   - 複数人での開発
   - プルリクエストによるコードレビュー
   - Issue管理

3. **公開・共有**
   - オープンソースプロジェクトの公開
   - ポートフォリオとしての活用

4. **CI/CD**
   - GitHub Actionsによる自動化
   - テスト・デプロイの自動実行

5. **プロジェクト管理**
   - Issue、Project、Wiki
   - マイルストーン管理

### GitHubの代替サービス

- **GitLab**: 自社でホスティング可能
- **Bitbucket**: Atlassian製品との連携
- **Gitea**: 軽量なセルフホスティング
- **SourceForge**: 老舗のホスティングサービス

---

## GitHubアカウントの作成

### 1. アカウント作成

1. [GitHub](https://github.com/)にアクセス
2. 「Sign up」をクリック
3. 以下の情報を入力：
   - メールアドレス
   - パスワード
   - ユーザー名（後で変更可能）

### 2. プランの選択

- **Free**: 個人利用、パブリックリポジトリ無制限
- **Pro**: プライベートリポジトリの高度な機能
- **Team**: チーム向け
- **Enterprise**: 大規模組織向け

**初学者はFreeプランで十分です。**

### 3. プロフィール設定

良いプロフィールは、採用担当者や他の開発者にあなたを知ってもらうのに役立ちます。

```
設定項目：
- プロフィール画像
- 自己紹介（Bio）
- 所在地
- WebサイトURL
- Twitter/LinkedIn等のリンク
```

---

## リポジトリの作成

### GitHubでリポジトリを作成する方法

#### 方法1：GitHub上で新規作成

1. GitHubにログイン
2. 右上の「+」→「New repository」をクリック
3. 以下を設定：
   - **Repository name**: リポジトリ名（例: my-first-repo）
   - **Description**: 説明（オプション）
   - **Public/Private**: 公開設定
   - **Initialize**: README、.gitignore、ライセンスを追加（推奨）

4. 「Create repository」をクリック

#### 方法2：ローカルから作成してプッシュ

```bash
# ローカルでリポジトリを作成
mkdir my-project
cd my-project
git init
echo "# My Project" > README.md
git add README.md
git commit -m "Initial commit"

# GitHubにプッシュ（リポジトリは事前に作成）
git remote add origin https://github.com/username/my-project.git
git branch -M main
git push -u origin main
```

### リポジトリの種類

#### Public（パブリック）
- 誰でも閲覧可能
- オープンソースプロジェクトに最適
- 学習の成果を公開できる

#### Private（プライベート）
- 指定したユーザーのみアクセス可能
- 商用プロジェクトや個人的なプロジェクトに

---

## SSH認証の設定

HTTPS認証よりもSSH認証が推奨されます。パスワードを毎回入力する必要がなくなります。

### SSHキーの生成

```bash
# SSHキーを生成
ssh-keygen -t ed25519 -C "your.email@example.com"

# 保存場所を聞かれる（Enterでデフォルト）
Enter file in which to save the key (/home/user/.ssh/id_ed25519):

# パスフレーズを設定（オプション、推奨）
Enter passphrase (empty for no passphrase):
```

**注意**: 古いシステムではed25519がサポートされていない場合があります。その場合は：
```bash
ssh-keygen -t rsa -b 4096 -C "your.email@example.com"
```

### 公開鍵をGitHubに登録

```bash
# 公開鍵をクリップボードにコピー（Linux）
cat ~/.ssh/id_ed25519.pub | xclip -selection clipboard

# macOS
cat ~/.ssh/id_ed25519.pub | pbcopy

# Windows（Git Bash）
cat ~/.ssh/id_ed25519.pub | clip

# または、手動でコピー
cat ~/.ssh/id_ed25519.pub
```

**GitHubでの設定：**

1. GitHub → Settings → SSH and GPG keys
2. 「New SSH key」をクリック
3. Title: 任意の名前（例: My Laptop）
4. Key: コピーした公開鍵を貼り付け
5. 「Add SSH key」をクリック

### 接続テスト

```bash
ssh -T git@github.com
```

成功すると以下のメッセージが表示されます：
```
Hi username! You've successfully authenticated, but GitHub does not provide shell access.
```

### HTTPS vs SSH

```bash
# HTTPS（パスワードまたはトークンが必要）
git clone https://github.com/username/repo.git

# SSH（鍵認証、パスワード不要）
git clone git@github.com:username/repo.git
```

**既存のリポジトリをSSHに変更：**
```bash
# 現在のリモートURLを確認
git remote -v

# HTTPSからSSHに変更
git remote set-url origin git@github.com:username/repo.git
```

---

## README・ライセンス・.gitignore

### README.md

リポジトリの「顔」となる重要なファイルです。

**良いREADMEの構成：**

```markdown
# プロジェクト名

プロジェクトの簡単な説明

## 特徴

- 特徴1
- 特徴2

## インストール

\`\`\`bash
npm install my-project
\`\`\`

## 使い方

\`\`\`javascript
const myProject = require('my-project');
myProject.doSomething();
\`\`\`

## ライセンス

MIT License

## 貢献

プルリクエスト歓迎です！

## 作者

[@yourname](https://github.com/yourname)
```

### ライセンス

主なオープンソースライセンス：

#### MIT License
- 最も寛容
- 商用利用OK
- 改変・再配布OK
- **初学者におすすめ**

#### Apache License 2.0
- MITに加えて特許権の保護
- 企業での利用に適している

#### GPL (GNU General Public License)
- コピーレフト
- 派生物も同じライセンスで公開が必要

#### BSD License
- MITに似た寛容なライセンス

**ライセンスの追加方法：**
1. リポジトリ作成時に選択
2. または、「Add file」→「Create new file」→ファイル名に「LICENSE」と入力すると、テンプレートが選択できる

### .gitignore

Gitで追跡したくないファイルを指定します。

**言語別のテンプレート：**

GitHubでリポジトリ作成時に、言語を選択すると自動的に適切な`.gitignore`が作成されます。

```gitignore
# Node.js
node_modules/
npm-debug.log

# Python
__pycache__/
*.py[cod]
.venv/

# Java
*.class
target/

# macOS
.DS_Store

# IDEs
.vscode/
.idea/
```

**GitHub公式テンプレート集：**
[github/gitignore](https://github.com/github/gitignore)

---

## GitHubの基本機能

### Issues（イシュー）

バグ報告、機能要求、タスク管理に使用します。

```
Issue の例：

Title: ログイン機能のバグ

Description:
## 問題
パスワードが間違っていてもログインできてしまう

## 再現手順
1. ログインページにアクセス
2. 正しいユーザー名と間違ったパスワードを入力
3. ログインボタンをクリック

## 期待される動作
エラーメッセージが表示される

## 実際の動作
ログインできてしまう

## 環境
- ブラウザ: Chrome 120
- OS: macOS 14.0
```

### Stars（スター）

お気に入りのリポジトリに「スター」を付けられます。

```bash
# 人気のリポジトリを探す
https://github.com/trending
```

### Fork（フォーク）

他人のリポジトリを自分のアカウントにコピーします。

```
元のリポジトリ          あなたのフォーク
┌──────────┐            ┌──────────┐
│ user/repo│  Fork →   │you/repo  │
└──────────┘            └──────────┘
```

### Watch（ウォッチ）

リポジトリの更新を通知で受け取れます。

---

## 実践演習

### 演習1：GitHubリポジトリの作成

1. GitHubにログイン
2. 新しいリポジトリを作成
   - Name: `hello-github`
   - Public
   - Add README file: チェック
3. 作成したリポジトリをローカルにクローン

```bash
git clone https://github.com/username/hello-github.git
cd hello-github
```

### 演習2：変更をプッシュ

```bash
# ファイルを編集
echo "Hello, GitHub!" >> README.md

# 変更をコミット
git add README.md
git commit -m "Update README"

# GitHubにプッシュ
git push origin main
```

GitHubのページで変更が反映されているか確認しましょう！

### 演習3：SSH認証の設定

1. SSHキーを生成
2. 公開鍵をGitHubに登録
3. 接続テスト
4. 既存のリポジトリをSSHに変更

```bash
git remote set-url origin git@github.com:username/hello-github.git
```

---

## トラブルシューティング

### Q: pushで認証エラーが出る

```
remote: Support for password authentication was removed...
```

**A**: GitHubは2021年8月以降、パスワード認証を廃止しました。以下のいずれかを使用してください：
- 個人アクセストークン（Personal Access Token）
- SSH認証（推奨）

### Q: SSH接続ができない

```bash
# SSH接続をデバッグ
ssh -vT git@github.com
```

**確認事項：**
- 公開鍵が正しくGitHubに登録されているか
- SSH Agentが起動しているか
- ファイアウォールでSSH（ポート22）がブロックされていないか

### Q: リモートリポジトリのURLを間違えた

```bash
# 現在のURLを確認
git remote -v

# URLを変更
git remote set-url origin https://github.com/username/correct-repo.git
```

---

## チェックリスト

- [ ] GitHubアカウントを作成した
- [ ] プロフィールを設定した
- [ ] リポジトリを作成できる
- [ ] SSH認証を設定した
- [ ] ローカルとGitHub間でpush/pullができる
- [ ] README、ライセンス、.gitignoreの目的を理解した

---

## 次のステップ

GitHubの基本を理解したら、リモートリポジトリの操作を詳しく学びましょう！

👉 [02. リモートリポジトリの操作](./02_remote_repository.md)

---

## 参考資料

- [GitHub Docs](https://docs.github.com/ja)
- [GitHub Skills](https://skills.github.com/)
- [Pro Git Book - リモートでの作業](https://git-scm.com/book/ja/v2/Git-%E3%81%AE%E5%9F%BA%E6%9C%AC-%E3%83%AA%E3%83%A2%E3%83%BC%E3%83%88%E3%81%A7%E3%81%AE%E4%BD%9C%E6%A5%AD)
