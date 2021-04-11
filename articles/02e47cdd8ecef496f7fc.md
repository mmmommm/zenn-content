---
title: "FCMの調査結果"
emoji: "👋"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: [Looker, Firebase]
published: true
---
# 初めに
データ分析としてアルバイトをしています、プッシュ通知の実装をFCM(Firebase Cloud Messaging)で行っており、プッシュ通知のデータの分析を頼まれ、それの調査結果です。
ドキュメントもあるのですが、いまいちわからない部分があり、同じように困った人に役立てば幸いです。

## 注意
データ分析の観点でしか話をしません、モバイルのエンジニアの方には役にたたないと思います。

## 参考にしたもの
[firebase_messagingのデータ構造](https://firebase.google.com/docs/cloud-messaging/understand-delivery?hl=ja)
[kakakakakkuさんの記事](https://kakakakakku.hatenablog.com/entry/2017/05/12/125542)
[pixivさんの記事](https://devpixiv.hatenablog.com/entry/2016/12/03/190000)

# それぞれのデータについて
### event_timestamp
日付、これでフィルターをかければ一週間での通知送信数や日毎の送信数などを出せます。

### message_id
メッセージ識別のためのid、カウントが２になっているものもありグローバルに一意ではない可能性があります。
アプリIDとタイムスタンプから生成され、
種類が二つあり、`0:16桁数字のみ%16桁英数混合` と `数字のみ16桁`　のみ
数字のみの部分はマイクロ秒単位のUNIXタイムスタンプだと思われます（おそらくそう？？）。
一つ目の方の後ろの英数混合が二種類しかなかったのでなんかしらを指していて、アプリIDから生成されるとのことなのでプロジェクトのIDだと思ったが違いました、謎です。

### instance_id
*what is instance ID*
原文　[https://developers.google.com/instance-id/](https://developers.google.com/instance-id/)
翻訳　[https://www.milk-island.net/translate/ggd/instance-id/](https://www.milk-island.net/translate/ggd/instance-id/)

> Android・iOS・Chrome apps/extensions などでそれぞれ各端末に対して発行されるユニークキーCrashlytics for Firebase や Analytics for Firebase などでユーザー(=端末)を紐付ける為に利用されるキーで各プラットフォームライブラリやREST APIで削除・再生成することが可能で、削除するとそのInstance IDに紐づくユーザーデータが各Firebase Productsから削除される

user_id = instance_id　というわけではないので気をつける必要がありそうです。
instance_idは再生成されることがあるので正確ではないですが `count_distinct` で重複を消してカウントすることによって擬似的なUUは出せます。

### message_type
通知メッセージかデータメッセージのどちらか、今回の場合は通知メッセージだけなのでフィルターかければ良かったです。

### sdk_platform
OS情報が取れる `IOS` `ANDROID` `UNKNOWN_OS`で分類可能です。
OSごとで集計したければ使います。

### app_name
Androidパッケージ名、iOSバンドルID、空のどれかが入ります。

### topic
トピックの名前らしいですが、`空`か`information`しか入ってませんでした。

### bulk_id
> トピックへの特定の送信など、関連するメッセージのグループを識別します。
と書いてあるので、これで通知メッセージごとの送信数などが出せると思っていたのですが、かなり別れていて、どれがどれを指しているのかいまいちよくわからなかったです。

### event
`MESSAGE_ACCEPTED`を使用することでメッセージの到達数、到達率を出すことができます。
イベントを軸にすることでエラーの詳細やそれぞれの数も出すことができます。

### analytics_label
[JSON](https://firebase.google.com/docs/reference/fcm/rest/v1/projects.messages?hl=ja)で返せそうなので期待していたのですが、全て空になってしまっていました、適切にデータが飛んでいなさそうなのでここを改良すれば色々とデータが取れそうです。

## 出せるもの
user_idがFCM側で取得できていないこと、メッセージ開封のフラグがないことが厳しいです、開封フラグと受信ユーザーのuser_id、送信メッセージのタイトルがあるとありがたいですね。。。

- 通知の到達率
  - デバイス別は出せますが、user_idがないので性別とリテンション（受け取った人の復帰率は厳しそうです）
- 通知の到達数、到達していない場合の原因
- メッセージタイプごとの割合
- 特定期間にメッセージを受け取った人の数

## その他
[analyticsが自動で取得しているもの](https://support.google.com/firebase/answer/9234069?hl=ja)の中に `notification_oo` みたいなのがあるのでこちらも使えます、OSの制約はあります。

メッセージ開封数に関してはデータとしてはないですが、firebase consoleの`cloud messaging`のところで見ることができます。

# 最後に
message_idの部分がわかる人、bulk_idなどについて知識のある方おられましたら、教えていただけると嬉しいです。
