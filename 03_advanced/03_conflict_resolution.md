# コンフリクトの解決

## 目次
1. [コンフリクトとは](#コンフリクトとは)
2. [コンフリクトの予防](#コンフリクトの予防)
3. [コンフリクトの解決方法](#コンフリクトの解決方法)
4. [高度なコンフリクト解決](#高度なコンフリクト解決)
5. [トラブルシューティング](#トラブルシューティング)

---

## コンフリクトとは

### 発生原因

同じファイルの同じ場所を、異なるブランチで編集したときに発生します。

```
main:    A---B(file.txt: line 1 = "Hello")
              \
feature:       C(file.txt: line 1 = "Hi")

マージ時: Gitはどちらを採用すべきか判断できない → コンフリクト
```

### コンフリクトが発生する操作

- `git merge`
- `git rebase`
- `git cherry-pick`
- `git pull` (内部でmergeまたはrebaseを実行)

---

## コンフリクトの予防

### 1. こまめにマージ/リベース

```bash
# 定期的にmainの変更を取り込む
git checkout feature
git pull origin main  # または git rebase main
```

### 2. 小さな変更単位

```bash
# ✅ 良い: 1機能ずつ
git commit -m "Add login form"
git commit -m "Add validation"

# ❌ 悪い: 大きな変更を1度に
git commit -m "Add entire authentication system"
```

### 3. ファイルの責任を分ける

```bash
# ✅ 良い: 担当を分ける
person-a.js  # Aさんが担当
person-b.js  # Bさんが担当

# ❌ 悪い: 同じファイルを複数人が編集
shared.js  # 全員が編集 → コンフリクト多発
```

### 4. コミュニケーション

```bash
# チーム内で:
# - 誰がどのファイルを編集するか共有
# - 大きな変更は事前に相談
# - コードレビューで早期発見
```

---

## コンフリクトの解決方法

### ステップ1: コンフリクトの発生を確認

```bash
$ git merge feature
Auto-merging file.txt
CONFLICT (content): Merge conflict in file.txt
Automatic merge failed; fix conflicts and then commit the result.
```

### ステップ2: コンフリクトファイルを確認

```bash
$ git status
On branch main
You have unmerged paths.
  (fix conflicts and run "git commit")

Unmerged paths:
  (use "git add <file>..." to mark resolution)
	both modified:   file.txt
```

### ステップ3: コンフリクトマーカーを確認

```text
<<<<<<< HEAD (現在のブランチ)
Hello World
=======
Hi World
>>>>>>> feature (マージしようとしているブランチ)
```

**マーカーの意味:**
- `<<<<<<< HEAD`: 現在のブランチの内容の開始
- `=======`: 区切り
- `>>>>>>> feature`: マージブランチの内容の終了

### ステップ4: 手動で解決

#### オプション1: どちらか一方を選択

```text
# HEADを採用
Hello World

# または featureを採用
Hi World
```

#### オプション2: 両方を統合

```text
# 両方を含める
Hello World
Hi World
```

#### オプション3: 新しい内容に書き換え

```text
# まったく新しい内容
Greetings, World
```

### ステップ5: マーカーを削除

```text
# 修正前:
<<<<<<< HEAD
Hello World
=======
Hi World
>>>>>>> feature

# 修正後（マーカー削除）:
Hello World
```

### ステップ6: 解決済みとしてマーク

```bash
# ファイルをステージング
git add file.txt

# 状態を確認
git status
# All conflicts fixed but you are still merging.
```

### ステップ7: コミット

```bash
# マージの場合
git commit  # メッセージは自動生成される

# Rebaseの場合
git rebase --continue
```

---

## 高度なコンフリクト解決

### マージツールの使用

#### VS Code

VS Codeは組み込みのマージエディタを提供：

```bash
# VS Codeでファイルを開く
code file.txt
```

VS Codeのインターフェース：
- **Accept Current Change**: HEADを採用
- **Accept Incoming Change**: featureを採用
- **Accept Both Changes**: 両方を含める
- **Compare Changes**: 差分を比較

#### 他のマージツール

```bash
# マージツールを設定
git config --global merge.tool vimdiff
git config --global merge.tool meld
git config --global merge.tool kdiff3

# マージツールを起動
git mergetool

# 解決後、バックアップファイルを削除
git clean -f *.orig
```

### 3-way merge差分の理解

```
         Common Ancestor (B)
              /        \
           HEAD (C)   Feature (D)
              \        /
            Merge Result (E)
```

Gitは3つのバージョンを比較：
1. **Base**: 共通の祖先
2. **Ours**: 現在のブランチ (HEAD)
3. **Theirs**: マージしようとしているブランチ

### コンフリクト戦略の指定

```bash
# oursを優先（自分の変更を優先）
git merge -X ours feature

# theirsを優先（相手の変更を優先）
git merge -X theirs feature
```

**注意**: これは自動的に解決するため、慎重に使用してください。

---

## 複雑なコンフリクト

### 複数ファイルのコンフリクト

```bash
# コンフリクトが発生
$ git merge feature
CONFLICT (content): Merge conflict in file1.txt
CONFLICT (content): Merge conflict in file2.txt
CONFLICT (content): Merge conflict in file3.txt

# 1つずつ解決
vim file1.txt
git add file1.txt

vim file2.txt
git add file2.txt

vim file3.txt
git add file3.txt

# コミット
git commit
```

### バイナリファイルのコンフリクト

```bash
# 画像などのバイナリファイル
CONFLICT (content): Merge conflict in image.png

# どちらか一方を選択
git checkout --ours image.png   # HEADを採用
# または
git checkout --theirs image.png # featureを採用

# ステージング
git add image.png
```

### ファイル名の変更によるコンフリクト

```bash
# mainブランチ: file.txt
# featureブランチ: file.txt → renamed.txt

CONFLICT (rename/delete): file.txt deleted in feature and renamed in HEAD.

# 解決方法:
git rm file.txt
git add renamed.txt
git commit
```

---

## コンフリクト解決の実践

### 演習1: 基本的なコンフリクト解決

```bash
# 準備
git checkout main
echo "Version 1" > conflict.txt
git add conflict.txt
git commit -m "Add version 1"

# featureブランチで別の変更
git checkout -b feature
echo "Version 2" > conflict.txt
git add conflict.txt
git commit -m "Add version 2"

# mainでも変更
git checkout main
echo "Version 3" > conflict.txt
git add conflict.txt
git commit -m "Add version 3"

# マージしてコンフリクト発生
git merge feature
# CONFLICT!

# ファイルを開いて確認
cat conflict.txt
# <<<<<<< HEAD
# Version 3
# =======
# Version 2
# >>>>>>> feature

# 解決
echo "Version Final" > conflict.txt
git add conflict.txt
git commit -m "Resolve conflict"
```

### 演習2: 複数行のコンフリクト

```bash
# 準備
git checkout -b exercise2
cat > code.js <<'EOF'
function hello() {
    console.log("Hello");
}
EOF
git add code.js
git commit -m "Initial code"

# ブランチ1
git checkout -b branch1
cat > code.js <<'EOF'
function hello() {
    console.log("Hello World");
}
EOF
git add code.js
git commit -m "Branch1 changes"

# ブランチ2
git checkout exercise2
git checkout -b branch2
cat > code.js <<'EOF'
function hello() {
    console.log("Hi there");
}
EOF
git add code.js
git commit -m "Branch2 changes"

# マージ
git checkout branch1
git merge branch2
# CONFLICT!

# 解決: 両方の変更を統合
cat > code.js <<'EOF'
function hello() {
    console.log("Hello World");
    console.log("Hi there");
}
EOF
git add code.js
git commit
```

---

## トラブルシューティング

### Q: コンフリクトを解決したのにまだエラー

```bash
# すべてのファイルがステージングされているか確認
git status

# ステージングされていないファイルがある
git add <files>

# コミット
git commit
```

### Q: コンフリクト解決を間違えた

```bash
# マージを中止して最初からやり直す
git merge --abort

# Rebaseの場合
git rebase --abort
```

### Q: コンフリクトが多すぎて解決できない

```bash
# オプション1: マージを諦めてRebaseを試す
git merge --abort
git rebase main

# オプション2: 戦略を変える
git merge -X ours feature  # 自分の変更を優先

# オプション3: マージツールを使う
git mergetool
```

### Q: バックアップから復元したい

```bash
# マージ前の状態に戻る
git reset --hard ORIG_HEAD
```

---

## ベストプラクティス

### 1. 落ち着いて対処

```bash
# ✅ 手順:
1. git status で状況を確認
2. コンフリクトファイルを確認
3. 1つずつ解決
4. テストを実行
5. コミット
```

### 2. チームと相談

```bash
# 複雑なコンフリクトは:
# - 関係者と相談
# - ペアプログラミングで解決
# - レビューを受ける
```

### 3. テストを実行

```bash
# コンフリクト解決後は必ずテスト
npm test
git commit
```

### 4. 小さく、頻繁にマージ

```bash
# こまめにマージしてコンフリクトを小さく保つ
git fetch origin
git merge origin/main
```

---

## チェックリスト

- [ ] コンフリクトの原因を理解している
- [ ] コンフリクトマーカーを読める
- [ ] 基本的なコンフリクトを解決できる
- [ ] マージツールを使える
- [ ] コンフリクトを予防する方法を知っている

---

## 次のステップ

👉 [04. 高度なコマンド](./04_advanced_commands.md)

---

## 参考資料

- [Pro Git - Basic Merge Conflicts](https://git-scm.com/book/ja/v2/Git-%E3%81%AE%E3%83%96%E3%83%A9%E3%83%B3%E3%83%81%E6%A9%9F%E8%83%BD-%E3%83%96%E3%83%A9%E3%83%B3%E3%83%81%E3%81%A8%E3%83%9E%E3%83%BC%E3%82%B8%E3%81%AE%E5%9F%BA%E6%9C%AC)
- [GitHub Docs - Resolving merge conflicts](https://docs.github.com/ja/pull-requests/collaborating-with-pull-requests/addressing-merge-conflicts)
