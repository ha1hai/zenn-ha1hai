---
title: "Google App Script (GAS) を利用して、LINE のメッセージを Discord へ送信する"
emoji: "💬"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: [line, discord, gas]
published: false
---

## はじめに

Google App Script (GAS) を利用して、LINE チャネルに入力したメッセージを Discord へ送信します。

## 事前準備

- Google App Script (GAS) が作成できること
- LINE チャネルが作成できること
- Discord の Webhook URL が作成済であること

## GAS プロジェクトの作成

[Apps Script](https://script.google.com/home) へアクセスします。  
左上にある 新しいプロジェクト をクリックします。  
![](/images/2024-06-19/001.png)

新規プロジェクトが作成されます。 `無題のプロジェクト` をクリックし、プロジェクト名を `line-message-to-discord` へ変更します。
![](/images/2024-06-19/002.png)
![](/images/2024-06-19/003.png)
![](/images/2024-06-19/004.png)

## GAS スクリプトプロパティ 設定

GAS スクリプトプロパティ（環境変数的なやつ）へ Discord の Webhook URL を登録します。  
Discord の Webhook URL を変更した際にコードを直接修正しなくてもいいように、スクリプトプロパティの設定値から値を取得します。

- スクリプトプロパティ設定値
  - `DISCORD_WEBHOOK_URL`: {Discord の Webhook URL}  

サイドメニューから プロジェクトの設定 を選択します。
![](/images/2024-06-19/005.png)

スクリプトプロパティを追加 から設定します。
![](/images/2024-06-19/006.png)

プロパティへ `DISCORD_WEBHOOK_URL` を、値へ `{自分のDiscord Webhook URL}` を入力し、スクリプトプロパティを保存します。
![](/images/2024-06-19/007.png)

## GAS スクリプト作成

コードを修正するために、エディタ へ戻ります。
![](/images/2024-06-19/008.png)

コードの既存の内容を削除し、新しいコードを貼り付けます。
![](/images/2024-06-19/009.png)
![](/images/2024-06-19/010.png)

以下のコードを貼り付けます。

```javascript
// POSTリクエストを受診すると実行される関数
function doPost(e) {

  const postData = JSON.parse(e.postData.contents).events[0];
  const lineMessageText = postData.message.text; // メッセージテキスト

  // Discordへメッセージを送信
  sendDiscordMessage(JSON.stringify(lineMessageText));

  // レスポンスを返す（LINE側では、後続処理に利用されない。HTTPステータス200だったら何でもOK?）
  return ContentService.createTextOutput(JSON.stringify({
    'message': 'Message received'
  })).setMimeType(ContentService.MimeType.JSON);
}

// DiscordのWebhook URLへメッセージを送信する関数
function sendDiscordMessage(message) {

  const payload = { 
    'content': message
  };
  const options = {
    'method': 'post',
    'headers': {
      'Content-Type': 'application/json'
    },
    'payload': JSON.stringify(payload),
  };

  // 環境変数からURLを取得
  const url = PropertiesService.getScriptProperties().getProperty('DISCORD_WEBHOOK_URL');
  const response = UrlFetchApp.fetch(url, options);
  const statusCode = response.getResponseCode();
  // Discordは返却するコンテンツがないときは正常系で204を返してくる
  if (statusCode !== 204) {
    console.error('Failed to send message to Discord. Status code: ' + statusCode + ', Response body: ' + response.getContentText());
  }
}
```

### コードの概要説明

LINE から GAS へメッセージが送信されると、 `doPost(e)` が実行されます。`e` から LINE のメッセージを取り出し、 `sendDiscordMessage(message)` で Discord へ転送します。  
スクリプトプロパティに設定した Discord の Webhook URL の値を取得するには、 `PropertiesService.getScriptProperties().getProperty({'DISCORD_WEBHOOK_URL'})` を利用します。Discord へのメッセージ送信は、 `UrlFetchApp.fetch(url, options)` で行っています。  

## GAS デプロイ

作成したコードをデプロイします。右上の デプロイ -> 新しいデプロイ をクリックすます。
![](/images/2024-06-19/011.png)

種類に `ウェブアプリ` を選択します。
![](/images/2024-06-19/012.png)

説明、ウェブアプリの実行ユーザーとアクセスできるユーザーをそれぞれ設定し、デプロイをクリックします。

- 新しい説明文
  - `v0.1`
- 次のユーザーとして実行
  - `自分（xxxx@gmail.com）`
- アクセスできるユーザー
  - `全員`

![](/images/2024-06-19/013.png)

:::message alert
初回のデプロイ時には、 デプロイするウェブアプリからのアクセス許可を行う必要があります
:::

以下の手順で、アクセス許可を行いました
![](/images/2024-06-19/014.png)
![](/images/2024-06-19/015.png)
![](/images/2024-06-19/016.png)
![](/images/2024-06-19/017.png)
![](/images/2024-06-19/018.png)

デプロイが完了すると、ウェブアプリのURLが表示されるので、コピーします。
![](/images/2024-06-19/019.png)

`https://script.google.com/macros/s/AKfycbz-zctXp5RtYoA_JqcuM06R0LV_0Ev3Ink0zeME61qjiwIqbLurgoRvddV6qcHp2vo/exec`
これで GAS 側の設定は完了です。

## LINE チャネル作成

[LINE Developers コンソール](https://developers.line.biz/console/) を開きます。  

新規プロバイダーを作成します。プロバイダー名は、 `開発テスト` にします。
![](/images/2024-06-19/101.png)

プロバイダーの画面からチャネルを作成します。 `Messaging API` をクリックします。
![](/images/2024-06-19/102.png)

必要な情報を入力します。

- チャネルの種類: `Messaging API`
- プロバイダー: `開発テスト`
- 会社・事業者の所在国・地: `日本`
- チャネル名: `Discord転送チャンネル`
- チャネル説明: `メッセージをDiscordへ転送します`
- 大業種: `ウェブサービス`
- 小業種: `ウェブサービス（ユーティリティ）`
- メールアドレス: `{自分のメールアドレス}`
- LINE公式アカウント利用規約ビの内容に同意します: `同意(チェック)`
- LINE公式アカウントAPI利用規約での内容に同意します: `同意(チェック)`

![](/images/2024-06-19/103.png)
![](/images/2024-06-19/104.png)
![](/images/2024-06-19/105.png)
![](/images/2024-06-19/106.png)
![](/images/2024-06-19/107.png)

２ つの利用規約に同意します。
![](/images/2024-06-19/108.png)
![](/images/2024-06-19/109.png)

LINE チャネルが作成できました。
![](/images/2024-06-19/110.png)

### LINE チャネルの Messaging API 設定

チャネル作成後に、追加で Messaging API 設定 を行います。  
応答メッセージ、あいさつメッセージの変更は、別の管理画面 (LINE Official Account Manager) で行います。

- Messaging API設定
  - Webhook URL: `{自分の Discord の Webhook URL}`
    - Webhookの利用: `有効`
  - 応答メッセージ: `無効`
  - あいさつメッセージ: `無効`

チャネルの Messaging API設定 をクリックします。
![](/images/2024-06-19/111.png)

Webhook URL の 編集 から、Discord の Webhook URL を設定します。併せて、 Webhookの利用を `有効` にします。
![](/images/2024-06-19/112.png)
![](/images/2024-06-19/113.png)

応答メッセージ右の 編集 をクリックすると別ウインドウで LINE Official Account Manager が開かれます。チャネルの画像変更も LINE Official Account Manager から行えます。
![](/images/2024-06-19/114.png)
LINE Official Account Manager の応答設定で あいさつメッセージ、応答メッセージを `無効` に変更します。  
![](/images/2024-06-19/115.png)
![](/images/2024-06-19/116.png)

LINE Developers コンソールのチャネル画面に戻ってリロードすると、応答メッセージ、あいさつメッセージが `無効` になっていることが確認できます。
![](/images/2024-06-19/117.png)

## LINE チャネルを友達追加

チャネルの Messaging API 設定 QRコードから友達追加します。
![](/images/2024-06-19/118.png)
友達追加します。
![](/images/2024-06-19/119.png =180x)
*LINEアプリ（mac版） 友達追加画面*

## 動作確認

以下の手順で動作確認します。

- LINE チャネルでメッセージを入力
![](/images/2024-06-19/121.png)
- Discord への通知を確認
![](/images/2024-06-19/122.png)
- GAS のプロジェクト画面の 実行数 を確認
  - 開始時間が正しいことやステータスが`完了`となっていることを確認
  サイドメニューから 実行数 を選択します。
  ![](/images/2024-06-19/123.png)
  ![](/images/2024-06-19/124.png)

## まとめ

Google App Script (GAS) を活用して、LINE チャネルに入力されたメッセージを Discord へ送信する方法について記載しました。  
LINE チャネルの Webhook を初めて利用しましたが、比較的少ない設定で連携することができました。LINE は慣れている人が多く、ユーザーフレンドリーなインターフェースであるため、今後も利用する機会がありそうです。
この記事が、GAS を活用した LINE 連携の参考になれば幸いです。  

## 参考 URL

- [LINE Developers Docs: チャネルを作成する](https://developers.line.biz/ja/docs/liff/getting-started/#step-one-create-provider)
- [Apps Script ガイド: ウェブアプリ](https://developers.google.com/apps-script/guides/web?hl=ja)
- [Discord Docs: Execute Webhook](https://discord.com/developers/docs/resources/webhook#execute-webhook)
