# 管理画面(活動報告の編集)セットアップ手順

`/admin/` にアクセスすると、Markdownやgitの知識がなくても、画面上のフォームから
活動報告の作成・編集ができる管理画面([Decap CMS](https://decapcms.org/))が使えます。

**セットアップは完了済みです。** 普段は下記の「4. 管理画面を使う」だけ読めばOKです。
1〜3 は、OAuth AppやCloudflare Workerを作り直す必要が出たとき(乗っ取り対応や作り直しなど)のための記録です。

- 使用しているGitHub OAuth App: Organization `sapporokonan-rotaract` の Developer settings → OAuth Apps →
  `sapporo-konan-rotaract admin`(`https://github.com/organizations/sapporokonan-rotaract/settings/applications`)
- OAuth仲介用Cloudflare Worker: `https://decap-cms-github-oauth-api.decap-cms-github-oauth-api-cloudflare-worker.workers.dev`
  (Fork元コードは `https://github.com/sapporokonan-rotaract/decap-cms-github-oauth-provider-cloudflare`)

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

## 5. うまくいかないときに実際に起きたトラブルと対処

セットアップ時に以下でつまずいたので、同じ状況になったら参考にしてください。

- **`'collections[0].meta' must be object`**: 旧Netlify CMSの`meta:`配列構文は現在のDecap CMSでは廃止。
  日付は`meta`ではなく通常の`fields`に`widget: datetime`として入れる。
- **Cloudflareアカウント作成直後、確認メールが届かない/確認してもWorkerデプロイが
  `You need to verify your email address to use Workers`(code 10034)で失敗し続ける**:
  ダッシュボード上は確認済みに見えても、API側の判定が追いつかない既知の不具合が起きることがある。
  ダッシュボードの画面から直接Workerを1つ作ってみる(Compute → Workers → Create)と、
  それをきっかけに解消することがあった。
- **デプロイは成功するが `/auth` にアクセスすると `client_id=` が空でGitHubが404になる**:
  `wrangler secret put CLIENT_ID` / `CLIENT_SECRET` を対話的に(値を手打ち・貼り付けで)実行すると、
  なぜか空文字列が登録されてしまうことがあった。
  `printf '%s' "$VALUE" | wrangler secret put CLIENT_ID` のように標準入力へパイプする形で
  実行し直したら解決した。
- **`Authorization callback URL`を仮の値のまま忘れると、ログイン時に
  `placeholder.workers.dev` で `DNS_PROBE_FINISHED_NXDOMAIN`**:
  GitHub OAuth Appの設定画面の「Redirect URIs」を、実際のWorker URL + `/callback` に
  更新するのを忘れずに。
