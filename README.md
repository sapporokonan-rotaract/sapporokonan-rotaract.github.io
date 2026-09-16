# 札幌江南ロータアクトクラブ 公式サイト

Jekyll + GitHub Pages で構築した、完全無料・複数人編集可能な公式サイトです。

- 公開URL: `https://<Organization名>.github.io/`(GitHub公開後に確定します)
- 使用技術: [Jekyll](https://jekyllrb.com/)(静的サイトジェネレーター)
- ホスティング: GitHub Pages(無料)
- 自動公開: GitHub Actions(`main`ブランチにpushすると自動でビルド・公開)

---

## 1. 初回セットアップ(最初の1人だけ行う作業)

### 1-1. GitHub Organization を作成する

個人アカウントではなく Organization を使うことで、役員交代があっても
特定の個人にサイトの所有権が縛られません。

1. GitHubにログイン → 右上の「+」→「New organization」
2. プラン選択で **Free** を選択
3. Organization名を決める(例: `sapporo-konan-rotaract`)

### 1-2. リポジトリを作成する

Organization のトップページを直接そのURLで公開したい場合、リポジトリ名を
**`<Organization名>.github.io`** にしてください(例: `sapporo-konan-rotaract.github.io`)。
この名前で作成すると、公開URLが `https://sapporo-konan-rotaract.github.io/` になります。

### 1-3. このフォルダの中身をpushする

このフォルダ(`sapporo-konan-rotaract`)の中身一式を、作成したリポジトリにpushします。

```bash
cd sapporo-konan-rotaract
git init
git add .
git commit -m "Initial commit: サイトの雛形を作成"
git branch -M main
git remote add origin https://github.com/<Organization名>/<リポジトリ名>.git
git push -u origin main
```

### 1-4. `_config.yml` のURLを実際のものに書き換える

`_config.yml` の `url:` を、実際に作成したURLに書き換えて再度pushしてください。

```yaml
url: "https://sapporo-konan-rotaract.github.io"
```

### 1-5. GitHub Pages の公開設定をする

1. リポジトリの **Settings** → 左メニュー **Pages**
2. 「Build and deployment」の **Source** を **GitHub Actions** に設定
3. `main` にpushすると、自動でビルド・公開が始まります(Actionsタブで進捗確認できます)
4. 数分後、公開URLにアクセスしてサイトが表示されればOKです

---

## 2. メンバーを招待する(複数人での編集)

1. リポジトリの **Settings** → **Collaborators and teams**
2. 編集してほしいメンバーをGitHubアカウントで招待
3. 招待されたメンバーはリポジトリを `git clone` して編集できます

役員が交代したら、Organizationの **People** からメンバーの追加・削除を行ってください。

---

## 3. サイトの編集方法

### 3-0. 管理画面から活動報告を作成・編集する(おすすめ)

`https://sapporokonan-rotaract.github.io/admin/` にアクセスすると、
Markdownやgitの知識がなくても、画面上のフォームから活動報告の作成・編集ができます。
初回セットアップ(OAuth連携)が必要です。詳しくは [ADMIN.md](ADMIN.md) を参照してください。

### 3-1. 新しい活動報告(ブログ記事)を手動で追加する

管理画面を使わず、直接Markdownファイルを追加することもできます。
`_posts` フォルダに、以下の形式でMarkdownファイルを追加してください。

- ファイル名: `YYYY-MM-DD-好きなタイトル.md`(例: `2026-06-15-summer-volunteer.md`)
- 中身:

```markdown
---
title: "夏の奉仕活動を行いました"
---

ここに本文を書きます。見出しや画像も使えます。

## 活動概要

- 日時: 2026年6月15日
- 場所: 〇〇公園
```

保存して`main`にpushすれば、数分後に「活動報告」ページとトップページに自動で反映されます。
サンプル記事(`【サンプル】`から始まるもの)は、実際の記事を追加したら削除してください。

### 3-2. 固定ページを編集する

各ページはルート直下のMarkdownファイルです。

| ページ | ファイル |
| --- | --- |
| ホーム | `index.md` / `_layouts/home.html` |
| クラブ紹介 | `about.md` |
| 活動報告一覧 | `activities.md` |
| 例会・行事予定 | `events.md` |
| 入会案内 | `join.md` |
| お問い合わせ | `contact.md` |

`＜　＞`で囲まれた部分は仮のプレースホルダーです。実際のクラブ情報に書き換えてください。

### 3-3. 連絡先・SNSリンクを設定する

`_config.yml` の `contact:` にメールアドレスやSNSのURLを入力すると、
フッターとお問い合わせページに自動で表示されます。

```yaml
contact:
  email: "info@example.com"
  instagram: "https://www.instagram.com/xxxx"
  x: "https://x.com/xxxx"
  facebook: "https://www.facebook.com/xxxx"
```

### 3-4. お問い合わせフォームを設置したい場合

GitHub Pagesは静的サイトのため、フォームの送信内容をサーバー側で受け取ることができません。
無料の **Googleフォーム** を作成し、`contact.md` 内のコメントを参考に埋め込むことをおすすめします。

---

## 4. ローカルで見た目を確認する(任意)

pushする前に手元のPCでプレビューしたい場合。

```bash
# 初回のみ
bundle install

# プレビュー起動(http://localhost:4000 で確認できます)
bundle exec jekyll serve
```

Rubyの環境構築が難しい場合は、GitHub上でファイルを直接編集して`main`にpushし、
Actionsのビルド結果を確認する運用でも問題ありません。

---

## 5. 複数人での編集ルール(おすすめ)

- 誤って公開中のサイトを壊さないよう、できれば以下の流れを推奨します。
  1. `main`からブランチを作成(例: `git checkout -b add-june-activity`)
  2. 編集してpush
  3. GitHub上で **Pull Request** を作成
  4. 他のメンバーが確認して**Merge**する
- 慣れないうちは、直接`main`にpushする運用でも問題ありません(小規模クラブ運営では現実的な選択です)。

---

## 6. 困ったときは

- **サイトが更新されない** → リポジトリの **Actions** タブでビルドが失敗していないか確認
- **画像を追加したい** → `assets/images/` にファイルを置き、記事から `![説明](/assets/images/ファイル名.jpg)` の形式で参照
- **独自ドメインにしたい場合**(例: `sapporo-konan-rotaract.jp`) → ドメイン取得費用は有料になりますが、GitHub Pages自体の設定変更は無料でできます(`Settings > Pages > Custom domain`)
