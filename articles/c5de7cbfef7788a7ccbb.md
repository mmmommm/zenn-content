---
title: "フロントエンドの基礎知識について②"
emoji: "😊"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: [フロントエンド, Web, 初心者]
published: true
---

[①](https://zenn.dev/mmomm/articles/525253649e0a227989d9)の続きです。
こちらの素晴らしい[スクラップ](https://zenn.dev/yamanoku/scraps/632e570e160251839d48)についての記事です。
[yamanoku](https://zenn.dev/yamanoku)さんありがとうございます。
今回はテスト、polyfill、babelなど、宣言的UI、バンドルツール編です。
:::message
不十分、間違っている部分があれば教えてください
:::

# テストツール
## なぜテストをしなければならないのかを説明できる
`テストを行うことは工数を増やしエンジニアの負担となるが、それ以上にテストによってエンジニアの負担を減らすことができるから`
テストによって減少するエンジニアの負担とは
1. 機能追加、変更、削除の時などに既存の機能が壊さない可能性をあげられる
2. スナップショットテストなどによって意図せぬ変更を検知できる
3. テストを通過させることでコードの品質を担保することができる
などが考えられます。
しかし小規模の開発などでは工数増加の負担が上回ることのほうが多いため、学習のためなどでなければ必要ないと思います。
### TDD、BDDとは何か
#### TDD
TDDは`Test Driven Development`のことでテスト駆動開発と呼ばれます。
私はしたことがないのですが、簡単にいうとプログラムを書く前にテストコードを書き（テストファースト）、そのテストコードに適合するように実装、リファクタリングなどを行っていく開発手法のことです。

1. 実装したい機能の要件を元に失敗するテストコードを書く
2. テストを通過する機能要件を満たすコードを書く
3. テストを通過する状態を維持しながら②で書いたコードをリファクタリングする
##### メリット
- 都度バグを潰せるためバグが残りにくい
- エンジニアが安心して開発を行える
##### デメリット
- 開発に時間がかかるため、導入をよく考える必要がある
- 初めのテストコードで抜けがあると困る
- 時間、資金に余裕がないと厳しい

個人的にはよほど資金がある開発か、私たち学生のように納期などが決まっていない開発でないと厳しい気がします。
#### BDD
BDDは`Behavior Driven Development`のことで振る舞い駆動開発と呼ばれます。
TDDとBDDは根本的には違わず、それまでのTDDの語彙では伝わりにくかった部分を言い換えたり、テンプレートを与えることで幾分かハードルを下げるという役割を持っているそうです。

根本的には同じですがBDDでは次のようなテンプレートを追加します
|語彙|意味|
|--|--|
|Given x. When y. Then z.|テンプレートで「xであるときに、yすると、zになる」というように手順的にして読みやすくする|
|Given|事前状態の表現|
|When|操作、イベントの表現|
|Then|事後状態の表現|
|x should y|テスト自体を「対象（x）がどうである（y）べきか？」と表現する|

[digitalsoulの翻訳記事](https://digitalsoul.hatenadiary.org/entry/20090819/1250686015)を参照しました。こちらを読めば詳しくわかると思います。

## どういうテストがあるのか理解している
フロントエンドのテストには
- ユニットテスト
単体テストとも呼ばれ小さなプログラムをカバレッジを100%に近い状態で行うテストのこと（getter関数が想定したものを返しているかなど）
これによりどこかでエラーがおこっても個々の関数などを原因から排除でき特定が容易になります。

ユニットテストにはブラックボックステストとホワイトボックステストの二つがあります

1. ホワイトボックステストはテスト対象のコードを中身まで理解した上で行うテストのことで質の高いテストになる一方、労力がかかります
2. ブラックボックステストはコードの中身をみずに、例えばAという関数は「この値を入れるとこの値で帰ってくる」ということだけを意識して書くテストのことです。労力はかかりませんが、潜在的なバグを見落とします。

- 結合テスト
検索機能など一つの機能にたいして行うテストのこと（検索欄にモックのデータを与えてフォームを送信すると送信完了のダイアログが表示されるなど）

- システムテスト
バックエンドとの通信なども繋ぎ合わせてテストを行うこと

- リグレッションテスト
コードになんらかの変更を行った後に動作が担保されているかを確認するために実行されるテストのことです。
[デグレーション](https://wa3.i-3-i.info/diff161degrade.html)を防ぐために行われます。
。

- UIテスト
見た目に関するテスト

があります
### それぞれのテストにおいてどういうツールを用いるかを理解している
#### ユニットテスト
フロントエンド開発のユニットテストにおいて使用されるテストツールはrailsであればRspec、javascriptであればjestやenzyme、laravelであればphpUnitなどになるかなと思います。
#### 結合テスト
基本的にブラウザ(chromeやsafari)などで人間の目でみてUIを確認することがフロントエンドだと多いと思います。
#### システムテスト
[Selenium](https://selenium.dev/)が有名だと思います。
#### リグレッションテスト
この後に説明しているCIツールを使用すると思います。例えばCIが走るタイミングをPRが出された時などにするとそのタイミングでテストが走り修正に問題がないかがわかります。
UIリグレッションテストには[Story Book](https://www.learnstorybook.com/intro-to-storybook/react/ja/get-started/)も使用できると思います。
#### UIテスト
javascriptであれば[mocha](https://mochajs.org/index.html)が有名だと思います。

## ブラウザ自動化ツールを知っている
これも[Selenium](https://selenium.dev/)が有名だと思います。
GUIテストツールについてまとめてくれている[jun2014さんの記事](https://qiita.com/jun2014/items/8cabbd52830904af49af)があったので紹介しておきます。

## テスト自動化ツールを知っている
皆さんCIという言葉を聞いたことがある方がいると思います。
CIは`Continuous Integration`の略でPRを出した時やプッシュした時に自動で私たちの書いたテストを行ってくれます。
CIは有名どころでは[CircleCI](https://circleci.com/)や[GithubActions](https://github.co.jp/features/actions)などが主に使われていると思います。
使ったことがない人はぜひドキュメントを読んで使ってみてください。

# Polyfill
## なぜ必要なのか説明できる
Javascriptのところで書きましたがJavascriptはブラウザの対応によっては書いたコードが動かない可能性があります、例えばMathを利用している時IEではES5までしか対応していないためMathライブラリがそもそも存在しないため動かないです。

そのためES6で書いたコードをES5に書き換える必要があり、そのために[Polyfill](https://polyfill.io/v3/)/[Babel](https://babeljs.io/)が必要です。
PolyfillはES5に存在しない関数やライブラリの代替コードのことで
BabelはES6記法をES5にトランスパイルしてくれるものという違いです。
実際にはbabelの中でpolyfillが動いていて対象ブラウザによってpolyfillから代替コードをとってくる形となっています。
[リクルートの記事](https://engineer.recruit-lifestyle.co.jp/techblog/2019-12-08-babel-approach/)が挙動を理解しやすかったです。
## 開発においてbabel、core-jsのセットアップができる
ドキュメントを読みましょう
[babel](https://babeljs.io/en/setup)
[core-js](https://github.com/zloirock/core-js)

# 宣言的UI
宣言的(declarative)という言葉がreactやvueなどのモダンな技術のドキュメントには書いてあります、私自身vueから入ったため命令的な書き方をするJQueryをほとんど触ったことがなく、なぜこんなにもreactやvueが持て囃されているのだろうと思っていました、
しかし宣言的UIについて詳しく書かれている記事を読むことによって現在のこのフロントエンド技術のトレンドがなぜこのようなことになっているのかということが腑に落ちました。
どのような経緯があり今の状態になっているのかを理解すれば学習するのが楽になると思います。（少なくとも自分はそうでした）


コードの書き方には宣言的な書き方と命令的な書き方があります、命令的な書き方を使用する技術としては上記したようにJQueryがあげられるのでないかと思います。

一方宣言的な書き方をする技術としてHTML/XAMLなどが思い浮かぶ方もいるかと思いますが、reactに端を発する宣言的UIを採用したフロントエンド技術の広がりは仮想DOMを使用することによって実現したHTMLなどとは異なったものです。

## 従来のJSによるDOM操作とは異なるものだと理解して説明できる
```js:sample.js
// 命令的な書き方
<script>
const main = document.createElement("main");
let message = "こんにちは";
const input = document.createElement("input");
input.value = message;
input.addEventListener("input", inputHandler);
main.appendChild(input);
const p1 = document.createElement("p");
p1.innerHTML = message;
main.appendChild(p1);
document.body.appendChild(main);
```
```vue:sample.vue
// 宣言的な書き方
<template>
  <main>
    <input type="text" v-model="message">
    <p>{{ message }}</p>
  </main>
</template>
<script>
  data() {
    return{
      message: "こんにちは"
    }
  }
</script>
```
[Yametaroさんの記事](https://qiita.com/Yametaro/items/3c27305072464e1d6230)からお借りしました
上記のふたつのコードは同じものを実装したコードですがみてわかるように宣言的な書き方のほうが直感的です。
今回の例のように簡単なものであればなんとかなりますが、DOMの追加などが発生する場合、最終的なビューを想像するのが難しいです。

#### なぜ最初から宣言的な書き方をするものが流行らずに命令的な書き方をするものが使用されていたのだろう？？？
普通に考えて直感的なほうが使いやすくて良いはずなのになぜだろうと無知な私は思いました。
理由は簡単です、実現できていなかったからです。

宣言的UIを実現することは中身が静的なものであれば問題ありません、問題は中身が動的なものとなり再描画が必要となった時です。
一つの要素が追加される度に画面全体を再描画していては重すぎて現実的に使うのは厳しいですが、
そこを解決したのが仮想DOMによる差分の再描画です。

簡単にいうと`宣言的な書き方を実現するために必要な再描画速度を使用可能なレベルで世の中に提供したのがreactであり、そのために仮想DOMが使用されている`ということです。
なのでsonatardさんのスライドでもおっしゃっていますが　`モダンで良さそうだからreactやvueを使うではなく、宣言的な書き方をしたいからreactやvueを使う`という考えが大切だと思います。

また宣言的UIの実現によって命令的な書き方では最終的なビューは前回の実行結果に依存していたのが状態の変化に応じて再描画するようになったことで前回の実行結果に依存しなくなりました。

仮想DOMによる再描画の話は私自身深く理解しておらず、変な説明をして混乱させてしまう可能性もあり、理解したい方は以下の記事を読んでいただければと思います。

[mizchiさんの記事](https://qiita.com/mizchi/items/4d25bc26def1719d52e6)や
[sonatardさんのスライド](https://speakerdeck.com/sonatard/xuan-yan-de-ui)
[erukitiさんの記事](https://qiita.com/erukiti/items/fb7bcbd9d79696579d06)を読めばより深く理解していただけると思います。

## 採用しているライブラリ、フレームワークを知っている
vue、react、SwiftUI、Flutterなどがあります、ドキュメントに`declarative`と書いてある技術は採用していると考えていいと思います。
## 状態管理を考慮することができる
宣言的な書き方ができるようになったことによって、状態を分離して書くことができるようになりました、状態をどのようにもつのかについて考える状態管理を考慮する必要があります。

状態管理の方法としてはローカル（コンポーネントごと）で保持する方法とグローバルで保持する方法（アプリケーション全体）のふたつがあります

### ステートを保持する場所（ローカル、グローバル）を理解している
- ローカル

それぞれのコンポーネントで状態を持つことで、複数のコンポーネントがそれぞれ状態を持つ場合、全体の状態の把握が難しい一方で
それぞれのコンポーネントが状態を含むのでコンポーネントだけで全ての機能が完結し再利用性が高いです、
ReactHooksなどはこちらです。

- グローバル

それぞれのコンポーネントで持つのではなく単一のストアと呼ばれる場所で状態を管理しそれぞれのコンポーネントで呼び出します。

単一の箇所に状態が全て書いてあるので全体の状態の把握が容易である一方で、どのコンポーネントがどの状態を持っているのかが把握しにくいことコンポーネントの再利用性が低くなってしまうことが問題点としてあげられます。
vuex、reduxなどはこちらです。

基本的にそれほど規模の大きくない開発ではローカルで状態管理を行って問題ないと思います、規模が大きくステートを渡す際にいわゆるバケツリレーになってしまうなど辛い状態になってしまうのであればグローバルでのステート管理を行うべきであると思います、ただreacthooksではuseReducerを使えばバケツリレーを防ぐことができるようなのでどちらを採用するのかはよく考える必要があると思います。

react/nextの状態管理については[takepepeさんの記事](https://zenn.dev/takepepe/articles/state-manegement-in-nextjs-2020)がとてもわかりやすかったです。

### ステートを受け渡す方法を知っている
ステートは状態のことを指します、ステートの渡し方は基本的にはローカルとグローバル同じで、
状態を保持するコンポーネントからそのステートが必要なコンポーネントがpropsとして受け取る形になります、
しかしグローバルでステートを定義している場合グローバルステートから直接必要なコンポーネントにステートを渡すことができます。
```ts:store.ts
import Vue from 'vue'
import Vuex from 'vuex'
export default new Vuex.Store({
  state: {
    user: "taro"
  }
})
```
```vue:sample.vue
<template>
  <!-- <p>taro</p> -->
  <p>{{ this.user }}</p>
</template>
<script>
  data() {
    return {
      user: ""
    }
  }
  // グローバルステートにアクセスして値を必要なコンポーネントで取得することができる。
  created() {
    this.user = this.$store.state.user
  }
</script>
```
### 更新方法を知っている
ステートの更新方法は
- コールバック関数で更新する
- イベントを発行する

があります。
```ts:sample.tsx
// ローカルステート
import React, { useState } from 'react';

function Example() {
  // ローカルステートとして`count`を定義、setCountが更新のための関数
  const [count, setCount] = useState(0);

  return (
    <div>
      <p>You clicked {count} times</p>
      // クリック時にsetCount関数を呼びcountに1を足す
      <button onClick={() => setCount(count + 1)}>
        Click me
      </button>
    </div>
  );
}
```
```ts:store.ts
// グローバルステート
import Vue from 'vue'
import Vuex from 'vuex'
export default new Vuex.Store({
  state: {
    loading: false
  }
  mutations: {
    // setLoading関数の第二引数として受け取ったものにステートを更新する
    setLoading(state, payload) {
      state.loading = payload
    }
  }
})
```
```vue:sample.vue
<template>
  <button @click="isLoading">ローディング開始</button>
</template>
<script>
  // グローバルステートにアクセスして値を必要なコンポーネントで取得することができる。
  isLoading() {
    // setLoading関数を呼び第二引数にtrueを渡す
    this.$store.commit("setLoading", true)
  }
</script>
```
### バインディングは単方向か双方向であるか理解している
バインディングには単方向バインディングと双方向バインディングがあります。

単方向バインディングは一方通行に同期を行うことでデータを受け取って見ることしかできないことなどがあげられます

双方向バインディングは双方向に同期を行うことができ、例えばフォームに入力した値がすぐに反映されることなどがあげられます。

双方向バインディングを提供しているフロントエンドフレームワークとしてはVueJsがあげられます、vueはv-modelというディレクティブを提供しており、これにより双方向バインディングが可能です、サンプルコードを添付しておきます。
```vue:sample.vue
<template>
  <div id="app">
  <p>
    // v-modelを使用することによって入力した値がすぐにpタグに入る
    <input type="text" v-model="message">
  </p>
  <p>
    {{ message }}
  </p>
</div>
</template>
<script>
  data() {
    return {
      message: 'Hello Vue.js!
    }
  }
</script>
```

# バンドルツール
バンドルツールとは`css`や`javascript`ファイルなどの複数のモジュールの依存関係を解消して一つにまとめてくるものです。
これによって開発時に扱いやすくなる他、http/1.1を使用している場合、リクエスト回数を減らすことでパフォーマンスの観点でも利点があります。

最近のフロントエンドフレームワークだとビルド時に自動で行っていたりする（distファイルとかに出力されていると思います）のでわかりにくいですが、理解しておかないといけないものです。
[Kosukeeeさんの記事](https://note.com/billion_dollars/n/n596fecfdeb2e)がよくまとまっていると思います。

## Webpack, gulp, rollup, parcelというツールが何をするものか理解している
### Webpack
Node.jsでサーバーサイドで動かすモジュールバンドラーツールです、バンドルツールの中では圧倒的な使用率でこれさえ使えれば問題ないのではないでしょうか？
（採用理由とかを考えるのであれば他との比較も必要です）
日本人のwebpackコミッターのhirropyさんがいるのでブログ読むのがおすすめです。
[hiroppyさんのブログ](https://blog.hiroppy.me/entry/mechanism-of-webpack)

webpackにおいて革新的だったのは以下の二点で
>コード分割機能によって、アプリケーションを管理可能なチャンクに分割して、オンデマンドで読み込めるようにします。これによりユーザーは、アプリケーション全体のダウンロードとパースを待つ場合よりも、はるかに早くインタラクティブなサイトを使用できるようになります。これは手作業でも可能ではありますが、大変な作業になります。

>画像やCSSなどの静的アセットをアプリケーションにインポートして、依存性グラフ内で別ノードとして扱うことができます。ファイルを適切なフォルダに格納する作業に神経を使ったり、ファイルのURLにハッシュを追加するためにスクリプトをいじったりする必要はありません。webpackが自動で処理してくれます。

[reactのバンドルツールの記事](https://postd.cc/webpack-and-rollup-the-same-but-different/)から参照

### gulp
Node.jsをベースとしたタスクランナーで`gulpfile.js`に定義したタスクを`$ npx gulp minify-css`のような形で呼び出して実行することができます。
cssの圧縮はこんな感じで定義します
```js:guplfile.js
gulp.task("minify-css", function() {
    gulp.src('src/*.css')
        .pipe(cleanCSS())
        .pipe(gulp.dest('dest/'));
});
```
圧縮などの機能は`gulp`や`npm script`で行われていたのですが、最近ではバンドルツールに圧縮してくれる機能が入ったりしてしまっています。

### rollup
[rollup](https://rollupjs.org/guide/en/)はwebpackと同じくモジュールバンドラーツールですが、webpackなどの他のモジュールバンドラーがes5を前提としていますがrollupはes6を前提としており、一旦全てes6に変換してからbabelでes5に変換します。

これによりes6の`tree shaking`を使用することが可能です。
[tree shaking](https://developer.mozilla.org/ja/docs/Glossary/Tree_shaking)

```js:rollup.js
// commonjs(es5)
const pkg = require("pkg")
// es6
import { sample } from 'pkg'
```
不要なパッケージなどをコンパイル時に削除することでバンドルサイズを小さくすることができます。

しかし、rollupを通した時にes6に変換されないものがあるというのが欠点らしいです。
なので
`コード分割が必要で静的アセットを多用しており、es5がベースの場合はwebpack、es6をベースとしており、他者も使用できるものを作成しているのであればrollupを使うべき`です。
実際にreactは他者も使用することができるライブラリなのでrollupを用いてバンドルしているそうです。
[reactのバンドルツールの記事](https://postd.cc/webpack-and-rollup-the-same-but-different/)がとてもわかりやすかったです。

### parcel
これもモジュールバンドラーツールです。
webpackとの違いとしてはwebpackは多機能で細かいところまで設定することができる、一方parcelは細かい設定はできないが手軽にバンドルをすることができるという点で異なります。

しかしビルドしたファイルの出力先や出力するファイル名を指定することができません、これは画像やcssは別ファイルに切り出したいと思っている人からすると厳しいのではないでしょうか。
しかし、ビルドが早いという特徴を持っています。

https://qiita.com/soarflat/items/3e43368b2d767c730781

## どういったものがバンドルされているか分析することができる
本来バンドルツールはjavascriptしか読み込めないですが、ローダーを使用することによってcssなども読み込むことができます。
（css-loaderを使用することでcssを読み込むことができるなど）

そのため`css` `javascript` `画像` `webフォント` `音声` `画像`などがバンドルされると思っていいと思います。

## 圧縮してファイルサイズを小さくすることができる
`html` `css` `javascript` `画像`ファイルを圧縮する際には`gulp`などのタスクランナーを使用します。

[gulpでのjsの圧縮](https://riptutorial.com/gulp/example/11376/minify-js-using-gulp-minify)
また上記したようにwebpackでも提供しています。
[webpackでのcssの圧縮](https://webpack.js.org/plugins/mini-css-extract-plugin/#minimizing-for-production)
## 開発モードとプロダクションモードの切り替えができる
おそらくほとんどの人が`npm script`で`NODE_ENV`を用いて切り替えを行っていると思います。
これによりlocalhostのポート番号をモードごとに変更したり、開発モードの時だけ手元の環境変数を渡したりすることができます。

```json:package.json
  {
  "scripts": {
    "stg": "NODE_ENV=stg webpack --mode production",
    "prd": "NODE_ENV=prd webpack --mode production",
    "start": "NODE_ENV=dev webpack-dev-server --mode development --hot --inline --watch-content-base"
  },
```

開発モードの時は`npm run start`で
プロダクションモードの時は`npm run stg` `npm run prd`を走らせる。

[webpackのサンプル](https://webpack.js.org/guides/environment-variables/)
[kenmoriさんのgist](https://gist.github.com/kenmori/ef48f09e9f1bfb30fe34cc11db0031d6)
## Code Splittingが何をしているものか理解している
Code Splittingはバンドルしたファイルを適切なファイルサイズに分割することで、http/1.1ではリクエスト回数を減らすことによりパフォーマンスを向上することができたがバンドルサイズが巨大すぎるとそのファイルが読み込まれない限り、画面にcssやjavascriptが適用されないという問題が起こります。

この問題を解決するためにコードを適切なファイルサイズに分割する必要があります。（もちろん大したファイルサイズでなければ分割しなくて良いです）

これにはhttp/2でストリームの導入により一つのコネクションで並行して複数のリクエスト/レスポンスの処理ができるようになったこともあるような気がします。

基本的に分割方法としては
`Page`ページごとの分割、
`Fold`初期描画画面内にあるかないかでの分割、
`Temporal`モーダルなどの最初の読み込みに必要かどうかの分割
の三つで行われているようです。

[seyaさんの記事](https://qiita.com/seya/items/06b160adb7801ae9e66f)がcode splittingについて詳しく説明してくれています。
[numb_86さんの記事](https://numb86-tech.hatenablog.com/entry/2020/12/01/093704)がwebpackでの分割方法についてわかりやすいです。
# 参照
https://zenn.dev/yamanoku/scraps/632e570e160251839d48
https://qiita.com/okmttdhr/items/c1e80353928e121c4761
https://www.qbook.jp/column/20181009_713.html
https://digitalsoul.hatenadiary.org/entry/20090819/1250686015
https://qiita.com/erukiti/items/fb7bcbd9d79696579d06
https://zenn.dev/takepepe/articles/state-manegement-in-nextjs-2020
https://postd.cc/webpack-and-rollup-the-same-but-different/
https://engineer.recruit-lifestyle.co.jp/techblog/2019-12-08-babel-approach/
https://numb86-tech.hatenablog.com/entry/2020/12/01/093704