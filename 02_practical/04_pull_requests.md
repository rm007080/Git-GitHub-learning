# プルリクエスト

## 目次
1. [プルリクエストとは](#プルリクエストとは)
2. [プルリクエストの作成](#プルリクエストの作成)
3. [コードレビュー](#コードレビュー)
4. [プルリクエストのマージ](#プルリクエストのマージ)
5. [実践演習](#実践演習)

---

## プルリクエストとは

### 概要

**プルリクエスト（Pull Request、PR）**は、自分の変更を他の人にレビューしてもらい、メインブランチにマージしてもらうための仕組みです。

```
あなた:   feature branch で開発
           ↓
        Pull Request を作成
           ↓
レビュアー: コードレビュー
           ↓
        承認 & マージ
           ↓
        main branch に統合
```

### プルリクエストのメリット

1. **コードレビュー**
   - バグを早期発見
   - コード品質の向上
   - 知識の共有

2. **議論の場**
   - 実装方法について相談
   - 質問と回答
   - 改善提案

3. **履歴の記録**
   - なぜこの変更が必要だったか
   - どのような議論があったか
   - 誰がレビューしたか

4. **自動化**
   - CI/CDの実行
   - 自動テスト
   - コードカバレッジチェック

---

## プルリクエストの作成

### ステップ1：ブランチで開発

```bash
# 1. 最新のmainを取得
git checkout main
git pull origin main

# 2. 新しいブランチを作成
git checkout -b feature/user-profile

# 3. 開発作業
# （ファイルを編集）

# 4. コミット
git add .
git commit -m "Add user profile page"

# 5. リモートにプッシュ
git push -u origin feature/user-profile
```

### ステップ2：GitHubでPRを作成

#### 方法1：GitHub Webインターフェース

1. GitHubのリポジトリページにアクセス
2. 「Compare & pull request」ボタンをクリック
3. または、「Pull requests」タブ → 「New pull request」

#### PR作成画面で入力

```markdown
# タイトル
ユーザープロフィールページを追加

# 説明
## 概要
ユーザーが自分のプロフィールを表示・編集できるページを追加しました。

## 変更内容
- プロフィール表示コンポーネントを追加
- プロフィール編集フォームを実装
- APIエンドポイントを追加

## スクリーンショット
（画像を添付）

## テスト
- [ ] ユニットテストが通過
- [ ] 手動テスト完了
- [ ] レスポンシブ対応確認

## 関連Issue
Closes #123
```

### 良いPR説明の書き方

#### タイトル

```bash
# 良い例
feat: ユーザープロフィールページを追加
fix: ログインフォームのバリデーションを修正
docs: READMEにインストール手順を追加

# 悪い例
更新
修正
WIP
```

#### 説明文のテンプレート

```markdown
## 何をしたか
- 変更の概要を簡潔に

## なぜこの変更が必要か
- 背景や理由

## どうやって確認したか
- テスト方法
- 動作確認の手順

## スクリーンショット（必要に応じて）
（画像やGIFを添付）

## 関連Issue
Closes #123
Refs #456
```

### PRのラベル・担当者・レビュアー

#### ラベル
- `bug`: バグ修正
- `enhancement`: 機能追加
- `documentation`: ドキュメント
- `WIP`: 作業中（Work In Progress）

#### 担当者（Assignees）
- PRを作成した人（自分）

#### レビュアー（Reviewers）
- コードレビューをお願いする人

---

## コードレビュー

### レビュアーの役割

1. **コードの確認**
   - ロジックは正しいか
   - バグはないか
   - パフォーマンスに問題はないか

2. **コーディング規約のチェック**
   - スタイルガイドに従っているか
   - 命名規則は適切か

3. **提案とフィードバック**
   - より良い実装方法の提案
   - 質問や確認事項

### レビューの種類

#### 1. Comment（コメント）
- 一般的なコメント
- 質問や議論

#### 2. Approve（承認）
- 問題なし、マージOK
- ✅マークが付く

#### 3. Request Changes（変更要求）
- 修正が必要
- マージブロック

### レビューのベストプラクティス

#### レビュアーとして

```markdown
# 良いコメント
この実装だと、ユーザー数が増えたときにパフォーマンスに影響がありそうです。
キャッシュを使うのはいかがでしょうか？

# 悪いコメント
これはダメです
なんでこうしたの？
```

**ポイント**:
- 建設的なフィードバック
- 理由を説明する
- 代替案を提示する
- 敬意を持つ

#### 作者として

```markdown
# レビューコメントへの返信
ご指摘ありがとうございます！
キャッシュの実装を追加しました。
コミット abc123 をご確認ください。
```

**ポイント**:
- 感謝の気持ちを伝える
- 素早く対応する
- 不明点は質問する
- 議論を恐れない

### GitHubのレビュー機能

#### インラインコメント

```diff
# 特定の行にコメント
+ function calculateTotal(items) {
+   return items.reduce((sum, item) => sum + item.price, 0);
+ }

💬 レビュアー: 小数点の扱いはどうなっていますか？
   price が小数の場合、丸め誤差が出る可能性があります。
```

#### サジェスト機能

```diff
- const result = data.map(x => x.value).filter(x => x > 0);
+ const result = data
+   .filter(item => item.value > 0)
+   .map(item => item.value);

💬 レビュアー: [Suggest] フィルターを先にすると効率的です
```

---

## プルリクエストのマージ

### マージの種類

#### 1. Create a merge commit
通常のマージコミットを作成。

```
main: A---B---C-----------M
           \             /
feature:    D-----E-----F
```

```bash
# コマンドライン
git checkout main
git merge --no-ff feature/user-profile
```

#### 2. Squash and merge
複数のコミットを1つにまとめる。

```
main: A---B---C---D'
           \
feature:    D---E---F (統合される)
```

**メリット**:
- 履歴がシンプル
- 1機能 = 1コミット

**使用場面**:
- 細かいコミットが多い時
- 履歴をきれいに保ちたい時

#### 3. Rebase and merge
リベースしてからマージ。

```
main: A---B---C---D'---E'---F'
```

**メリット**:
- 直線的な履歴
- マージコミットなし

**デメリット**:
- コミットハッシュが変わる

### マージのワークフロー

```bash
# 1. レビューで承認される
# 2. CI/CDテストが通過
# 3. コンフリクトがない
# 4. マージボタンをクリック
# 5. ブランチを削除（オプション）
```

### マージ後のクリーンアップ

```bash
# 1. ローカルのmainを更新
git checkout main
git pull origin main

# 2. マージ済みブランチを削除
git branch -d feature/user-profile

# 3. リモートブランチも削除（GitHub上で削除していない場合）
git push origin --delete feature/user-profile

# 4. リモート追跡ブランチをクリーンアップ
git fetch --prune
```

---

## Draft Pull Request

### Draft PRとは

レビュー前の「下書き」状態のPR。

**使用場面**:
- 作業中の進捗を共有したい
- 早めにフィードバックが欲しい
- CI/CDで動作確認したい

```markdown
# Draft PRのタイトル例
[WIP] ユーザープロフィールページを追加
Draft: ログイン機能の実装中
```

### Draft PRの作成

GitHub上で:
1. PR作成時に「Create draft pull request」を選択
2. または、既存のPRを「Convert to draft」

### Draft → Ready for Review

```markdown
作業が完了したら:
1. 「Ready for review」ボタンをクリック
2. レビュアーに通知される
```

---

## 実践演習

### 演習1：プルリクエストの作成

```bash
# 1. 新しいブランチを作成
git checkout -b feature/add-contact-form

# 2. ファイルを作成
cat > contact.html <<EOF
<!DOCTYPE html>
<html>
<head>
    <title>Contact</title>
</head>
<body>
    <form>
        <input type="text" name="name" placeholder="Name">
        <input type="email" name="email" placeholder="Email">
        <textarea name="message" placeholder="Message"></textarea>
        <button type="submit">Send</button>
    </form>
</body>
</html>
EOF

# 3. コミット
git add contact.html
git commit -m "feat: Add contact form"

# 4. プッシュ
git push -u origin feature/add-contact-form

# 5. GitHub上でPRを作成
# （ブラウザでGitHubを開く）
```

### 演習2：コードレビューの実践

**シナリオ**: チームメンバーのPRをレビューする

1. **PRを開く**
   - 「Files changed」タブをクリック

2. **コードを確認**
   - 行をクリックしてコメント
   - 改善提案をする

3. **レビューを提出**
   - 「Review changes」をクリック
   - Comment / Approve / Request changes を選択

4. **作者として対応**
   - コメントに返信
   - 必要に応じて修正
   - 追加コミット

---

## よくあるシナリオ

### シナリオ1：コンフリクトの解決

PRでコンフリクトが発生した場合:

```bash
# 1. mainの最新を取得
git checkout feature/your-branch
git fetch origin
git merge origin/main

# 2. コンフリクトを解決
# （ファイルを編集）

# 3. コミット
git add .
git commit -m "Resolve merge conflicts"

# 4. プッシュ
git push origin feature/your-branch
```

### シナリオ2：レビュー後の修正

```bash
# 1. レビューコメントに基づいて修正
# （ファイルを編集）

# 2. 追加コミット
git add .
git commit -m "fix: Address review comments"

# 3. プッシュ（PRは自動更新される）
git push origin feature/your-branch
```

### シナリオ3：PRを作り直す

```bash
# 間違えて作成したPRをクローズして作り直す
# 1. GitHub上でPRをClose

# 2. ブランチを削除
git branch -D wrong-branch
git push origin --delete wrong-branch

# 3. 正しいブランチで作り直す
git checkout -b correct-branch
# ...作業...
git push -u origin correct-branch
```

---

## プルリクエストのチェックリスト

PRを作成する前に確認:

- [ ] コミットメッセージは明確か
- [ ] テストは通過しているか
- [ ] コードは自分でレビューしたか
- [ ] PR説明は十分に詳しいか
- [ ] 関連Issueをリンクしたか
- [ ] レビュアーを指定したか
- [ ] CI/CDが成功しているか

---

## ベストプラクティス

### 1. 小さなPR

```bash
# 良い例：1つの機能に集中
feat: Add login form

# 悪い例：複数の機能を混ぜる
feat: Add login, signup, and profile pages
```

### 2. わかりやすい説明

- 何を変更したか
- なぜ変更したか
- どう確認したか

### 3. 早めのレビュー依頼

- Draft PRで進捗共有
- 大きな変更は設計段階で相談

### 4. 素早い対応

- レビューコメントには24時間以内に返信
- 修正も素早く対応

---

## チェックリスト

- [ ] プルリクエストを作成できる
- [ ] 良いPR説明を書ける
- [ ] コードレビューができる
- [ ] レビューコメントに対応できる
- [ ] マージとクリーンアップができる

---

## 次のステップ

実践編をマスターしたら、応用編でより高度なテクニックを学びましょう！

👉 [応用編へ進む](../03_advanced/01_gitflow.md)

---

## 参考資料

- [GitHub Docs - プルリクエストについて](https://docs.github.com/ja/pull-requests)
- [The Art of Pull Requests](https://hackernoon.com/the-art-of-pull-requests-6f0f099850f9)
- [Code Review Best Practices](https://google.github.io/eng-practices/review/)
