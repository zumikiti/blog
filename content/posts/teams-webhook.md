---
title: "Webhook で Teams に投稿する方法"
date: 2020-03-07T17:05:06+09:00
tags: [teams, webhook, bot, post]
---

teams に webhook を設定して、teams に投稿するサンプル

## curl で直接送る場合
```sh
echo '{"text": "Hello World!"}' | curl -H 'Content-type: application/json' -d @- <WEBHOOK_URL>
```

## Google App Script(GAS) で送る場合
```js
UrlFetchApp.fetch(
  <WEBHOOK_URL>,
  {
    "headers" : {
      "Content-Type": "application/json"
    },
    "method" : "POST",
    "payload" : JSON.stringify({
      text: "Hello World!"
    })
  }
);
```

## メンションがつけられない
今の時点では、メンションをつける方法がサポートされていない様子。  
`<at>user</at>`,  `<at>user@microsoft.com`, `@user` と色々試してみたがダメだった。

https://stackoverflow.com/questions/41440803/how-to-mention-a-user-group-in-a-new-message-to-a-channel

