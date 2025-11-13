# GitFlow ワークフロー

## 目次
1. [GitFlowとは](#gitflowとは)
2. [ブランチの種類](#ブランチの種類)
3. [GitFlowの運用](#gitflowの運用)
4. [実践例](#実践例)
5. [GitHub Flow vs GitFlow](#github-flow-vs-gitflow)

---

## GitFlowとは

### 概要

**GitFlow**は、Vincent Driessenが提案したGitブランチングモデルです。大規模プロジェクトや計画的なリリース管理に適しています。

### GitFlowの特徴

- 明確なブランチ構造
- 計画的なリリース管理
- 本番・開発・機能を明確に分離
- チーム開発に適している

### GitFlowが適しているプロジェクト

- **適している**:
  - 定期リリースがあるプロジェクト
  - 複数バージョンをサポート
  - 大規模チーム
  - エンタープライズ開発

- **適していない**:
  - 継続的デプロイメント
  - 小規模プロジェクト
  - モバイルアプリ（リリース後の変更が困難）

---

## ブランチの種類

GitFlowでは5種類のブランチを使用します。

### 1. Main（Master）ブランチ

**役割**: 本番環境のコード

```
main: 常にデプロイ可能な状態
      リリースされたバージョンのみ

A---B---C (v1.0)---D (v1.1)---E (v2.0)
```

**特徴**:
- 直接コミットしない
- リリースとhotfixからのみマージ
- すべてのコミットにタグを付ける

```bash
# mainブランチの確認
git checkout main
git log --oneline

# タグを付ける
git tag -a v1.0.0 -m "Release version 1.0.0"
git push origin v1.0.0
```

### 2. Developブランチ

**役割**: 開発の中心となるブランチ

```
develop: 次のリリースに向けた開発
         機能ブランチの統合先

A---B---C---D---E---F---G
```

**特徴**:
- mainから分岐して作成
- 機能ブランチはここから作成
- 常にビルド可能な状態を保つ

```bash
# developブランチの作成
git checkout -b develop main
git push -u origin develop
```

### 3. Featureブランチ

**役割**: 新機能の開発

```
develop: A---B---C-------F---G
              \         /
feature:       D---E---
```

**命名規則**: `feature/<feature-name>`

**例**:
- `feature/user-authentication`
- `feature/payment-integration`
- `feature/dashboard`

**ライフサイクル**:

```bash
# 1. developから作成
git checkout develop
git pull origin develop
git checkout -b feature/user-auth

# 2. 開発作業
git add .
git commit -m "feat: Implement user authentication"

# 3. developにマージ
git checkout develop
git merge --no-ff feature/user-auth -m "Merge feature: user auth"

# 4. ブランチを削除
git branch -d feature/user-auth
git push origin develop
```

### 4. Releaseブランチ

**役割**: リリース準備

```
develop: A---B---C---D-------H
              \         \
release:       E---F---G---
                       \
main:                   I (v1.0)
```

**命名規則**: `release/<version>`

**例**:
- `release/1.0.0`
- `release/2.1.0`

**目的**:
- バージョン番号の更新
- 最終テスト
- バグ修正（小規模のみ）
- ドキュメント更新

**ライフサイクル**:

```bash
# 1. developから作成
git checkout develop
git checkout -b release/1.0.0

# 2. バージョン番号を更新
echo "1.0.0" > VERSION
git add VERSION
git commit -m "chore: Bump version to 1.0.0"

# 3. 最終テスト・バグ修正
# （小規模な修正のみ）

# 4. mainにマージ
git checkout main
git merge --no-ff release/1.0.0
git tag -a v1.0.0 -m "Release version 1.0.0"

# 5. developにもマージ（修正を反映）
git checkout develop
git merge --no-ff release/1.0.0

# 6. ブランチを削除
git branch -d release/1.0.0

# 7. プッシュ
git push origin main develop --tags
```

### 5. Hotfixブランチ

**役割**: 本番環境の緊急バグ修正

```
main:    A---B (v1.0)-------E (v1.0.1)
              \             /
hotfix:        C-----D-----
                     \
develop:              F
```

**命名規則**: `hotfix/<version>`

**例**:
- `hotfix/1.0.1`
- `hotfix/2.1.1`

**ライフサイクル**:

```bash
# 1. mainから作成
git checkout main
git checkout -b hotfix/1.0.1

# 2. バグ修正
git add .
git commit -m "fix: Critical security bug"

# 3. バージョン更新
echo "1.0.1" > VERSION
git add VERSION
git commit -m "chore: Bump version to 1.0.1"

# 4. mainにマージ
git checkout main
git merge --no-ff hotfix/1.0.1
git tag -a v1.0.1 -m "Hotfix version 1.0.1"

# 5. developにもマージ
git checkout develop
git merge --no-ff hotfix/1.0.1

# 6. ブランチを削除
git branch -d hotfix/1.0.1

# 7. プッシュ
git push origin main develop --tags
```

---

## GitFlowの運用

### 初期セットアップ

```bash
# 1. リポジトリを作成
git init

# 2. 初回コミット
echo "# My Project" > README.md
git add README.md
git commit -m "Initial commit"

# 3. mainブランチの設定（既にmainの場合はスキップ）
git branch -M main

# 4. developブランチを作成
git checkout -b develop

# 5. リモートにプッシュ
git remote add origin <url>
git push -u origin main develop
```

### 日常的な開発フロー

#### 新機能の開発

```bash
# 1. 最新のdevelopを取得
git checkout develop
git pull origin develop

# 2. featureブランチを作成
git checkout -b feature/new-feature

# 3. 開発作業
# （ファイルを編集）
git add .
git commit -m "feat: Add new feature"

# 4. developにマージ
git checkout develop
git pull origin develop  # 最新を再取得
git merge --no-ff feature/new-feature

# 5. プッシュ
git push origin develop

# 6. ブランチを削除
git branch -d feature/new-feature
```

#### リリース

```bash
# 1. releaseブランチを作成
git checkout develop
git checkout -b release/1.0.0

# 2. バージョンを更新
# （VERSION ファイル、package.json など）
git add .
git commit -m "chore: Prepare release 1.0.0"

# 3. テスト・修正
# （必要に応じて）

# 4. mainとdevelopにマージ
git checkout main
git merge --no-ff release/1.0.0
git tag -a v1.0.0 -m "Release 1.0.0"

git checkout develop
git merge --no-ff release/1.0.0

# 5. プッシュ
git push origin main develop --tags

# 6. デプロイ
# （CI/CDまたは手動）
```

#### 緊急修正

```bash
# 1. hotfixブランチを作成
git checkout main
git checkout -b hotfix/1.0.1

# 2. 修正
git add .
git commit -m "fix: Critical bug"

# 3. バージョンを更新
git add .
git commit -m "chore: Bump to 1.0.1"

# 4. mainとdevelopにマージ
git checkout main
git merge --no-ff hotfix/1.0.1
git tag -a v1.0.1 -m "Hotfix 1.0.1"

git checkout develop
git merge --no-ff hotfix/1.0.1

# 5. プッシュ
git push origin main develop --tags
```

---

## git-flow ツール

### インストール

```bash
# macOS
brew install git-flow

# Ubuntu
sudo apt-get install git-flow

# Windows
# Git for Windowsに含まれている
```

### 初期化

```bash
# GitFlowを初期化（対話式）
git flow init

# デフォルト設定で初期化
git flow init -d
```

### 機能開発

```bash
# 機能ブランチを開始
git flow feature start user-auth

# 機能を完了（developにマージ）
git flow feature finish user-auth
```

### リリース

```bash
# リリースを開始
git flow release start 1.0.0

# リリースを完了（main と develop にマージ、タグ作成）
git flow release finish 1.0.0
```

### Hotfix

```bash
# Hotfixを開始
git flow hotfix start 1.0.1

# Hotfixを完了
git flow hotfix finish 1.0.1
```

---

## 実践例

### シナリオ：ECサイトの開発

```bash
# 1. 初期セットアップ
git init
git add .
git commit -m "Initial commit"
git branch develop
git push -u origin main develop

# 2. ショッピングカート機能の開発
git checkout develop
git checkout -b feature/shopping-cart
# 開発...
git add .
git commit -m "feat: Add shopping cart"
git checkout develop
git merge --no-ff feature/shopping-cart
git push origin develop

# 3. ユーザー認証機能の開発（並行開発）
git checkout develop
git checkout -b feature/user-auth
# 開発...
git add .
git commit -m "feat: Add user authentication"
git checkout develop
git merge --no-ff feature/user-auth
git push origin develop

# 4. リリース準備
git checkout develop
git checkout -b release/1.0.0
# バージョン更新、最終テスト...
git checkout main
git merge --no-ff release/1.0.0
git tag -a v1.0.0 -m "Release 1.0.0"
git checkout develop
git merge --no-ff release/1.0.0
git push origin main develop --tags

# 5. 本番で重大なバグ発見
git checkout main
git checkout -b hotfix/1.0.1
# バグ修正...
git add .
git commit -m "fix: Fix payment processing bug"
git checkout main
git merge --no-ff hotfix/1.0.1
git tag -a v1.0.1 -m "Hotfix 1.0.1"
git checkout develop
git merge --no-ff hotfix/1.0.1
git push origin main develop --tags
```

---

## GitHub Flow vs GitFlow

### GitHub Flow（シンプル）

```
main
 ├── feature1
 ├── feature2
 └── feature3
```

**特徴**:
- ブランチは main と feature のみ
- 継続的デプロイメント
- シンプル

**適用**:
- SaaS
- Webアプリ
- 小〜中規模チーム

### GitFlow（複雑）

```
main
 ├── develop
 │    ├── feature1
 │    ├── feature2
 │    └── feature3
 ├── release
 └── hotfix
```

**特徴**:
- 5種類のブランチ
- 計画的リリース
- 複雑だが強力

**適用**:
- パッケージソフトウェア
- 複数バージョンサポート
- 大規模チーム

### 比較表

| 項目 | GitHub Flow | GitFlow |
|------|-------------|---------|
| ブランチ数 | 少ない（2種類） | 多い（5種類） |
| 複雑さ | シンプル | 複雑 |
| リリース | 継続的 | 計画的 |
| 学習コスト | 低い | 高い |
| チーム規模 | 小〜中 | 中〜大 |

---

## ベストプラクティス

### 1. developは常にビルド可能に

```bash
# マージ前に必ずテスト
npm test
git checkout develop
git merge --no-ff feature/my-feature
```

### 2. こまめにdevelopにマージ

```bash
# 長期間のfeatureブランチは避ける
# 2-3日に1回はマージを検討
```

### 3. releaseブランチでは大きな変更をしない

```bash
# リリースブランチでは:
# ✓ バグ修正（小規模）
# ✓ ドキュメント更新
# ✓ バージョン番号更新
# ✗ 新機能追加
# ✗ 大規模なリファクタリング
```

### 4. タグは必ず付ける

```bash
# すべてのリリースにタグ
git tag -a v1.0.0 -m "Release 1.0.0"
git push origin --tags
```

---

## チェックリスト

- [ ] GitFlowの5つのブランチを理解している
- [ ] featureブランチで開発できる
- [ ] releaseブランチでリリース準備ができる
- [ ] hotfixブランチで緊急修正ができる
- [ ] プロジェクトに適したワークフローを選択できる

---

## 次のステップ

GitFlowをマスターしたら、リベースについて学びましょう！

👉 [02. Rebase](./02_rebase.md)

---

## 参考資料

- [A successful Git branching model](https://nvie.com/posts/a-successful-git-branching-model/)
- [git-flow cheatsheet](https://danielkummer.github.io/git-flow-cheatsheet/index.ja_JP.html)
- [Atlassian GitFlow Workflow](https://www.atlassian.com/git/tutorials/comparing-workflows/gitflow-workflow)
