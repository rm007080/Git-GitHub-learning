# Rebase（リベース）

## 目次
1. [Rebaseとは](#rebaseとは)
2. [MergeとRebaseの違い](#mergeとrebaseの違い)
3. [Rebaseの基本操作](#rebaseの基本操作)
4. [Interactive Rebase](#interactive-rebase)
5. [Rebaseの注意点](#rebaseの注意点)
6. [実践演習](#実践演習)

---

## Rebaseとは

### 概要

**Rebase**は、ブランチの基点（base）を変更する操作です。コミット履歴を「書き換える」強力な機能です。

### Rebaseの目的

1. **履歴をきれいに保つ**
   - 直線的な履歴を作る
   - マージコミットを減らす

2. **コミットを整理する**
   - 複数のコミットをまとめる
   - コミットメッセージを修正
   - コミットの順序を変更

---

## MergeとRebaseの違い

### Merge

```
Before:
main:    A---B---C
              \
feature:       D---E

After: git merge feature
main:    A---B---C---F (マージコミット)
              \     /
feature:       D---E
```

**特徴**:
- マージコミットが作成される
- 履歴が分岐する
- 元の履歴が保持される
- **安全**

### Rebase

```
Before:
main:    A---B---C
              \
feature:       D---E

After: git rebase main
main:    A---B---C
feature:           D'---E' (mainの上に再適用)
```

**特徴**:
- マージコミットなし
- 直線的な履歴
- コミットハッシュが変わる
- **履歴を書き換える**

### どちらを使うべきか

| シナリオ | 推奨 |
|---------|------|
| ローカルブランチの整理 | Rebase |
| 公開済みブランチ | Merge |
| 履歴を保持したい | Merge |
| きれいな履歴が欲しい | Rebase |
| チーム開発 | Merge |
| 個人開発 | Rebase |

---

## Rebaseの基本操作

### git rebase - 基本的な使い方

```bash
# 現在のブランチをmainの最新に再適用
git checkout feature
git rebase main
```

### ステップバイステップ

```bash
# 1. 現在の状態を確認
git log --oneline --graph --all

# 2. mainの最新を取得
git checkout main
git pull origin main

# 3. featureブランチをrebase
git checkout feature
git rebase main

# 4. 履歴を確認
git log --oneline --graph

# 5. リモートにプッシュ（強制プッシュが必要）
git push -f origin feature
```

### Rebaseの流れ

```bash
# Before:
main:    A---B---C
              \
feature:       D---E (based on B)

# After rebase:
main:    A---B---C
                  \
feature:           D'---E' (based on C)
```

1. featureブランチのコミット（D, E）を一時保存
2. featureをmainの最新（C）に移動
3. 保存したコミットを順番に再適用

---

## Interactive Rebase

### git rebase -i - 対話的リベース

コミット履歴を編集する強力な機能です。

```bash
# 最新の3つのコミットを編集
git rebase -i HEAD~3

# 特定のコミットから後を編集
git rebase -i <commit-hash>
```

### 編集画面

```bash
pick a1b2c3d Add feature A
pick e4f5g6h Fix typo
pick i7j8k9l Add feature B

# Rebase commands:
# p, pick = use commit
# r, reword = use commit, but edit the commit message
# e, edit = use commit, but stop for amending
# s, squash = use commit, but meld into previous commit
# f, fixup = like "squash", but discard this commit's log message
# d, drop = remove commit
```

### 主なコマンド

#### 1. pick（そのまま使用）

```bash
pick a1b2c3d Add feature A
pick e4f5g6h Fix typo
```

#### 2. reword（メッセージ変更）

```bash
pick a1b2c3d Add feature A
reword e4f5g6h Fix typo  # メッセージを変更
```

#### 3. squash（前のコミットとマージ）

```bash
pick a1b2c3d Add feature A
squash e4f5g6h Fix typo in feature A  # a1b2c3dと統合
```

結果：
```bash
Add feature A

Fix typo in feature A
```

#### 4. fixup（前のコミットとマージ、メッセージ破棄）

```bash
pick a1b2c3d Add feature A
fixup e4f5g6h Fix typo  # a1b2c3dと統合、メッセージは破棄
```

結果：
```bash
Add feature A
```

#### 5. edit（コミットを修正）

```bash
pick a1b2c3d Add feature A
edit e4f5g6h Fix typo  # ここで停止、修正可能
```

停止後：
```bash
# ファイルを修正
git add .
git commit --amend

# 続行
git rebase --continue
```

#### 6. drop（コミットを削除）

```bash
pick a1b2c3d Add feature A
drop e4f5g6h Wrong commit  # このコミットを削除
```

#### 7. 順序の変更

```bash
# Before:
pick a1b2c3d Commit A
pick e4f5g6h Commit B
pick i7j8k9l Commit C

# After（順序を入れ替え）:
pick i7j8k9l Commit C
pick a1b2c3d Commit A
pick e4f5g6h Commit B
```

---

## 実践例

### 例1：複数のコミットをまとめる

```bash
# 履歴を確認
git log --oneline
# e3f4g5h Fix typo3
# c1d2e3f Fix typo2
# a1b2c3d Fix typo1
# 9x8y7z6 Add new feature

# 3つのtypo修正をまとめる
git rebase -i HEAD~4
```

編集：
```bash
pick 9x8y7z6 Add new feature
pick a1b2c3d Fix typo1
fixup c1d2e3f Fix typo2
fixup e3f4g5h Fix typo3
```

結果：
```bash
# 9x8y7z6 Add new feature
# a1b2c3d Fix typo1  # typo2, typo3が統合された
```

### 例2：コミットメッセージを修正

```bash
git rebase -i HEAD~3
```

編集：
```bash
pick a1b2c3d Add faeture  # typo!
reword c1d2e3f Update docs
pick e3f4g5h Fix bug
```

保存すると、"Add faeture"のメッセージを編集する画面が開く：
```bash
Add feature  # 修正

# コミットメッセージを修正してください...
```

### 例3：コミットの順序を変更

```bash
git log --oneline
# e3f4g5h Add tests
# c1d2e3f Add feature
# a1b2c3d Update docs

git rebase -i HEAD~3
```

編集（順序を入れ替え）：
```bash
pick a1b2c3d Update docs
pick c1d2e3f Add feature
pick e3f4g5h Add tests
↓
pick c1d2e3f Add feature
pick e3f4g5h Add tests
pick a1b2c3d Update docs
```

---

## Rebaseの注意点

### 黄金律：公開済みのコミットはRebaseしない

```bash
# ❌ 危険：他の人が使っているブランチをrebase
git checkout main
git rebase develop  # 他の人のコミットが壊れる

# ✅ 安全：自分だけのブランチをrebase
git checkout my-feature
git rebase main  # 自分のブランチなのでOK
```

### なぜ危険なのか

```
あなた:
main: A---B---C

Rebase後:
main: A---B'---C'  # ハッシュが変わった

他の開発者（Rebase前のmainを使っている）:
main: A---B---C
       \
       D---E  # B, Cが見つからない！
```

### 強制プッシュが必要

```bash
# Rebase後はプッシュが拒否される
git push origin feature
# ! [rejected]

# 強制プッシュが必要
git push -f origin feature

# より安全な強制プッシュ
git push --force-with-lease origin feature
```

### Rebaseの中断・取り消し

```bash
# Rebase中にコンフリクトが発生したら

# オプション1: コンフリクトを解決して続行
git add <resolved-files>
git rebase --continue

# オプション2: 現在のコミットをスキップ
git rebase --skip

# オプション3: Rebaseを中止
git rebase --abort
```

---

## Rebaseとコンフリクト

### コンフリクトの解決

```bash
# Rebase中にコンフリクト発生
$ git rebase main
CONFLICT (content): Merge conflict in file.txt

# ファイルを編集してコンフリクトを解決
vim file.txt

# 解決したファイルをステージング
git add file.txt

# Rebaseを続行
git rebase --continue

# 次のコミットでもコンフリクトが...
# （同じ手順を繰り返す）
```

### コンフリクトが多い場合

```bash
# Mergeの方が簡単な場合もある
git rebase --abort
git merge main
```

---

## 実践演習

### 演習1：基本的なRebase

```bash
# 1. mainブランチを更新
git checkout main
git pull origin main

# 2. featureブランチを作成
git checkout -b feature/test-rebase
echo "Feature content" > feature.txt
git add feature.txt
git commit -m "Add feature"

# 3. mainに変更を追加（他の人の作業をシミュレート）
git checkout main
echo "Main content" > main.txt
git add main.txt
git commit -m "Update main"

# 4. featureブランチをrebase
git checkout feature/test-rebase
git rebase main

# 5. 履歴を確認
git log --oneline --graph
```

### 演習2：Interactive Rebase

```bash
# 1. 複数のコミットを作成
git checkout -b feature/interactive-test
echo "Line 1" > test.txt && git add . && git commit -m "Add line 1"
echo "Line 2" >> test.txt && git add . && git commit -m "Add line 2"
echo "Line 3" >> test.txt && git add . && git commit -m "Add line 3"

# 2. 履歴を確認
git log --oneline
# abc1234 Add line 3
# def5678 Add line 2
# ghi9012 Add line 1

# 3. 3つのコミットを1つにまとめる
git rebase -i HEAD~3

# エディタで:
# pick ghi9012 Add line 1
# fixup def5678 Add line 2
# fixup ghi9012 Add line 3

# 4. 結果を確認
git log --oneline
# abc1234 Add line 1  # 3つが統合された
```

---

## Merge vs Rebase まとめ

### Merge を使うべき場合

```bash
✅ 公開済みブランチ
✅ チーム開発
✅ 履歴を保持したい
✅ 安全性が最優先
```

### Rebase を使うべき場合

```bash
✅ ローカルブランチの整理
✅ PRを出す前のクリーンアップ
✅ きれいな履歴が欲しい
✅ 個人開発
```

### 推奨ワークフロー

```bash
# 1. ローカルでrebaseを使って履歴を整理
git checkout feature
git rebase main

# 2. PRを作成
git push -f origin feature

# 3. PRをmergeでmainに統合
# （GitHub上でMerge）
```

---

## ベストプラクティス

### 1. こまめにRebase

```bash
# mainの変更を定期的に取り込む
git checkout feature
git fetch origin
git rebase origin/main
```

### 2. PRを出す前にCleanup

```bash
# コミットを整理してから PR
git rebase -i main
git push -f origin feature
```

### 3. 強制プッシュは慎重に

```bash
# --force-with-lease を使う
git push --force-with-lease origin feature
```

### 4. Rebase中はこまめにコミット

```bash
# コンフリクト解決は段階的に
git rebase --continue
```

---

## チェックリスト

- [ ] MergeとRebaseの違いを理解している
- [ ] 基本的なRebaseができる
- [ ] Interactive Rebaseでコミットを編集できる
- [ ] Rebaseの危険性を理解している
- [ ] 適切な場面でRebaseを使える

---

## 次のステップ

Rebaseをマスターしたら、コンフリクト解決の詳細を学びましょう！

👉 [03. コンフリクトの解決](./03_conflict_resolution.md)

---

## 参考資料

- [Pro Git - Rebasing](https://git-scm.com/book/ja/v2/Git-%E3%81%AE%E3%83%96%E3%83%A9%E3%83%B3%E3%83%81%E6%A9%9F%E8%83%BD-%E3%83%AA%E3%83%99%E3%83%BC%E3%82%B9)
- [Atlassian - Git Rebase](https://www.atlassian.com/git/tutorials/rewriting-history/git-rebase)
- [Git Merge vs Rebase](https://www.atlassian.com/git/tutorials/merging-vs-rebasing)
