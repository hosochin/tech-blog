---
layout: post
title: LINE公式アカウントのMessaging API + GAS でメッセージを通知する
date: 2025-04-03
tags: [LINE, GAS]
---

# はじめに

お世話になります、hosochinです  
今回は、GAS(Google Apps Script)で、RSSから取得したニュースをLINEに送信するbotを作りました  
当初LINE Notify を使って実装しようとしたのですが、[LINE Notifyが2025/03/31にサービス終了](https://notify-bot.line.me/closing-announce)するということで、今回はLINE公式アカウントの[Messaging API](https://developers.line.biz/ja/docs/messaging-api/)を使います

# 目次

- [この記事で扱うこと](#この記事で扱うこと)
- [前準備、やったこと](#前準備やったこと)
- [LINE IDの取得](#line-idの取得)
- [doPost関数の実行ログを確認する](#dopost関数の実行ログを確認する)

# この記事で扱うこと

主に私がハマりそうな気がしたポイントを書き残しておこうと思います  
以下の2点です  
1. LINE ユーザーID の取得方法  
2. GASでdoPost関数が呼び出された際に実行ログを確認する方法  
なので本記事のタイトルとはおそらく趣旨がずれちゃいます…w

# 前準備、やったこと

1. LINE公式アカウント作成 + Messaging APIの設定 
   - こちらの記事を参考にさせていただきました  
   <https://qiita.com/jksoft/items/4d57a9282a56c38d0a9c>
2. GASの実装 
   - RSSでニュースを取得、連携先のMessaging APIに送信 
     - 実装はchat-gptに丸投げです
3. Messaging APIとの紐付け 
   - webhookをonにしたり、API トークンを有効にしたり

# LINE IDの取得

今回、意図しないユーザへの配信が嫌だったので、自分のLINEアカウントのみに配信されるようにしました

```javascript
function sendNewsToLine(messageText) {
  var token = "xxxxxxxxxxxxxxx"; // LINEのチャネルアクセストークン
  
  // 特定のユーザに通知する場合はこちら
  var message = {
    "to": "xxx", // 自分のLINE ID
    "messages": [{
      "type": "text",
      "text": messageText
    }]
  };

  // 全員に通知する場合はこちら
  // var message = {
  //   messages: [
  //     {
  //       type: 'text',
  //       text: messageText,
  //     },
  //   ],
  //   notificationDisabled: false
  // };
  
  var options = {
    "method": "post",
    "headers": {
      "Content-Type": "application/json",
      "Authorization": "Bearer " + token
    },
    "payload": JSON.stringify(message)
  };
  
  // 特定のユーザに通知する場合はこちら
  UrlFetchApp.fetch("https://api.line.me/v2/bot/message/push", options);
  // 全員に通知する場合はこちら
  // UrlFetchApp.fetch('https://api.line.me/v2/bot/message/broadcast', options);

}
```

特定アカウント(=自分のアカウント)に配信する場合、自分のLINE ユーザーIDを取得する必要があります  
このIDは、LINEアプリからは確認できないようになっています

<https://developers.line.biz/ja/docs/messaging-api/getting-user-ids/>

> ユーザーIDとは、ユーザーの一意な識別子です。ユーザーIDは、LINEで登録されているユーザーの表示名や、LINEでの友だち検索に利用するLINE IDとは異なります。ユーザーIDはLINEプラットフォームが発行する値で、`U8189cf6745fc0d808977bdb0b9f22995`のように、`U[0-9a-f]{32}`の正規表現にマッチする文字列です。

取得方法は以下の4つがあるそうです  
1. 開発者が自分自身のユーザーIDを取得する  
2. WebhookからユーザーIDを取得する  
3. 友だち全員のユーザーIDを取得する  
4. グループトークや複数人トークのメンバーのユーザーIDを取得する  

今回は2の手順で取得しました  
連携先のGASでdoPostを実装します

```javascript
function doPost(e) {
  var json = JSON.parse(e.postData.contents);
  Logger.log(json);  // 受信したデータをログに出力

  var userId = json.events[0].source.userId;  // 送信者のUserIDを取得
  Logger.log("UserID: " + userId);

  return ContentService.createTextOutput("OK");
}
```

自分のLINEアカウントで作成したLINE公式アカウントをフォローし、LINE公式アカウントに対して適当なメッセージを送信してやると、doPostが反応して、ログが出力されます

# doPost関数の実行ログを確認する

よし、ログ確認するかーと「実行数」から見てみると…実行ログはあるのですが肝心のコンソールログを確認しようとみても「Cloudのログ」というのが選択できません  
自分はテキストファイルに出力して確認しましたが…以下のサイトが参考になりそうでした  
[【GCPと連携で解決】GASでLINE bot等の外部サービスを扱う際、ログを出力する方法](https://gas-4u.com/gas20)

# まとめ

LINE Notify終了に伴い、LINE公式アカウントのMessaging APIを使ったGASでの通知システムを構築しました。特にユーザーID取得とGASでのログ確認は少し癖があるので、同じ実装をする際の参考になれば幸いです。
