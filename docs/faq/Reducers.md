---
id: reducers
title: リデューサ
hide_title: true
---

# Redux FAQ: リデューサ

<!--
## Table of Contents

- [Redux FAQ: Reducers](#redux-faq-reducers)
  - [Table of Contents](#table-of-contents)
  - [Reducers](#reducers)
    - [How do I share state between two reducers? Do I have to use `combineReducers`?](#how-do-i-share-state-between-two-reducers-do-i-have-to-use-combinereducers)
      - [Further information](#further-information)
    - [Do I have to use the `switch` statement to handle actions?](#do-i-have-to-use-the-switch-statement-to-handle-actions)
      - [Further information](#further-information-1)
-->

## リデューサ

### 2 つのリデューサ間でステートを共有する方法は？ `combineReducers` は使う必要がありますか？

Redux ストアの構造として推奨されているのは、ステートオブジェクトをキーごとに複数の「スライス」または「ドメイン」に分割し、個々のデータスライスを管理するため個別にリデューサ関数を与えるというものです。これは標準の Flux パターンが複数の独立したストアを持つのと似ており、Redux はこのパターンをより簡単にするために [`combineReducers`](../api/combineReducers.md) ユーティリティ関数を提供しています。しかし、`combineReducers` は必須*ではない*ことに注意することが重要です。これは「ステートスライスごとに単一のリデューサ関数があってデータにはプレーンな JavaScript オブジェクトを使用する」という一般的な使用例のためのユーティリティ関数に過ぎません。

2 つのリデューサ間でデータを共有したいのに `combineReducers` ではそれができない、と多くのユーザが後になって気付きます。そんな時に使用できるアプローチはいくつかあります：

- リデューサが別のステートスライスからのデータを知る必要がある場合、ステートツリー形を再編成して、1 つのリデューサがより多くのデータを扱うようにする必要があるかもしれません。
- これらのアクションの一部を処理するためにカスタム関数を書く必要があるかもしれません。そのためには、`combineReducers` を独自のトップレベルのレデューサ関数で置き換える必要があるかもしれません。また、[reduce-reducers](https://github.com/acdlite/reduce-reducers) のようなユーティリティを使用すれば `combineReducers` を実行してほとんどのアクションを処理させつつ、ステートスライスをまたがる特定のアクションのために、より特化したリデューサも実行するようにできます。
- - [redux-thunk](https://github.com/reduxjs/redux-thunk) のような[非同期ロジックを持つミドルウェア](../tutorials/fundamentals/part-4-store.md#middleware)は、`getState()` を通じて全体の状態にアクセスできます。アクションクリエータはステートから追加のデータを取得してアクションに入れることができ、そうすることで各リデューサが自分のステートスライスを更新するのに十分な情報を持つようにできます。

一般論として、リデューサは単なる関数であるということを覚えておいてください。どのように構成するのも分割するのもあなたの自由ですが、小さく再利用可能な関数に分割すること（「リデューサコンポジション」）が推奨されています。その際に、子リデューサが次のステートを計算するのに追加のデータが必要なのであれば、親リデューサからカスタムの 3 番目の引数を与えることができます。最終的な組み合わせが `(state, action) => newState` となり、ステートを書き換えずイミュターブルに更新するといった基本的なリデューサのルールに従ってさえいれば良いのです。

#### 参考情報

**ドキュメント内**

- [API: combineReducers](../api/combineReducers.md)
- [Recipes: Structuring Reducers](../recipes/structuring-reducers/StructuringReducers.md)

**ディスカッション**

- [#601: A concern on combineReducers, when an action is related to multiple reducers](https://github.com/reduxjs/redux/issues/601)
- [#1400: Is passing top-level state object to branch reducer an anti-pattern?](https://github.com/reduxjs/redux/issues/1400)
- [Stack Overflow: Accessing other parts of the state when using combined reducers?](http://stackoverflow.com/questions/34333979/accessing-other-parts-of-the-state-when-using-combined-reducers)
- [Stack Overflow: Reducing an entire subtree with redux combineReducers](http://stackoverflow.com/questions/34427851/reducing-an-entire-subtree-with-redux-combinereducers)
- [Sharing State Between Redux Reducers](https://invalidpatent.wordpress.com/2016/02/18/sharing-state-between-redux-reducers/)

### アクションをハンドルする際に `switch` 文を使う必要はありますか？

いいえ。リデューサー内でアクションに応答するためにどのようなアプローチを使っても全く構いません。`switch` 文は最も一般的なアプローチですが、`if` 文や関数ルックアップテーブルを使用しても構いませんし、これを抽象化した関数を作成しても構いません。実際、Redux はアクションオブジェクトに `type` フィールドを含めることを要求していますが、リデューサのロジックはアクションを処理するために `type` フィールドに依存する必要すらありません。とはいえ、標準的アプローチは、間違いなく `type` に基づいて `switch` 文やルックアップテーブルを使用することです。

#### 参考情報

**ドキュメント内**

- [Recipes: Reducing Boilerplate](../recipes/ReducingBoilerplate.md)
- [Recipes: Structuring Reducers - Splitting Reducer Logic](../recipes/structuring-reducers/SplittingReducerLogic.md)

**ディスカッション**

- [#883: take away the huge switch block](https://github.com/reduxjs/redux/issues/883)
- [#1167: Reducer without switch](https://github.com/reduxjs/redux/issues/1167)
