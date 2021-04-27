---
title: "Lookerまとめ"
emoji: "🤖"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: [Looker]
published: true
---

# はじめに
現在Lookerを使用しているのですがかなり情報が限られており（ほぼ公式？）、自分でまとめたいと思ったのでこの記事を書きます。

:::message alert
公式のドキュメントがかなり充実しているので分かる方はそちらをみた方が良いと思います。
:::
## Lookerとは
[Developers IOの記事](https://dev.classmethod.jp/articles/looker-overview/)がわかりやすいと思います。
`Redshift`や`BigQuery`からとってきたデータを使用してKPIダッシュボードやレポートなどを作成することができます。
私の所属している事業ではDBにfirestoreを使用しており、[Export Collections to BigQuery](https://firebase.google.com/products/extensions/firestore-bigquery-export/)を使ってfirestoreのデータをBigQueryに流し、それを同期して分析を行っています。

[メルカリ](https://engineering.mercari.com/blog/category/looker/)や[リクルート](https://ja.looker.com/24/_info_looker_com/customer-stories/recruit-marketing-partners-case-study)などでも使われているようです。

## 使うとなった時に
まずどこになんのデータがあるのかを覚えるのがとてもきついと思います、データ構造などをきちんとまとめておくとデータを出す上でとても楽です。

私の場合はオンボードで
- どこになんのデータがあるのか
- lookerの簡単な使い方
- 試しにデータを出してみる
- 実際のタスク
みたいな感じでした、実際のタスクに入るまで２〜３日ぐらいです。

### どこになんのデータがあるのか
やっていくうちに慣れますが、どこのテーブルになんのデータがあるのかはきちんと覚えておいた方が良いと思います。
例えば私の場合は性別・デバイス・日付・イベント名・画面名はよく使うので最初に説明されました。

日付に関してはデータの軸として使用することが多いと思いますが、どれがなんの日付なのか、今出したいデータの軸としてはどれが正しいのかをきちんと理解しておかないといけないと思います。

### lookerの簡単な使い方
https://training.looker.com/
という公式の提供しているコースみたいなのがあるので一通りみてみると良いと思います、無料です。

## 用語
私はBIの分野は初めてだったのですが聞きなれない用語がいくつかあったので初めにまとめて説明します。
一旦飛ばしてもらってわからなくなった時に参考にしてください。

| 用語 | 説明 | url |
|--|--|--|
|dimension|次元や寸法などの意味ですが、新しいデータを分析などで使用する時に新たに追加する定義みたいな感じです。例えば`document_id`のディメンションを追加することでdocument_idをテーブル作成などで使用できるようになります。document_idは`string`だと思いますが、さまざまな型の値を持って来れます。|https://docs.looker.com/ja/reference/field-params/dimension|
|measure|集計などの意味で、その名の通り特定の条件で一致したものを集計します、集計の種類にはcount(重複あり)、count_distinct(重複なし)があり、例えばユーザーIDなどの一意のものはcount_distinctで集計することでユーザー数を算出出来ます、イベントの実行数などの時はcountを使用します。|https://docs.looker.com/ja/reference/field-params/measure|
|フィールド|フィールドはディメンションと同様です、データ算出の時に`.view`ファイルに定義したディメンションの名前がフィールドとして表示され、選択することでデータを使用できます、もし変更したい場合は`.view`ファイルでの定義のところで`label`を使用することで変更できます。|https://docs.looker.com/ja/exploring-data/exploring-data|
|カスタムフィールド|そのデータ算出の時にだけ使いたいフィールドがある時に使用するフィールドです、選択したexploreとそのexploreにjoinされているものの中からディメンション、メジャーを選択して追加できます、よく使うものなら`.view`ファイルに定義しても良いですが、使い捨てなのであればカスタムフィールドを使用すると良いと思います。|https://docs.looker.com/ja/exploring-data/adding-fields/custom-measure|

# ヘッダー
ヘッダーには `閲覧` `Explore` `開発` の三つがあります。
閲覧は見ればわかると思いますが `Explore` `開発` についてはわかりにくい部分があるので掘り下げていこうと思います。

# 開発
こんな感じになっていると思います。
![](https://storage.googleapis.com/zenn-user-upload/wx76u8wql8gbhb91jpy9zlmodiqs)

## 開発モード
lookMLファイルを編集する時には開発モードに入る必要があり、モードを変更するためのトグルボタンです。
開発モードがオンになっていないと編集することができません。

## SQL Runner
BigQueryなどからデータを流してきて分析を行いますが、ビューファイルにディメンションやメジャーを定義しないと分析に使用することができません。
そもそもBigQueryからどんなデータが流れてきているのかをSQL Runnerで確認することができます。
詳しい使い方は[ドキュメント](https://docs.looker.com/ja/data-modeling/learning-lookml/sql-runner)をみるのが早いと思います。

## Content Validator
これを使うと、現在エラーが発生しているLook、Dashboardをまとめて表示することができます。
[Developers IO](https://dev.classmethod.jp/articles/looker-content-validator/)の記事がとてもわかりやすかったです。

## LookMLプロジェクトの管理
LookMLの定義ファイルに飛びます、ここにLookMLを定義することでダッシュボードなどでデータを使用することが可能になります。
## lookMLについて
[lookML](https://docs.looker.com/ja/data-modeling/learning-lookml/what-is-lookml)
>LookMLはSQLデータベース内のディメンション、集計、計算、およびデータ関係を記述するための言語です。LookerはLookMLで記述されたモデルを使用して、特定のデータベースに対するSQLクエリを作成します。

ヘッダーの`開発`というところから`LookMLプロジェクトの管理`を押下するとlookMLの定義しているページに飛ぶことができます。
`.model` と `.view.lkml`の二つの拡張子のついたファイルを作成でき
モデルファイルで使用するテーブルの設定(exploreで表示される)とどのテーブル同士を結合(join)するかを設定できます。
:::message alert
開発モードをオンにしないと編集できません
:::

```hogehoge.model
connection example # 接続するDB名
include "sample/*.view.lkml" # 取り込みたいビューファイル名、この場合sample以下の全てのビューファイルをinclude

explore: sample {
  group_label: ""
  label: ""
  description: ""
  join: hoge {
    view_name: view_name # ビューファイルでの登録名、どのビューファイルを指しているか
    from: hoge
    type: left_outer # どっちを優先して結合するか
    sql_on: ${sample.user_id} = ${hoge.user_id} # どれで結合するか
    relation: many_to_one
  }
}
```

[dimentionとmeasure、exploreの使い方](https://docs.looker.com/ja/data-modeling/getting-started/lookml-validation)について
ビューファイルで使用するテーブルの設定(どのテーブルの情報をとってくるか、)`dimension`の定義と`measure`の定義を行うことができます。
dimensionとmeasureの定義を行うことによってとってきたテーブルのデータについての分析を行うことができます。
全て１からファイルを作って定義を書かないといけないわけではなく
![](https://storage.googleapis.com/zenn-user-upload/kgt9vrobs6vs29oo9mtzhk8pv7uo)
全部は写せないですが、こんな感じで
`Create View From Table`を押せば自動的に取得してきてviewファイルを作成しlookMLを出力してくれます、もちろんそこからは自力で追加したりする必要があります。

```sample.view
view: sample {
  sql_table_name: `example.firestore.sample` ;;

  dimension: data {
    hidden: no
    type: string
    sql: ${TABLE}.data ;;
  }
  dimension: user_id {
    type: string
    sql: SPLIT(${document_name}, '/')[OFFSET(8)] ;;
  }
  measure: uu_count {
    group_label: "UU"
    label: "uu_count"
    sql: ${user_id} ;;
    type: count_distinct
  }
}
```

typeについては
[dimensionドキュメント](https://docs.looker.com/ja/reference/field-reference/dimension-type-reference)
[measureドキュメント](https://docs.looker.com/ja/reference/field-reference/measure-type-reference)
をご覧ください。

measureのドキュメントのdateのところにもありますが
最新の日時、最古の日時を出したい場合はsqlでMAXなどを使うと出すことができます。

都道府県などで何県の人みたいな感じで取りたい場合は[case when](https://docs.looker.com/ja/reference/field-params/case)を使うと出すことができます。

``` sample.view
dimension: living_at {
  label: "都道府県"
  type: string
  case: {
    when: {
      sql:  ${living_at_raw} = '"hokkaido"' ;;
      label: "北海道"
    }
    when: {
      sql:  ${living_at_raw} = '"aomori"' ;;
      label: "青森"
    }
  }
  ~~~~~~~~~~~~~~~~~~~~~~~~~
}
```

注意点としては真偽値が `bool` `boolean` ではなく `yesno` であることぐらいだと思います。
`count` は全数カウント、 `count_distinct` は重複なしでカウントします、user_idなどの一意のものをカウントしてUUを出したい場合などに使います。

また変更を `save change + validate LookML` するとその変更をexploreやダッシュボードで試すことができます。
変更を破棄したい場合は一番左のブランチみたいなマークのところに `Revert` が追加されるのでそれを押下することで破棄できます。
![](https://storage.googleapis.com/zenn-user-upload/idb3h6z39hpcy2ex7224yb1iorpq)

他の人にも変更を反映したい場合は `Commit Change & Push` をして `Merge deploy` を押すとメインに取り込まれ、それを他の人がpullすることでその人のlookerに反映されます。

# Explore
手元だけでデータを分析したい場合はヘッダーの `Explore` からビューファイルを選択することで分析を行うことができます。
Exploreで分析したものはurlを共有したり、ダッシュボードに保存することで他の人に共有することが可能です。

## フィールド、カスタムフィールドなどについて
フィールドについては[ドキュメント](https://docs.looker.com/ja/exploring-data/exploring-data)をみるのが一番良いと思います、ここを見ればわかると思うので省略します。

注意点としては
日付などをフィルタリングする場合は `日` と `終了した日` がありますが、日は今日も含みますが、終了した日は今日を含まず昨日からになります。
また`週`などを指定した場合、まだ週が終わっていない場合残りの日付の分は表示されません、なので過去1週間を指定したい場合は `終了した日` を7日にする必要があります。

### カスタムフィールドについて
カスタムフィールドには`カスタムディメンション` `カスタムフィールド` `表計算` があります
https://docs.looker.com/ja/exploring-data/adding-fields/custom-measure


`カスタムフィールド　カスタムディメンション`については
https://redpillanalytics.com/combining-advanced-and-custom-filters-in-looker/
がわかりやすかったです。

#### 表計算
表計算は割合や平均数などを計算する際に使用します、表計算では[ドキュメント](https://docs.looker.com/ja/exploring-data/creating-looker-expressions/looker-functions-and-operators)にある関数を使用して計算を行うことができます。
使い方については上記のドキュメントをきちんと読めばできると思いますが、よく使うものについて簡単にまとめたいと思います。

`expression`が特定のディメンションやメジャー、`value`が自分で設定を行う任意の数字を表しています

##### large(expression, k)
> expressionで作成される列のk番目に大きい値を返します。ただし、expressionがリストの列を定義している場合を除きます。この場合、各リストのk番目に大きい値を返します。

この場合だと何日分かのデータで二番目に大きい数値が入ります。
一ヶ月の中で上位三日の数の合計を取りたいとかの場合などに使えると思います。
![](https://storage.googleapis.com/zenn-user-upload/ajwakqm54q6uhbc2ky63uibpi0p6)

##### match(value, expression)
> expressionで作成される列に最初に出現したvalueの行番号を返します。ただし、expressionがリストの列を定義している場合を除きます。この場合、各リストにあるvalueの位置を返します。

この場合だと468という数字が初めて出た行番号が入ります。
![](https://storage.googleapis.com/zenn-user-upload/0nkalp8h17ho4cb0th4bh0oq02c4)

##### sum(expression)
> expressionで作成される列の和を返します。ただし、expressionがリストの列を定義している場合を除きます。この場合、各リストの和を返します。

ただ合計を出すだけです、わかると思います。

##### if(yesno_expression, value_if_yes, value_if_no)
> yesno_expressionでYesという結果が出た場合、value_if_yes値を返します。そうではない場合は、value_if_no値を返します。

`if(${users.state}="OHIO" OR ${users.state}="ARIZONA", "New Market", "Existing Market")`
この場合だと`${users.state}`が"OHIO"もしくは"ARIZONA"だったら"New Market"それ以外は"Existing Market"が入ります。

#### pivot
`pivot_○○`という表計算にのみ使用することのできる関数があります。
それぞれの関数を使用している方の[記事](https://community.lightspeedhq.com/en/discussion/580/analytics-calculations-pivot-functions)がありますので、実際に使うとどんな感じになるのか確認するのにちょうど良いと思います。

上記した`large` `match` `sum` 関数に普通にexpressionを渡すだけでは行の計算を行うことができないですがpivotの関数を使用することで行の計算を行うことができるようになります。
例えば `sum(pivot_row(expression))` とすると行の指定したexpressionの合計を出すことができます。

[ドキュメント](https://community.looker.com/explores-36/creating-a-percent-of-total-across-rows-with-table-calculations-1057)に例があるのでみるのをお勧めします。
この例ではsum関数を使用して行の合計を出し、合計でそれぞれのexpressionを割ることによって、全体からの割合を計算しています。


##### pivot_row(expression)
> expressionのピボット値をリストとして返します。ピボットされていない結果には、 null が返されます。

上記した例のように割合を出す時に使用することがほとんどだと思います、他の例が思い浮かばないので何か良い例があれば教えてください。

##### pivot_index(expression, pivot_index)
> 位置pivot_index（1は最初のピボット、2は2番目のピボット）にあるピボット列のコンテキストでexpressionの結果を求めます。ピボットされていない結果には、nullが返されます。

特定の行の値だけを計算に使用したいことがあると思います、pivot_indexを使用すると実現できます。

[ドキュメント](https://community.looker.com/explores-36/using-pivot-index-in-table-calculations-3-28-1291)に例があるのでみるのをお勧めします。

pivot_indexを計算に使用した場合、データの数などに変更があった時に計算結果がずれる場合があるので極力 `pivot_row` を使用することをお勧めします。
使い捨ての時やどうしようもない時以外は極力使うのを避けましょう。

##### pivot_offset(pivot_expression, col_offset)
> 位置(n + col_offset)にあるpivot_expressionの値を返します。ここで、nは現在のピボット列の位置です。ピボットされていない結果には、nullが返されます。

[ドキュメント](https://community.looker.com/explores-36/creating-a-running-total-across-rows-with-table-calculations-1375)のように段階的に合計を求めたい場合などに使用すると思います。

[もう一つのドキュメント](https://help.looker.com/hc/en-us/articles/360023509454)のように前日比の割合を出したい場合にも使用できます。

##### pivot_offset_list(pivot_expression, col_offset, num_values)
> 位置(n + col_offset)から始まるpivot_expressionにあるnum_values個の値のリストを返します。ここで、nは現在のピボット指数です。ピボットされていない結果には、nullが返されます。

sumなどで数字のリストを使用する関数を使いたい場合にを一部の値だけをリストにするのに使えます。

##### pivot_where(select_expression, expression)
> 一意にselect_expressionを満たすピボット列にはexpressionの値を、そうした一意の列が存在しない場合はnullを返します。

[ドキュメント](https://docs.looker.com/ja/exploring-data/creating-looker-expressions/pivot_where)
`pivot_where(${orders.status} = "pending", ${orders.count})`
この場合だと行で`orders.status`が`pending`のものの`orders.count`の合計を返します。



### Exploreのボタンについて

![](https://storage.googleapis.com/zenn-user-upload/3te1wjbwjtrjjpuxcve1xmfs0c64)

`Lookとして保存` はその分析のみを保存する場合に使います。

`DashBoardに保存` はその分析やその他の分析を一つにして保存する場合に使います。例えば同じ分析でも男女別やデバイス別で出すことがあると思います、そういうのをまとめる時はダッシュボードに保存します。

`lookMLを取得する` は中間テーブルを作成する時に使用します。
![](https://storage.googleapis.com/zenn-user-upload/44a14xzm5jylh5rgslyba0i7cejd)

ボタンを押すとこんな感じになると思うので

1. 派生テーブルのところのlookMLをコピーして
2. ビューファイルを新しく作成しペーストして、名前等を変更し
3. モデルファイルに登録

すれば使用することができるようになります、メッセージなどの受信側のuser_idを取りたい場合や重いクエリを軽減するために使用しています。

[ドキュメント](https://docs.looker.com/ja/data-modeling/learning-lookml/derived-tables)をみるとより理解できると思います。

`結果をマージする` はいくつかのデータをマージして分析に使用したい場合に使います。
例えば、Aイベントの行われた割合を出したいが母数としたい総数が他のファイルに定義されている場合などに必要な総数のデータをとってくるために使用したりします。
複雑なデータ分析をしたい場合は使用することが多いと思います。

ボタンを押すともう一つビューファイルを選択できるようになります。
![](https://storage.googleapis.com/zenn-user-upload/kwiyth9yilbd7skx8pkbgzeect3w)

必要なデータを出して保存を押すと、こんな感じになります。
![](https://storage.googleapis.com/zenn-user-upload/i939vxf0sqocccob8pct51pvxem4)

### Source Queries
使用しているビューファイルが表示されます、どれがどのデータをとっているのかわかりやすいように名前を変更しておくと良いと思います。
`Add Query`を押下するとさらに追加できます。

### Merge Rules
どのデータを軸に結合しているかが表示されます、必ず一つは重複していないとマージを行うことができません、今回は`eventsのEvent Date`がマージされていますが、時間軸の場合はそれぞれで出す日付の期間を同じにしないと片方の足りない期間の分が全て空になってしまいます。

右上の歯車をクリックすると `Save to dashboard` からダッシュボードを指定して保存することができます。

:::message alert
ピボットをする場合はマージしてからピボットしないとエラーになります、気をつけてください。
ピボットはデータ名の右の歯車から設定できます。
:::

# 最後に
ざっくりとまとめてみました、誤字脱字、その他間違っているところがありましたら連絡いただけるとありがたいです。