---
id: general
title: 一般的事項
hide_title: true
---

# Redux FAQ: 一般的事項

<!--
## 目次

- [When should I learn Redux?](#when-should-i-learn-redux)
- [When should I use Redux?](#when-should-i-use-redux)
- [Can Redux only be used with React?](#can-redux-only-be-used-with-react)
- [Do I need to have a particular build tool to use Redux?](#do-i-need-to-have-a-particular-build-tool-to-use-redux)
-->

## 一般的事項

### Redux をいつ学ぶべきですか？

何を学ぶべきかというのは、JavaScript 開発者にとって由々しき問題になりえます。その回答如何で、一度にひとつのことだけを学び、業務での問題に集中し、選択の幅を狭めることができるのですから。Redux とは、アプリケーションのステートを管理するためのパターンです。ステート管理に問題を感じていなければ、Redux のメリットは理解しづらいかもしれません。一部の UI ライブラリ（React など）には、独自のステート管理システムがあります。これらのライブラリを使用している場合、特にそれらの使い方を学んでいる最中の場合には、まずそのシステム組み込みの機能を学ぶことをお勧めします。それがアプリケーションを構築するのに必要なすべてかもしれないのです。アプリケーションが複雑になり、ステートがどこに保存されどのように変化するのかで混乱するようになってきたら、Redux を学ぶのに良いタイミングです。Redux が抽象化しようとしている複雑さを先に経験しておくことが、その抽象化を効果的に業務に適応するためのとても良い事前準備となるでしょう。

#### 参考情報

**記事**

- [Deciding What Not To Learn](http://gedd.ski/post/what-not-to-learn/)
- [How to learn web frameworks](https://ux.shopify.com/how-to-learn-web-frameworks-9d447cb71e68)
- [Redux vs MobX vs Flux vs... Do you even need that?](https://goshakkk.name/redux-vs-mobx-vs-flux-etoomanychoices/)

**ディスカッション**

- [Ask HN: Overwhelmed with learning front-end, how do I proceed?](https://news.ycombinator.com/item?id=12882816)
- [Twitter: If you want to teach someone to use an abstraction...](https://twitter.com/acemarke/status/901329101088215044)
- [Twitter: it was never intended to be learned before...](https://twitter.com/dan_abramov/status/739961787295117312)
- [Twitter: Learning Redux before React?](https://twitter.com/dan_abramov/status/739962098030137344)
- [Twitter: The first time I used React, people told me I needed Redux...](https://twitter.com/raquelxmoss/status/901576285020856320)
- [Twitter: This was my experience with Redux...](https://twitter.com/garetmckinley/status/901500556568645634)
- [Dev.to: When is it time to use Redux?](https://dev.to/dan_abramov/comment/1n2k)

### React をいつ使うべきですか？

Redux を使うことを当然のことのように考えるべきではありません。

React の初期の貢献者である Pete Hunt はこう述べています：

> Flux が必要になれば時が教えてくれる。必要かどうか分からないのなら、必要ないということだ。

同様に、Redux の作者の 1 人である Dav Abramov によれば：

> この記事の訂正：素の React で問題がないなら、Redux を使ってはいけません。

一般的には、時間の経過とともに変化する相当な量のデータがある場合、単一の信頼できる情報源 (single source of truth) が必要な場合、トップレベルの React コンポーネントの state にすべて保持するといったアプローチではもはやうまく行かないと分かった場合に、Redux を使うようにしてください。

しかし、Redux にはトレードオフがあるということを理解することも重要です。Redux はコードを書くための最短・最速の方法として設計されているわけではありません。予測可能な動作によって、「あるステートの断片がいつ変化したのか、そのデータはどこから来たのか」という質問に答えられるようにすることを目的としています。そのために、アプリケーションのステートをプレーンなデータとして保持し、変更をプレーンなオブジェクトで記述し、その変更を処理するのに更新をイミュータブルに適用する純関数を使う、といった、特定の制約をあなたに課そうとします。これがしばしば「ボイラープレート」に関する不満の原因ともなっています。これらの制約は開発者側に労力を要求する一方で、多くの可能性（永続的なストアや同期など）をもたらすものでもあります。

つまるところ Redux とは単なる道具です。すばらしい道具であり、使うべきすばらしい理由があるのですが、あなたがそれを使わないという理由もまたあるのです。自分の使う道具について知った上で判断を行い、それぞれの判断におけるトレードオフを理解するようにしてください。

#### 参考情報

**ドキュメント内**

- [Thinking in Redux: Motivation](../understanding/thinking-in-redux/Motivation.md)

**記事**

- **[When (and when not) to reach for Redux](https://changelog.com/posts/when-and-when-not-to-reach-for-redux)**
- **[The Tao of Redux, Part 1 - Implementation and Intent](https://blog.isquaredsoftware.com/2017/05/idiomatic-redux-tao-of-redux-part-1/)**
- [You Might Not Need Redux](https://medium.com/@dan_abramov/you-might-not-need-redux-be46360cf367)
- [The Case for Flux](https://medium.com/swlh/the-case-for-flux-379b7d1982c6)

**ディスカッション**

- [Twitter: Don't use Redux until...](https://twitter.com/dan_abramov/status/699241546248536064)
- [Twitter: Redux is designed to be predictable, not concise](https://twitter.com/dan_abramov/status/733742952657342464)
- [Twitter: Redux is useful to eliminate deep prop passing](https://twitter.com/dan_abramov/status/732912085840089088)
- [Twitter: Don't use Redux unless you're unhappy with local component state](https://twitter.com/dan_abramov/status/725089243836588032)
- [Twitter: You don't need Redux if your data never changes](https://twitter.com/dan_abramov/status/737036433215610880)
- [Twitter: If your reducer looks boring, don't use redux](https://twitter.com/dan_abramov/status/802564042648944642)
- [Reddit: You don't need Redux if your app just fetches something on a single page](https://www.reddit.com/r/reactjs/comments/5exfea/feedback_on_my_first_redux_app/dagglqp/)
- [Stack Overflow: Why use Redux over Facebook Flux?](http://stackoverflow.com/questions/32461229/why-use-redux-over-facebook-flux)
- [Stack Overflow: Why should I use Redux in this example?](http://stackoverflow.com/questions/35675339/why-should-i-use-redux-in-this-example)
- [Stack Overflow: What could be the downsides of using Redux instead of Flux?](http://stackoverflow.com/questions/32021763/what-could-be-the-downsides-of-using-redux-instead-of-flux)
- [Stack Overflow: When should I add Redux to a React app?](http://stackoverflow.com/questions/36631761/when-should-i-add-redux-to-a-react-app)
- [Stack Overflow: Redux vs plain React?](http://stackoverflow.com/questions/39260769/redux-vs-plain-react/39261546#39261546)
- [Twitter: Redux is a platform for developers to build customized state management with reusable things](https://twitter.com/acemarke/status/793862722253447168)

### Redux は React と組み合わせてしか使えないのですか？

Redux は、あらゆる UI レイヤーのデータストアとして使用できます。最も一般的な使用場所は React と React Native ですが、Angular、Angular 2、Vue、Mithril などで利用できるバインディングもあります。Redux は、単純に他のあらゆるコードが利用できるサブスクリプションの仕組みを提供しているだけです。とはいえ、React やその類似ライブラリのように、ステートの変化から UI の更新を推論できる宣言的ビューの実装と組み合わせた時に最も有用性を発揮します。

### Redux を使うのに特定のビルドツールは必要ですか？

Redux はまず ES6 で書かれた後に、Webpack と Babel で本番用の ES5 へとトランスパイルされています。JavaScript のビルドプロセスに関係なく利用できるはずです。Redux には、ビルドプロセスを一切使わずに直接使用できる UMD ビルドもあります。[counter-vanilla](https://github.com/reduxjs/redux/tree/master/examples/counter-vanilla) サンプルは、基本的な ES5 と `<script>` で読み込まれた Redux でどのように使用するかについてのデモです。関連するプルリクエストでは以下のように述べられています：

> この「素の JavaScript によるカウンタ」サンプルは、Webpack だの React だのホットリローディングだの saga だのアクションクリエータだの定数だの Babel だの npm だの CSS modules だのデコレータだの、はたまたラテン語能力だの Egghead への課金登録だの博士号だのホグワーツ魔法魔術学校での優秀な成績だのが Redux にとって必要だ……という神話を消し去るためのものです。
>
> いいえ、ここにあるのは HTML と、伝統工芸的な `<script>` タグが幾つかと、何の変哲もない DOM 操作だけです！ 楽しんでください！
