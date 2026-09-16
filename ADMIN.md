# 管理画面(活動報告の編集)セットアップ手順

`/admin/` にアクセスすると、Markdownやgitの知識がなくても、画面上のフォームから
活動報告の作成・編集ができる管理画面([Decap CMS](https://decapcms.org/))が使えます。

すでにセットアップ済みの場合は、下記の「4. 管理画面を使う」だけ読めばOKです。
初回セットアップ(最初の1人だけ)は 1〜3 の作業が必要です。

---

## 1. GitHub OAuth Appを作成する

管理画面はGitHubアカウントでログインします。そのための「鍵」を発行します。

1. GitHubの **Settings** → 左メニュー最下部 **Developer settings** → **OAuth Apps** → **New OAuth App**
2. 以下を入力(Worker URLは手順2で発行されるので、後で戻って修正してもOK)
   - Application name: 任意(例: `sapporo-konan-rotaract admin`)
   - Homepage URL: `https://sapporokonan-rotaract.github.io/admin/`
   - Authorization callback URL: `https://<あなたのWorker名>.workers.dev/callback`(手順2の後で確定)
3. 作成後に表示される **Client ID** と、**Generate a new client secret** で発行される **Client Secret** を控えておく
   (Client Secretは一度しか表示されないので、必ずこの時点でコピーしてください)

## 2. OAuth仲介サーバーをCloudflare Workerとしてデプロイする

GitHub Pagesは静的サイトのため、OAuth認証のやり取りを行うサーバーが別途必要です。
無料のCloudflare Workersを使って、[ottmartens/decap-cms-github-oauth-provider-cloudflare](https://github.com/ottmartens/decap-cms-github-oauth-provider-cloudflare)
という既製のプロキシをデプロイします。

1. 上記リポジトリを自分のGitHubアカウントに **Fork** する
2. [Cloudflareアカウント](https://dash.cloudflare.com/)を作成する(無料)
3. ローカルPCで `wrangler` CLI をインストールしてログインする
   ```bash
   npm install --global wrangler
   wrangler login
   ```
4. Forkしたリポジトリを `git clone` し、`wrangler.toml` の `account_id` を自分のIDに書き換える
   (`wrangler whoami` で確認できます)
5. 以下のコマンドで、手順1で控えたClient ID/Secretをシークレットとして登録する
   ```bash
   wrangler secret put CLIENT_ID
   wrangler secret put CLIENT_SECRET
   ```
6. デプロイする
   ```bash
   wrangler deploy
   ```
   成功すると `https://<worker名>.workers.dev` のようなURLが発行されます。
7. 手順1のGitHub OAuth Appに戻り、**Authorization callback URL** を
   `https://<worker名>.workers.dev/callback` に確定させる

(GitHub Actionsで自動デプロイしたい場合は、Cloudflareの「Edit Cloudflare Workers」権限の
APIトークンを発行し、Forkしたリポジトリの Settings → Secrets → Actions に登録してください。
詳細はFork元リポジトリのREADMEを参照。)

## 3. サイト側の設定を書き換える

[admin/config.yml](admin/config.yml) の `base_url` を、手順2で発行されたWorkerのURLに書き換えて
`main` にpushしてください。

```yaml
backend:
  name: github
  repo: sapporokonan-rotaract/sapporokonan-rotaract.github.io
  branch: main
  base_url: https://<worker名>.workers.dev
```

## 4. 管理画面を使う

1. `https://sapporokonan-rotaract.github.io/admin/` にアクセス
2. 「Login with GitHub」→ このリポジトリにアクセス権のあるGitHubアカウントでログイン
3. 左メニューの **活動報告** から
   - 一覧に並んでいる既存の記事をクリックすると編集画面が開きます
   - 右上の **New 活動報告** で新規作成できます
4. タイトル・アイキャッチ画像(任意)・本文・公開日を入力し、右上の **Publish**(または下書き保存の
   **Save**)を押すと、`_posts/` にMarkdownファイルが作成され、`main` ブランチへ自動でコミットされます
5. 数分後、GitHub Actionsのビルドが終わるとサイトに反映されます(Actionsタブで進捗確認可)

管理画面を使えるのは、このリポジトリの **Collaborator** に招待されたGitHubアカウントのみです。
役員交代時はリポジトリの Settings → Collaborators and teams から追加・削除してください。
