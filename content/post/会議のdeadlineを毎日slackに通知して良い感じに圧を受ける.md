+++
title = "会議の Deadline を毎日 Slack に通知していい感じに圧を受ける"

date = 2018-04-04
lastmod = 2018-04-04
draft = false

tags = ["gas", "javascript", "slack", "圧"]
summary = "[Google Apps Script](https://developers.google.com/apps-script/) で面白い実装ができないか考えてる時に思いついたネタ．"

[header]
image = ""
caption = ""
+++

弊研究室では論文を投稿する会議のリストを Google Sheets を使ってこんな感じで管理してます．

![](/img/gss-conference-list.png)

この中の「締切まで何日 (Days left)」の項目は怠惰な僕にとって非常に重要なわけですが，Google Sheets は重いし，このページを毎日見に行こうとは思いません．そこでこれらの項目を毎日 Slack で自動的に通知してくれたら，いい感じに締切への圧がかかって研究が進むというものです．そのような機能を実現する bot を [Google Apps Script (GAS)](https://developers.google.com/apps-script/) を使って実装してみました．

GAS はとても強力で，例えば以下のような機能を備えています：

- Google Sheets の読み書きができる：[Extending Google Sheets  |  Apps Script  |  Google Developers](https://developers.google.com/apps-script/guides/sheets)
- サーバへリクエストを送信できる：[External APIs  |  Apps Script  |  Google Developers](https://developers.google.com/apps-script/guides/services/external)
- 定期的なスクリプトの実行ができる：[Installable Triggers  |  Apps Script  |  Google Developers](https://developers.google.com/apps-script/guides/triggers/installable)

これらの機能を使えば，毎日 Slack で通知を受け取るような bot は GAS 単体で簡単に書けます．この投稿ではその実装を簡単に紹介します．

## 実装

### Google Sheets の値を読み取る

```js
function getConferenceInfo() {
  var ss = SpreadsheetApp.openByUrl(CONFERENCE_LIST_SHEET_URL);
  var conference = ss.getSheetByName('Conference list');
  var confs = conference.getSheetValues(1, 1, conference.getMaxRows(), 9);
  return confs.filter(
    function (conf) {return typeof conf[1] === 'number'}
  ).map(
    function (conf) {return {
      name: conf[0],
      daysLeft: parseInt(conf[1]),
      abstractDue: conf[2],
      paperDue: conf[3],
      url: conf[8],
    }}
  );
}
```

流れとしては，

1. [`SpreadsheetApp.openByUrl`](https://developers.google.com/apps-script/reference/spreadsheet/spreadsheet-app#openByUrl(String)) で，引数の URL で指定したスプレッドシートを開く（[`Spreadsheet`](https://developers.google.com/apps-script/reference/spreadsheet/spreadsheet) オブジェクトが返る）
2. [`Spreadsheet.getSheetByName`](https://developers.google.com/apps-script/reference/spreadsheet/spreadsheet#getSheetByName(String)) で，引数に与えた名前のシートを開く（[`Sheet`](https://developers.google.com/apps-script/reference/spreadsheet/sheet) オブジェクトが返る）
3. [`Sheet.getSheetValues`](https://developers.google.com/apps-script/reference/spreadsheet/sheet#getSheetValues(Integer,Integer,Integer,Integer)) で，指定した範囲の値を受け取る（array の入れ子 `Object[][]` が返る）
4. 適当に加工する

で終わりです．匿名関数を書くのに `function` を使ってたり，変数宣言で `var` を使ってたりしますが，これは GAS が ES6 に対応していないためです．

### Slack Incoming Webhooks にリクエストを投げる

```js
function sendDeadlineInfo(attachments) {
  var data = {
    attachments: attachments
  };
  var options = {
    method : 'post',
    contentType: 'application/json',
    payload : JSON.stringify(data)
  };
  UrlFetchApp.fetch(SLACK_WEBHOOK_URL, options);
}
```

加工した情報を Slack の Incoming Webhooks に送信します．GAS では [`urlFetchApp.fetch`](https://developers.google.com/apps-script/reference/url-fetch/url-fetch-app#fetch(String,Object)) でリクエストを投げることができます．便利．

[Slack の attachment](https://api.slack.com/docs/message-attachments) への加工は省略します．

### 毎日定時刻に関数を実行する

これも非常に便利で，特定の条件（イベント）を満たしたときに指定した関数を実行するように設定することができます．イベントを発火するトリガーは「時間」と「日付」の2種類あって，「時間」の場合，分・時間・日・週・月毎にトリガーを設定できます．今回の場合は毎日日付が変わったころに圧を感じたいので，次の画像のように設定しました．

![](/img/gas-schedule.png)

Run に指定した `main` 関数では会議の情報を加工して `sendDeadlineInfo` を叩きにいきます．

## 結果

こんな感じのができました．

![](/img/slack-deadline.png)

Incoming Webhooks のアイコンが :innocent: なのもあって，締切への死を感じさせる bot になってますね．

このように，単純な bot であれば GAS だけで簡単に書くことができます[^1]．他の Google アプリとの連携も組み込みで用意されているので，アイデア次第で高度な自動化ができそうです．

[^1]: ES6 で書けないのが玉に瑕ですが．
