# Git/GitHub 完全ガイド

初学者から上級者まで、Git/GitHubを体系的に学べる教科書的な資料です。

参照元：https://git.mylastwork.net/

---

## この資料について

このリポジトリは、Git/GitHubを**ゼロから学びたい初学者**のために作成された、包括的な学習資料です。基礎から応用まで、実践的な例とともに詳しく解説しています。

### 特徴

- 初学者にも分かりやすい丁寧な説明
- 実際に手を動かせる演習問題
- 図解とコード例が豊富
- 実務で使える実践的な内容
- 日本語で詳しく解説

---

## 目次

### 基礎編（初級）

Gitの基本概念とコマンド習得

1. [Gitとは？](./01_basics/01_what_is_git.md) - バージョン管理の基本概念を理解する（推定時間15分）
2. [環境設定](./01_basics/02_setup.md) - Gitのインストールと初期設定（推定時間20分）
3. [基本コマンド](./01_basics/03_basic_commands.md) - add、commit、statusなどの基本操作（推定時間30分）

### 実践編（中級）

GitHubを使った実践的開発手法

1. [GitHubの基礎](./02_practical/01_github_basics.md) - GitHubアカウント作成、SSH設定、リポジトリ管理
2. [リモートリポジトリの操作](./02_practical/02_remote_repository.md) - push、pull、fetch、cloneの使い方
3. [ブランチとマージ](./02_practical/03_branches_and_merge.md) - ブランチ戦略、マージ、コンフリクト解決
4. [プルリクエスト](./02_practical/04_pull_requests.md) - PRの作成、コードレビュー、マージ

### 応用編（上級）

GitFlowと高度なブランチ戦略

1. [GitFlow](./03_advanced/01_gitflow.md) - 大規模プロジェクトのブランチ戦略
2. [Rebase](./03_advanced/02_rebase.md) - 履歴の書き換えと整理
3. [コンフリクトの解決](./03_advanced/03_conflict_resolution.md) - 高度なコンフリクト解決テクニック
4. [高度なコマンド](./03_advanced/04_advanced_commands.md) - stash、cherry-pick、reset、reflog、bisect

### リソース

- [チートシート](./resources/cheatsheet.md) - よく使うコマンドのクイックリファレンス
- [トラブルシューティング](./resources/troubleshooting.md) - よくある問題と解決方法
- [役立つリンク集](./resources/useful_links.md) - 学習リソース、ツール、コミュニティ

---

## 学習の進め方

### 初学者の方

1. **基礎編から順番に読む**
   - まずは[Gitとは？](./01_basics/01_what_is_git.md)から始めましょう
   - 各章の演習を必ず実践してください
   - 理解できるまで次に進まないことが重要です

2. **手を動かして学ぶ**
   ```bash
   # 実際にコマンドを打ってみる
   git init
   git add .
   git commit -m "初めてのコミット"
   ```

3. **分からないことはすぐ調べる**
   - [チートシート](./resources/cheatsheet.md)を活用
   - [トラブルシューティング](./resources/troubleshooting.md)を参照

### 中級者の方

- 実践編から読み始めることをおすすめします
- GitHubを使った開発フローを学びましょう
- チーム開発を想定した練習をしてみましょう

### 上級者の方

- 応用編で高度なテクニックを学びましょう
- GitFlowやRebaseを実際のプロジェクトで活用してみましょう
- [役立つリンク集](./resources/useful_links.md)から更なる学習リソースを探索

---

## 推奨学習時間

| レベル | 内容 | 推定時間 |
|--------|------|----------|
| 基礎編 | Git の基本 | 2-3時間 |
| 実践編 | GitHub の使い方 | 3-4時間 |
| 応用編 | 高度なテクニック | 4-5時間 |
| **合計** | | **9-12時間** |

---

## ディレクトリ構成

```
Git-GitHub-learning/
├── README.md                          # このファイル
├── 01_basics/                         # 基礎編
│   ├── 01_what_is_git.md
│   ├── 02_setup.md
│   ├── 03_basic_commands.md
│   └── exercises/
├── 02_practical/                      # 実践編
│   ├── 01_github_basics.md
│   ├── 02_remote_repository.md
│   ├── 03_branches_and_merge.md
│   ├── 04_pull_requests.md
│   └── exercises/
├── 03_advanced/                       # 応用編
│   ├── 01_gitflow.md
│   ├── 02_rebase.md
│   ├── 03_conflict_resolution.md
│   ├── 04_advanced_commands.md
│   └── exercises/
└── resources/                         # リソース
    ├── cheatsheet.md
    ├── troubleshooting.md
    └── useful_links.md
```

---

## 前提知識

この資料を学ぶのに必要な前提知識：

- **必須**:
  - コマンドライン（ターミナル）の基本操作
  - テキストエディタの使い方

- **推奨**（あれば望ましい）:
  - プログラミングの基礎知識
  - ファイルシステムの理解

---

## 動作環境

- **OS**: Windows、macOS、Linux
- **Git**: バージョン 2.x 以降
- **ブラウザ**: 最新版の Chrome、Firefox、Safari、Edge
- **エディタ**: VS Code、Vim、Emacs など（お好みで）

---

## よくある質問

### Q: プログラミング初心者でも学べますか？

**A:** はい！この資料は初学者向けに作られています。コマンドラインの基本操作ができれば問題ありません。

### Q: どのくらいの時間で学べますか？

**A:** 基礎から応用まで、約10-12時間で一通り学べます。ただし、実践的に使いこなせるようになるには、日々の開発で使い続けることが重要です。

### Q: GitHubアカウントは必要ですか？

**A:** 実践編以降を学ぶには必要です。無料で作成できます：https://github.com/

### Q: コマンドラインが苦手です...

**A:** 最初は難しく感じるかもしれませんが、この資料では丁寧に解説しています。また、GUI ツール（GitHub Desktop、SourceTree等）の使用も検討できます。

---

## 貢献について

この資料への貢献を歓迎します！

- 誤字脱字の修正
- 内容の改善提案
- 新しい章の追加
- 演習問題の追加

プルリクエストをお待ちしています。

---

## おすすめの学習リソース

### 公式ドキュメント

- [Pro Git Book（日本語版）](https://git-scm.com/book/ja/v2) - 完全無料のGit解説書
- [GitHub Docs](https://docs.github.com/ja) - GitHub公式ドキュメント

### インタラクティブ学習

- [Learn Git Branching](https://learngitbranching.js.org/?locale=ja) - ビジュアルでGitを学ぶ
- [GitHub Skills](https://skills.github.com/) - GitHub公式の学習コース

### コミュニティ

- [Stack Overflow - Git タグ](https://stackoverflow.com/questions/tagged/git)
- [GitHub Community](https://github.community/)
- [Qiita - Git タグ](https://qiita.com/tags/git)

詳しくは[役立つリンク集](./resources/useful_links.md)をご覧ください。

---

## ライセンス

この資料はMIT Licenseのもとで公開されています。自由に学習・共有・改変していただけます。

---

## クレジット

参照元：https://git.mylastwork.net/

---

## 始めましょう！

準備ができたら、[Gitとは？](./01_basics/01_what_is_git.md)から学習を始めましょう！

Happy Learning! 🚀
