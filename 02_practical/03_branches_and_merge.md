# ブランチとマージ

## 目次
1. [ブランチとは](#ブランチとは)
2. [ブランチの基本操作](#ブランチの基本操作)
3. [マージ](#マージ)
4. [コンフリクトの解決](#コンフリクトの解決)
5. [ブランチ戦略](#ブランチ戦略)
6. [実践演習](#実践演習)

---

## ブランチとは

### ブランチの概念

**ブランチ**は、開発の流れを分岐させる機能です。本番コードに影響を与えずに、新機能の開発やバグ修正ができます。

```
main:      A---B---C---F---G
                \       /
feature:         D-----E
```

### ブランチのメリット

1. **並行開発**
   - 複数の機能を同時に開発できる
   - 他の開発者の作業に影響しない

2. **安全な実験**
   - 新しいアイデアを試せる
   - 失敗しても本番コードに影響なし

3. **コードレビュー**
   - プルリクエストによるレビューが可能
   - 品質を保ちながら開発できる

4. **リリース管理**
   - 本番、開発、機能ごとにブランチを分ける
   - 計画的なリリースが可能

### Gitのブランチは軽量

他のバージョン管理システムと違い、Gitのブランチは非常に軽量です：
- ブランチ作成は一瞬
- ディスク容量もほとんど使わない
- 積極的にブランチを使うべき

---

## ブランチの基本操作

### git branch - ブランチの管理

```bash
# ブランチ一覧を表示
git branch

# リモートブランチも含めて表示
git branch -a

# 詳細情報を表示
git branch -v

# 新しいブランチを作成（切り替えはしない）
git branch <branch-name>

# ブランチを削除
git branch -d <branch-name>

# 強制削除（マージされていなくても削除）
git branch -D <branch-name>

# ブランチ名を変更
git branch -m <old-name> <new-name>
```

**例：**
```bash
# 現在のブランチを確認
git branch
* main

# 新しいブランチを作成
git branch feature/login

# ブランチ一覧
git branch
  feature/login
* main
```

### git checkout - ブランチの切り替え

```bash
# ブランチを切り替え
git checkout <branch-name>

# 新しいブランチを作成して切り替え
git checkout -b <branch-name>

# リモートブランチを元に作成して切り替え
git checkout -b <branch-name> origin/<branch-name>

# 前のブランチに戻る
git checkout -
```

**例：**
```bash
# feature/loginブランチに切り替え
git checkout feature/login

# 新しいブランチを作成して切り替え
git checkout -b feature/signup

# mainブランチに戻る
git checkout main
```

### git switch - ブランチの切り替え（Git 2.23以降）

より直感的なコマンドとして`git switch`が追加されました。

```bash
# ブランチを切り替え
git switch <branch-name>

# 新しいブランチを作成して切り替え
git switch -c <branch-name>

# 前のブランチに戻る
git switch -
```

### checkout vs switch

```bash
# 従来の方法
git checkout feature/login      # ブランチ切り替え
git checkout -b feature/signup  # 作成 + 切り替え
git checkout HEAD -- file.txt   # ファイルの復元

# 新しい方法（推奨）
git switch feature/login        # ブランチ切り替え
git switch -c feature/signup    # 作成 + 切り替え
git restore file.txt            # ファイルの復元
```

---

## マージ

### git merge - ブランチを統合

```bash
# 現在のブランチに<branch-name>をマージ
git merge <branch-name>

# マージコミットを作成しない（Fast-forwardのみ）
git merge --ff-only <branch-name>

# 必ずマージコミットを作成
git merge --no-ff <branch-name>

# マージを中止
git merge --abort
```

### マージの種類

#### 1. Fast-forward merge（早送りマージ）

マージ先が変更されていない場合、ポインタを進めるだけ。

```
Before:
main:    A---B---C
              \
feature:       D---E

After: git checkout main && git merge feature
main:    A---B---C---D---E
feature:             \---E
```

```bash
# Fast-forward mergeの実行
git checkout main
git merge feature
# Updating a1b2c3d..e5f6g7h
# Fast-forward
```

#### 2. 3-way merge（3方向マージ）

両方のブランチが進んでいる場合、新しいマージコミットを作成。

```
Before:
main:    A---B---C-------F
              \
feature:       D---E

After: git checkout main && git merge feature
main:    A---B---C-------F---G (Gはマージコミット)
              \             /
feature:       D-----E------
```

```bash
# 3-way mergeの実行
git checkout main
git merge feature
# Merge made by the 'recursive' strategy.
```

#### 3. Squash merge

複数のコミットを1つにまとめてマージ。

```
Before:
main:    A---B---C
              \
feature:       D---E---F

After: git merge --squash feature
main:    A---B---C---G (GはD,E,Fをまとめたコミット)
```

```bash
# Squash mergeの実行
git checkout main
git merge --squash feature
git commit -m "Merge feature: まとめてコミット"
```

### Fast-forward vs No-fast-forward

#### Fast-forward（デフォルト）
- 履歴が直線的になる
- ブランチの存在が履歴から消える

#### No-fast-forward（--no-ff）
- 必ずマージコミットを作成
- ブランチの存在が履歴に残る
- **チーム開発では推奨**

```bash
# No-fast-forwardでマージ
git merge --no-ff feature/login -m "Merge feature: ログイン機能"
```

```
Fast-forward:
main: A---B---C---D---E

No-fast-forward:
main: A---B---C-----------F
           \             /
feature:    D-----E-----
```

---

## コンフリクトの解決

### コンフリクトとは

同じファイルの同じ場所を、異なるブランチで編集した場合に発生します。

```
main:    A---B(file.txt: "Hello")---C
              \
feature:       D(file.txt: "Hi")
```

### コンフリクトの発生

```bash
$ git merge feature
Auto-merging file.txt
CONFLICT (content): Merge conflict in file.txt
Automatic merge failed; fix conflicts and then commit the result.
```

### コンフリクトの確認

```bash
# コンフリクトが発生しているファイルを確認
git status

# On branch main
# You have unmerged paths.
#   (fix conflicts and run "git commit")
#
# Unmerged paths:
#   (use "git add <file>..." to mark resolution)
#
#	both modified:   file.txt
```

### コンフリクトマーカー

コンフリクトが発生したファイルには、以下のマーカーが追加されます：

```
<<<<<<< HEAD
Hello World (現在のブランチの内容)
=======
Hi World (マージしようとしているブランチの内容)
>>>>>>> feature
```

### コンフリクトの解決手順

#### 1. ファイルを編集

```bash
# コンフリクトマーカーを削除し、正しい内容に修正
# Before:
<<<<<<< HEAD
Hello World
=======
Hi World
>>>>>>> feature

# After:
Hello World
```

#### 2. ステージング

```bash
# 解決済みとしてマーク
git add file.txt
```

#### 3. コミット

```bash
# マージコミットを作成
git commit
```

### コンフリクトを起こしにくくする工夫

1. **こまめにマージ**
   ```bash
   # mainの変更を定期的に取り込む
   git checkout feature
   git merge main
   ```

2. **小さな変更単位**
   - 大きな変更は避ける
   - 機能ごとにブランチを分ける

3. **コミュニケーション**
   - 同じファイルを編集する場合は事前に相談
   - コードレビューで早期発見

### コンフリクト解決ツール

```bash
# マージツールを起動
git mergetool

# 使用するツールを設定
git config --global merge.tool vimdiff
git config --global merge.tool meld
git config --global merge.tool vscode
```

**VS Codeでの解決**:
VS Codeは組み込みのマージエディタを提供しています：
- "Accept Current Change"
- "Accept Incoming Change"
- "Accept Both Changes"
- "Compare Changes"

---

## ブランチ戦略

### GitHub Flow（シンプル）

最もシンプルなワークフロー：

```
main (常にデプロイ可能)
 ↓
feature branch で開発
 ↓
Pull Request でレビュー
 ↓
mainにマージ
 ↓
デプロイ
```

**特徴**:
- シンプルで理解しやすい
- 小規模チーム向け
- 継続的デプロイに適している

### Git Flow（詳細は応用編で）

より複雑なワークフロー：

```
main (本番)
 ↓
develop (開発)
 ↓
feature branches (機能開発)
release branches (リリース準備)
hotfix branches (緊急修正)
```

**特徴**:
- 大規模プロジェクト向け
- 計画的なリリース管理
- 本番・開発・機能を明確に分離

### トランクベース開発

短命なブランチで開発：

```
main (trunk)
 ↓
短命な feature branch (1-2日)
 ↓
頻繁にmainにマージ
```

**特徴**:
- 統合の問題を早期発見
- CI/CDに最適
- 大規模チームでも効果的

---

## 実践演習

### 演習1：ブランチの作成とマージ

```bash
# 1. 新しいブランチを作成
git checkout -b feature/add-footer

# 2. ファイルを編集
echo "<footer>Copyright 2024</footer>" >> index.html

# 3. コミット
git add index.html
git commit -m "Add footer to index.html"

# 4. mainブランチに戻る
git checkout main

# 5. マージ
git merge feature/add-footer

# 6. ブランチを削除
git branch -d feature/add-footer
```

### 演習2：コンフリクトの解決

```bash
# 1. 準備: mainブランチでファイルを作成
git checkout main
echo "Hello" > greet.txt
git add greet.txt
git commit -m "Add greet.txt"

# 2. feature1ブランチで編集
git checkout -b feature1
echo "Hello World" > greet.txt
git add greet.txt
git commit -m "Update greeting in feature1"

# 3. mainブランチに戻って別の編集
git checkout main
echo "Hi There" > greet.txt
git add greet.txt
git commit -m "Update greeting in main"

# 4. マージしてコンフリクトを発生させる
git merge feature1
# CONFLICT (content): Merge conflict in greet.txt

# 5. ファイルを開いてコンフリクトを解決
# 好きな方を選択または両方を統合

# 6. 解決してコミット
git add greet.txt
git commit -m "Resolve merge conflict"
```

### 演習3：No-fast-forwardマージ

```bash
# 1. 機能ブランチを作成
git checkout -b feature/header
echo "<header>My Site</header>" > header.html
git add header.html
git commit -m "Add header"

# 2. mainに戻ってNo-fast-forwardでマージ
git checkout main
git merge --no-ff feature/header -m "Merge feature: Add header"

# 3. ログを確認（マージコミットが作成されている）
git log --graph --oneline --decorate
```

---

## よくあるコマンド一覧

### ブランチ操作
```bash
git branch                    # ブランチ一覧
git branch <name>            # ブランチ作成
git switch <name>            # ブランチ切り替え
git switch -c <name>         # 作成 + 切り替え
git branch -d <name>         # ブランチ削除
git branch -m <old> <new>    # ブランチ名変更
```

### マージ操作
```bash
git merge <branch>           # マージ
git merge --no-ff <branch>   # No-FF マージ
git merge --squash <branch>  # Squash マージ
git merge --abort            # マージ中止
```

### コンフリクト解決
```bash
git status                   # コンフリクト確認
git add <file>              # 解決済みマーク
git commit                  # マージコミット
git mergetool               # マージツール起動
```

---

## トラブルシューティング

### Q: ブランチを削除できない

```bash
$ git branch -d feature
error: The branch 'feature' is not fully merged.
```

**解決方法**:
```bash
# マージされていなくても強制削除
git branch -D feature
```

### Q: 間違ったブランチで作業してしまった

```bash
# 変更を別のブランチに移動
git stash
git checkout correct-branch
git stash pop
```

### Q: マージを取り消したい

```bash
# マージ直後の場合
git reset --hard HEAD~1

# マージがpush済みの場合
git revert -m 1 <merge-commit-hash>
```

---

## ベストプラクティス

1. **わかりやすいブランチ名**
   ```bash
   feature/user-auth       # 機能開発
   fix/login-bug          # バグ修正
   hotfix/security-patch  # 緊急修正
   release/v1.0.0         # リリース
   ```

2. **短命なブランチ**
   - 長期間のブランチは避ける
   - こまめにマージする

3. **1つのブランチ = 1つの目的**
   - 複数の機能を混ぜない
   - レビューしやすくする

4. **マージ前にテスト**
   ```bash
   # ブランチでテスト
   npm test
   # 問題なければマージ
   git checkout main
   git merge feature
   ```

---

## チェックリスト

- [ ] ブランチを作成・削除できる
- [ ] ブランチを切り替えられる
- [ ] Fast-forwardマージとNo-fast-forwardマージの違いを理解している
- [ ] コンフリクトを解決できる
- [ ] わかりやすいブランチ名をつけられる

---

## 次のステップ

ブランチとマージをマスターしたら、プルリクエストを学びましょう！

👉 [04. プルリクエスト](./04_pull_requests.md)

---

## 参考資料

- [Pro Git - ブランチとマージの基本](https://git-scm.com/book/ja/v2/Git-%E3%81%AE%E3%83%96%E3%83%A9%E3%83%B3%E3%83%81%E6%A9%9F%E8%83%BD-%E3%83%96%E3%83%A9%E3%83%B3%E3%83%81%E3%81%A8%E3%83%9E%E3%83%BC%E3%82%B8%E3%81%AE%E5%9F%BA%E6%9C%AC)
- [Learn Git Branching](https://learngitbranching.js.org/?locale=ja)
