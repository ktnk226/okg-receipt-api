# レシート解析APIの公開

GitHub Pagesは画面を配信し、このWorkerがGeminiへ接続します。キーはHTMLに入れません。

## 初回設定（Cloudflareアカウントが必要）

このディレクトリで以下を実行します。キーはコマンドの引数やGitに書かず、各コマンドの非表示入力に貼り付けてください。

```sh
npx wrangler login
npx wrangler secret put GEMINI_API_KEY
npx wrangler secret put RECEIPT_ACCESS_TOKEN
npx wrangler deploy
```

GEMINI_API_KEYはGoogle AI Studioで新規発行したキーです。Google側でGenerative Language APIへのAPI制限を設定してください。
RECEIPT_ACCESS_TOKENにはパスワード管理ツール等で生成した32文字以上のランダムな利用パスワードを設定します。Googleのキーとは別の値です。
モデルが利用できない場合はwrangler.jsoncのGEMINI_MODELを利用可能なモデルに変更します。

公開後、レシート画面の「AI解析の接続設定」にWorkerのHTTPS URL（末尾/analyzeは不要）と利用パスワードを入力します。URLのみブラウザに保存され、パスワードは画面を閉じる・更新すると消えます。Googleのキーは画面に入力しません。
画像は解析時にWorker経由でGoogleへ送信されます。Workerは画像や解析結果を保存・ログ出力しません。

接続元制限に加えパスワード認証と毎分10回の利用制限があります。Cloudflareのレート制限は概算であり、厳密な課金上限ではありません。Google Cloud側でも利用枠と請求アラートを設定してください。

## 検証

```sh
node --test worker.test.mjs
```

実APIの確認は設定後にJPEG・PNGで日付、金額、店名を確認し、不正パスワード・読み取り不能画像も確認します。自動テストはGoogleに通信しません。
