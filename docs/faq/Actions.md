---
id: actions
title: アクション
hide_title: true
---

# Redux FAQ: アクション

<!--
## Table of Contents

- [Redux FAQ: Actions](#redux-faq-actions)
  - [Table of Contents](#table-of-contents)
  - [Actions](#actions)
    - [Why should `type` be a string, or at least serializable? Why should my action types be constants?](#why-should-type-be-a-string-or-at-least-serializable-why-should-my-action-types-be-constants)
      - [Further information](#further-information)
    - [Is there always a one-to-one mapping between reducers and actions?](#is-there-always-a-one-to-one-mapping-between-reducers-and-actions)
      - [Further information](#further-information-1)
    - [How can I represent “side effects” such as AJAX calls? Why do we need things like “action creators”, “thunks”, and “middleware” to do async behavior?](#how-can-i-represent-side-effects-such-as-ajax-calls-why-do-we-need-things-like-action-creators-thunks-and-middleware-to-do-async-behavior)
      - [Further information](#further-information-2)
    - [What async middleware should I use? How do you decide between thunks, sagas, observables, or something else?](#what-async-middleware-should-i-use-how-do-you-decide-between-thunks-sagas-observables-or-something-else)
    - [Should I dispatch multiple actions in a row from one action creator?](#should-i-dispatch-multiple-actions-in-a-row-from-one-action-creator)
      - [Further information](#further-information-3)
-->

## アクション

### `type` が文字列であるべき、あるいは少なくともシリアライズ可能であるべき理由は？ それを定数で宣言すべき理由は？

ステートと同様にアクションがシリアライズ可能であることにより、タイムトラベルデバッギングやアクションの記録・再生など、Redux の特徴的な機能が使えるようになります。アクションの `type` 値として `Symbol` を使ったり、アクションの型を調べるのに `instanceof` を使ったりすると、これがうまく動きません。文字列が良いのはシリアライズ可能であり自己記述的だからです。ただしミドルウェアにアクションを処理させるつもりの場合は、シンボルや Promise やその他シリアライズできない値をアクション内で使うことは構いません。アクションは実際にストアに届いてリデューサに渡されるまでにシリアライズ可能になっていれば良いのです。

パフォーマンス上の理由により、アクションがシリアライズ可能であることを強制することはできないため、Redux はアクションがプレーンオブジェクトであり `type` が存在することのみ確認します。残りはあなたの自由ですが、すべてをシリアライズ可能にしておくことでデバッグや問題の再現の際の助けになります。

よく使われるコードをカプセル化して集中的に取り扱うというのはプログラミングにおける共通のコンセプトです。アクションオブジェクトをあらゆる場所で手で作り `type` を手書きすることは確かに可能ですが、再利用可能な定数を定義することでコードのメンテナンスが容易になります。定数を別ファイルに置いておくことで [`import` 文の誤字をチェックできる](https://www.npmjs.com/package/eslint-plugin-import)ようになり、うっかり間違った文字列を使ってしまうことがなくなります。

#### 参考情報

**ドキュメント内**

- [Reducing Boilerplate](../recipes/ReducingBoilerplate.md#actions)

**ディスカッション**

- [#384: Recommend that Action constants be named in the past tense](https://github.com/reactjs/redux/issues/384)
- [#628: Solution for simple action creation with less boilerplate](https://github.com/reactjs/redux/issues/628)
- [#1024: Proposal: Declarative reducers](https://github.com/reactjs/redux/issues/1024)
- [#1167: Reducer without switch](https://github.com/reactjs/redux/issues/1167)
- [Stack Overflow: Why do you need 'Actions' as data in Redux?](http://stackoverflow.com/q/34759047/62937)
- [Stack Overflow: What is the point of the constants in Redux?](http://stackoverflow.com/q/34965856/62937)

### リデューサとアクションは常に 1 対 1 に対応しているのですか？

いいえ。ステートの特定の一部分への更新を担当する独立した小さなリデューサ関数を複数書くことをお勧めします。このパターンを「リデューサコンポジション」と呼んでいます。あるアクションはすべてのリデューサが処理することも、どのリデューサも処理しないこともありえます。アクションはステートツリーの複数の場所に影響する可能性があり、コンポーネントはそのことを知っておく必要がないため、コンポーネントと実際のステートの変化との間の密結合を避けることができます。ユーザによっては "ducks" ファイルパターンなどでリデューサとアクションをより強く結びつけるようにしていますが、デフォルトでは 1 対 1 のマッピングがあるわけでは決してありませんし、1 つのアクションを多数のリデューサで処理したくなった場合はいつでもそのような枠組みから抜け出すべきです。

#### 参考情報

**ドキュメント内**

- [Fundamentals: State, Actions, Reducers](../tutorials/fundamentals/part-3-state-actions-reducers.md)
- [Recipes: Structuring Reducers](../recipes/structuring-reducers/StructuringReducers.md)

**ディスカッション**

- [Twitter: most common Redux misconception](https://twitter.com/dan_abramov/status/682923564006248448)
- [#1167: Reducer without switch](https://github.com/reactjs/redux/issues/1167)
- [Reduxible #8: Reducers and action creators aren't a one-to-one mapping](https://github.com/reduxible/reduxible/issues/8)
- [Stack Overflow: Can I dispatch multiple actions without Redux Thunk middleware?](http://stackoverflow.com/questions/35493352/can-i-dispatch-multiple-actions-without-redux-thunk-middleware/35642783)

### AJAX コールなどの「副作用」をどのように表現しますか？ 非同期処理のために「アクションクリエータ」「thunk」「ミドルウェア」のようなものが何故必要なのですか？

これは長くて複雑なトピックであり、どのようにコードを書きどのようなアプローチをとるべきかについては様々な意見が存在しています。

何かしらの意味のあるあらゆるウェブアプリは、AJAX リクエスト作成のような非同期処理も含む、複雑なロジックを有しています。そのようなコードは入力に対する純粋な関数にはならず、外の世界とのやりとりのことは[「副作用 (side effect)」](https://en.wikipedia.org/wiki/Side_effect_%28computer_science%29)として知られています。

Redux は関数型プログラムにインスパイアされて作成されているため、デフォルトでは副作用を処理する場所がありません。特に、リデューサ関数は*常に* `(state, action) => newState` 型の純関数である必要があります。しかし、Redux のミドルウェアの仕組みにより、ディスパッチされたアクションを途中で捕まえて、副作用も含む複雑な挙動を加えることができます。

一般的に、Redux では副作用を有するコードをアクション作成のプロセスの一環として書くことが勧められます。そのようなロジックを UI コンポーネントの中で実行すること*も*可能ですが、再利用可能な関数（つまりアクションクリエータ）として抽出して複数の場所から呼べるようにすることは理にかなっています。

これを行う最もシンプルな方法は [Redux Thunk](https://github.com/gaearon/redux-thunk) ミドルウェアを追加して、複雑なロジックや非同期のロジックを含むアクションクリエータを書けるようにすることです。他の広く使われている方法としては [Redux Saga](https://github.com/yelouafi/redux-saga) があり、より同期的な見た目のコードをジェネレータで書いて、Redux アプリ内で「バックグラウンドスレッド」や「デーモン」のように動かすことができます。別のアプローチとしては [Redux Loop](https://github.com/raisemarketplace/redux-loop) があり、処理を反転させて、ステート変化に反応して起こる副作用をリデューサ内で宣言し、それらが独立して実行されるようにします。このほかにもコミュニティで開発されたライブラリやアイディアが*たくさん*あり、それぞれが副作用をどのように管理するかについて別の考えを持っています。

#### 参考情報

**ドキュメント内**

- [Redux Fundamentals: Async Logic and Data Flow](../tutorials/fundamentals/part-6-async-logic.md)
- [Redux Fundamentals: Store - Middleware](../tutorials/fundamentals/part-4-store.md#middleware)

**記事**

- [Redux Side-Effects and You](https://medium.com/@fward/redux-side-effects-and-you-66f2e0842fc3)
- [Pure functionality and side effects in Redux](http://blog.hivejs.org/building-the-ui-2/)
- [From Flux to Redux: Async Actions the easy way](http://danmaz74.me/2015/08/19/from-flux-to-redux-async-actions-the-easy-way/)
- [React/Redux Links: "Redux Side Effects" category](https://github.com/markerikson/react-redux-links/blob/master/redux-side-effects.md)
- [Gist: Redux-Thunk examples](https://gist.github.com/markerikson/ea4d0a6ce56ee479fe8b356e099f857e)

**ディスカッション**

- [#291: Trying to put API calls in the right place](https://github.com/reactjs/redux/issues/291)
- [#455: Modeling side effects](https://github.com/reactjs/redux/issues/455)
- [#533: Simpler introduction to async action creators](https://github.com/reactjs/redux/issues/533)
- [#569: Proposal: API for explicit side effects](https://github.com/reactjs/redux/pull/569)
- [#1139: An alternative side effect model based on generators and sagas](https://github.com/reactjs/redux/issues/1139)
- [Stack Overflow: Why do we need middleware for async flow in Redux?](http://stackoverflow.com/questions/34570758/why-do-we-need-middleware-for-async-flow-in-redux)
- [Stack Overflow: How to dispatch a Redux action with a timeout?](http://stackoverflow.com/questions/35411423/how-to-dispatch-a-redux-action-with-a-timeout/35415559)
- [Stack Overflow: Where should I put synchronous side effects linked to actions in redux?](http://stackoverflow.com/questions/32982237/where-should-i-put-synchronous-side-effects-linked-to-actions-in-redux/33036344)
- [Stack Overflow: How to handle complex side-effects in Redux?](http://stackoverflow.com/questions/32925837/how-to-handle-complex-side-effects-in-redux/33036594)
- [Stack Overflow: How to unit test async Redux actions to mock ajax response](http://stackoverflow.com/questions/33011729/how-to-unit-test-async-redux-actions-to-mock-ajax-response/33053465)
- [Stack Overflow: How to fire AJAX calls in response to the state changes with Redux?](http://stackoverflow.com/questions/35262692/how-to-fire-ajax-calls-in-response-to-the-state-changes-with-redux/35675447)
- [Reddit: Help performing Async API calls with Redux-Promise Middleware.](https://www.reddit.com/r/reactjs/comments/469iyc/help_performing_async_api_calls_with_reduxpromise/)
- [Twitter: possible comparison between sagas, loops, and other approaches](https://twitter.com/dan_abramov/status/689639582120415232)

### どの非同期ミドルウェアを使うべきですか？ thunk, saga, observerble などのどれを使うかどう決めれば良いでしょうが？

[多くの非同期/副作用関連のミドルウェア](https://github.com/markerikson/redux-ecosystem-links/blob/master/side-effects.md)がありますが、特によく使われているのは [`redux-thunk`](https://github.com/reduxjs/redux-thunk)、[`redux-saga`](https://github.com/redux-saga/redux-saga)、[`redux-observable`](https://github.com/redux-observable/redux-observable) です。これらは異なるツールであり、それぞれに利点・欠点・ユースケースが存在します。

一般的な経験則として：

- thunk は複雑かつ同期的なロジック（特に Redux ステート全体へのアクセスを必要とするもの）や、単純な非同期ロジック（例えば基本的な AJAX コール）に最適です。`async/await` を使えば、Promise ベースのより複雑なロジックに対して合理的に使うことも可能です。
- saga は複雑な非同期処理や疎結合の「バックグラウンドスレッド」タイプの挙動、特にディスパッチされたアクションをリッスンしないといけない場合（これは thunk では不可能）に最適です。ES6 のジェネレータ関数および `redux-saga` の「副作用」演算子に通じている必要があります。
- observable は saga と同じ問題を解決しますが、非同期処理を実装するために RxJS に依存しています。RxJS の API に通じている必要があります。

ほとんどの Redux ユーザは thunk から始め、アプリでより複雑な非同期ロジックの処理が本当に必要となってから、追加で saga や observable のような副作用処理ライブラリを追加することをお勧めします。

saga と observable は同一のユースケースを有しているため、アプリは通常どちらか一方しか使いません。しかし **thunk と saga/observable のどちらか一方とを組み合わせて使うことは全く構わない**ということに留意してください。それらは別の問題を解決するものだからです。

**記事**

- [Decembersoft: What is the right way to do asynchronous operations in Redux?](https://decembersoft.com/posts/what-is-the-right-way-to-do-asynchronous-operations-in-redux/)
- [Decembersoft: Redux-Thunk vs Redux-Saga](https://decembersoft.com/posts/redux-thunk-vs-redux-saga/)
- [Redux-Thunk vs Redux-Saga: an overview](https://medium.com/@shoshanarosenfield/redux-thunk-vs-redux-saga-93fe82878b2d)
- [Redux-Saga V.S. Redux-Observable](https://hackmd.io/s/H1xLHUQ8e#side-by-side-comparison)

**ディスカッション**

- [Reddit: discussion of using thunks and sagas together, and pros and cons of sagas](https://www.reddit.com/r/reactjs/comments/8vglo0/react_developer_map_by_adamgolab/e1nr597/)
- [Stack Overflow: Pros/cons of using redux-saga with ES6 generators vs redux-thunk with ES2017 async/await](https://stackoverflow.com/questions/34930735/pros-cons-of-using-redux-saga-with-es6-generators-vs-redux-thunk-with-es2017-asy)
- [Stack Overflow: Why use Redux-Observable over Redux-Saga?](https://stackoverflow.com/questions/40021344/why-use-redux-observable-over-redux-saga/40027778#40027778)

### 1 つのアクションクリエータから連続して複数のアクションをディスパッチすべきですか？

アクションをどのように構成すべきかについて特定のルールはありません。Redux Thunk のような非同期ミドルウェアを使えば、複数の関連するアクションを連続的にディスパッチしたり、AJAX リクエストの進行状況を表すアクションをディスパッチしたり、ステートに基づいて条件付きでアクションをディスパッチしたり、アクションをディスパッチした直後に更新後のステートをチェックしたり、といったシナリオが確かに可能になります。

一般的には、それらのアクションは関連しつつも独立したものなのか、あるいは実際には単一のアクションとして表現されるべきなのか、と考えるようにしてください。あなた自身の状況に照らして理にかなったことを行い、リデューサの可読性とアクションログの可読性のバランスをとるようにしてください。例えば、新しいステートツリー全体を含むような単一のアクションを作成すればリデューサは 1 行で書けてしまいますが、なぜその変更が起きたのかについてのヒストリがなくなってしまい、デバッギングは非常に難しくなります。一方で、粒度を保つためにループ内でたくさんアクションを出力しているような場合、別の方法で行う新しいアクションタイプを導入した方がいいかもしれないというサインです。

パフォーマンスが気になるところでは、同期的に連続して複数のディスパッチを行うことは避けるようにしてください。ディスパッチをバッチ化して行うようなアドオンやアプローチも複数あります。

#### 参考情報

**ドキュメント内**

- [FAQ: Performance - Reducing Update Events](./Performance.md#performance-update-events)

**記事**

- [Idiomatic Redux: Thoughts on Thunks, Sagas, Abstraction, and Reusability](http://blog.isquaredsoftware.com/2017/01/idiomatic-redux-thoughts-on-thunks-sagas-abstraction-and-reusability/#multiple-dispatching)

**ディスカッション**

- [#597: Valid to dispatch multiple actions from an event handler?](https://github.com/reactjs/redux/issues/597)
- [#959: Multiple actions one dispatch?](https://github.com/reactjs/redux/issues/959)
- [Stack Overflow: Should I use one or several action types to represent this async action?](http://stackoverflow.com/questions/33637740/should-i-use-one-or-several-action-types-to-represent-this-async-action/33816695)
- [Stack Overflow: Do events and actions have a 1:1 relationship in Redux?](http://stackoverflow.com/questions/35406707/do-events-and-actions-have-a-11-relationship-in-redux/35410524)
- [Stack Overflow: Should actions be handled by reducers to related actions or generated by action creators themselves?](http://stackoverflow.com/questions/33220776/should-actions-like-showing-hiding-loading-screens-be-handled-by-reducers-to-rel/33226443#33226443)
- [Twitter: "Good thread on the problems with Redux Thunk..."](https://twitter.com/dan_abramov/status/800310164792414208)
