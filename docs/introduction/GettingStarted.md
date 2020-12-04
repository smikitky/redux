---
id: getting-started
title: Redux を始める
description: 'Introduction > Getting Started: Resources to get started learning and using Redux'
hide_title: true
---

# Redux を始める

Redux は JavaScript アプリのための予測可能 (predictable) なステートコンテナです。

一貫して動作するアプリを書き、それを様々な環境（クライアント・サーバ・ネイティブ）で実行し、簡単にテストできるようになります。さらには、[ライブコードエディットやタイムトラベルデバッグ](https://github.com/reduxjs/redux-devtools)といった素晴らしい開発者体験をもたらしてくれます。

Redux は [React](https://reactjs.org) やその他任意のビューライブラリと組み合わせて使うことができます。Redux は非常に小さい（依存を含んでも 2kB）ですが、アドオンによる大きなエコシステムが利用可能です。

## インストール

### Redux Toolkit

[**Redux Toolkit**](https://redux-toolkit.js.org) (RTK) は、Redux のロジックを書くにあたって我々が公式に推奨するアプローチです。これは Redux のコアをラップしたものであり、Redux アプリを構築するのに我々が必須だと考えているパッケージや関数が含まれています。Redux Toolkit を使うことで、推奨されるベストプラクティスに準拠し、多くの Redux のタスクを簡略化し、よくあるミスを防ぎ、Redux アプリをより簡単に書けるようになります。

RTK には、[ストアのセットアップ](https://redux-toolkit.js.org/api/configureStore)、
[更新ロジックを記述するためのリデューサの作成](https://redux-toolkit.js.org/api/createreducer)、
更には[ステート「スライス」まるごとの作成](https://redux-toolkit.js.org/api/createslice)といった、多くのよくあるユースケースを簡略化するためのユーティリティが含まれています。

Redux で初めてのプロジェクトをセットアップしようとしている新しいユーザの方も、既存のアプリをシンプルにしたいと考えている経験者も、**[Redux Toolkit](https://redux-toolkit.js.org/)** を使うことでコードをよりよくできるでしょう。

Redux Toolkit は NPM パッケージとして公開されており、モジュールバンドラと組み合わせて、あるいは Node アプリケーションで使用できます。

```bash
# NPM
npm install @reduxjs/toolkit

# Yarn
yarn add @reduxjs/toolkit
```

### Create React App で使う

React と Redux で新しいアプリを始めたい場合には [Create React App](https://github.com/facebook/create-react-app) の[公式 Redux+JS テンプレート](https://github.com/reduxjs/cra-template-redux)を使うことをお勧めします。**[Redux Toolkit](https://redux-toolkit.js.org/)** と React Redux を使った React コンポーネントとのインテグレーションが含まれています。

```sh
npx create-react-app my-app --template redux
```

### Redux Core

Redux コアライブラリは NPM パッケージとして公開されており、モジュールバンドラと組み合わせて、あるいは Node アプリケーションで使用できます。

```bash
# NPM
npm install redux

# Yarn
yarn add redux
```

グローバル変数として `window.Redux` を定義する、コンパイル済みの UMD パッケージも提供されています。UMD パッケージは [`<script>` タグ](https://unpkg.com/redux/dist/redux.js) で直接使えます。

詳しくは[インストール](Installation.md)のページを参照してください。

## 基本の例

アプリのグローバルステートはストア (_store_) と呼ばれる単一のオブジェクトツリー内に保持されます。
このステートツリーを更新する唯一の方法は、何が起きたのかの記述であるアクション (_action_) を作成して、ストアに向けてディスパッチ (_dispatch_) することです。
アクションに対応してステートがどう更新されるのかを定義するために、古いステートとアクションに基づいて新しいステートを計算する純関数であるリデューサ (_reducer_) 関数を書きます。

```js
import { createStore } from 'redux'

/**
 * This is a reducer - a function that takes a current state value and an
 * action object describing "what happened", and returns a new state value.
 * A reducer's function signature is: (state, action) => newState
 *
 * The Redux state should contain only plain JS objects, arrays, and primitives.
 * The root state value is usually an object.  It's important that you should
 * not mutate the state object, but return a new object if the state changes.
 *
 * You can use any conditional logic you want in a reducer. In this example,
 * we use a switch statement, but it's not required.
 */
function counterReducer(state = { value: 0 }, action) {
  switch (action.type) {
    case 'counter/incremented':
      return { value: state.value + 1 }
    case 'counter/decremented':
      return { value: state.value - 1 }
    default:
      return state
  }
}

// Create a Redux store holding the state of your app.
// Its API is { subscribe, dispatch, getState }.
let store = createStore(counterReducer)

// You can use subscribe() to update the UI in response to state changes.
// Normally you'd use a view binding library (e.g. React Redux) rather than subscribe() directly.
// There may be additional use cases where it's helpful to subscribe as well.

store.subscribe(() => console.log(store.getState()))

// The only way to mutate the internal state is to dispatch an action.
// The actions can be serialized, logged or stored and later replayed.
store.dispatch({ type: 'counter/incremented' })
// {value: 1}
store.dispatch({ type: 'counter/incremented' })
// {value: 2}
store.dispatch({ type: 'counter/decremented' })
// {value: 1}
```

直接ステートを書き換えるのはなく、代わりに起こしたい書き換えの内容を _action_ と呼ばれるプレーンオブジェクトで記述します。そして _reducer_ と呼ばれる関数を書いて、個々のアクションがどのようにアプリのステートを変換するのかを記述します。

典型的な Redux アプリでは、ストアとそれに対応するルートとなるリデューサ関数が 1 組だけ存在します。アプリが大きくなるにつれて、ルートのリデューサを、ステートの別々の部分に作用する独立した小さなリデューサに分割していきます。これは、React アプリにはルートコンポーネントが 1 つだけあって、それが多くの小さなコンポーネントで出来ている、ということと全く同様です。

このアーキテクチャはカウンタのようなアプリでは大袈裟に見えるかもしれませんが、このパターンの美しいところは、大きく複雑なアプリでもよくスケールするということです。また、すべてのステートの書き換えを、それを起こしたアクションにまで追跡することができるため、非常にパワフルな開発者向けツールが実現できます。ユーザの操作を記録して再現することも、アクションをリプレイするだけで行えるようになります。

### Redux Toolkit の例

Redux Tookit は Redux ロジックを記述してストアをセットアップするプロセスを簡略化します。Redux Toolkit を使うと、同じロジックは以下のように書けます。

```js
import { createSlice, configureStore } from '@reduxjs/toolkit'

const counterSlice = createSlice({
  name: 'counter',
  initialState: {
    value: 0
  },
  reducers: {
    incremented: state => {
      // Redux Toolkit allows us to write "mutating" logic in reducers. It
      // doesn't actually mutate the state because it uses the Immer library,
      // which detects changes to a "draft state" and produces a brand new
      // immutable state based off those changes
      state.value += 1
    },
    decremented: state => {
      state.value -= 1
    }
  }
})

export const { incremented, decremented } = counterSlice.actions

const store = configureStore({
  reducer: counterSlice.reducer
})

// Can still subscribe to the store
store.subscribe(() => console.log(store.getState()))

// Still pass action objects to `dispatch`, but they're created for us
store.dispatch(incremented())
// {value: 1}
store.dispatch(incremented())
// {value: 2}
store.dispatch(decremented())
// {value: 1}
```

Redux Toolkit により、Redux の振る舞いとデータフローを保ったままで読みやすく短いロジックを書けるようになります。

## Redux を学ぶ

Redux を学ぶための様々なリソースがあります。

### Redux Essentials チュートリアル

[**Redux Essentials （必修）チュートリアル**](../tutorials/essentials/part-1-overview-concepts.md) は「トップダウン」式のチュートリアルであり、推奨される最新の API とベストプラクティスを用いて「正しいやり方で Redux を使う」ための方法が学べます。ここから始めるのがお勧めです。

### Redux Fundamentals チュートリアル

The [**Redux Fundamentals （原理）チュートリアル**](../tutorials/fundamentals/part-1-overview.md) は「ボトムアップ」型のチュートリアルであり、何も抽象化せず最初の原理から「React がどうやって動くのか」を、そして標準的な React の使用パターンがなぜそうなっているのかを説明しています。

### 追加のチュートリアル

- Redux リポジトリには Redux の様々な使い方をデモするための幾つかのサンプルプロジェクトが含まれています。ほぼ全ての例には対応する CodeSandbox のサンドボックスが付いています。これはオンラインで試せるインタラクティブなコードとなっています。**[サンプル](./Examples.md)**のページでサンプルの全リストが参照できます。
- Redux の作者である Dan Abramov による **無料の ["Getting Started with Redux" ビデオシリーズ](https://egghead.io/series/getting-started-with-redux)** や、Egghead.io のビデオコース **[Building React Applications with Idiomatic Redux](https://egghead.io/courses/building-react-applications-with-idiomatic-redux)**。
- Redux のメンテナである Mark Erikson による **[発表 "Redux Fundamentals"](http://blog.isquaredsoftware.com/2018/03/presentation-reactathon-redux-fundamentals/)**、および[**ワークショップスライド "Redux Fundamentals"**](https://blog.isquaredsoftware.com/2018/06/redux-fundamentals-workshop-slides/)
- Dave Ceddia による [**A Complete React Redux Tutorial for Beginners**](https://daveceddia.com/redux-tutorial/)

### その他のリソース

- **[Redux FAQ](../FAQ.md)** では Redux の使い方に関する多くの質問に対する回答が載っています。and the **["レシピ" セクション](../recipes/README.md)** には派生データの処理、テスト、リデューサの構成、ボイラープレート削減に関する情報が掲載されています。
- Redux メンテナである Mark Erikson による **[チュートリアルシリーズ "Practical Redux"](http://blog.isquaredsoftware.com/series/practical-redux/)** には、実世界で React と Redux を使う場合の中上級テクニックに関するデモがあります。（**[Educative.io のインタラクティブコース](https://www.educative.io/collection/5687753853370368/5707702298738688)**でも利用可能。）
- **[React/Redux リンク集](https://github.com/markerikson/react-redux-links)** には[リデューサとセレクタ](https://github.com/markerikson/react-redux-links/blob/master/redux-reducers-selectors.md), [副作用の管理](https://github.com/markerikson/react-redux-links/blob/master/redux-side-effects.md), [Redux アーキテクチャとベストプラクティス](https://github.com/markerikson/react-redux-links/blob/master/redux-architecture.md)などに関する記事がカテゴリ別に掲載されています。
- コミュニティによって何千もの Redux 関連ライブラリ、アドオン、ツールが作成されています。**[ドキュメントの "エコシステム" ページ](./Ecosystem.md)** に我々が推奨するもののリストがあり、**[Redux addons catalog](https://github.com/markerikson/redux-ecosystem-links)**には全リストもあります。

## ヘルプとディスカッション

**[Reactiflux Discord コミュニティ](http://www.reactiflux.com)** の **[#redux channel](https://discord.gg/reactiflux)** は Redux を学び、使用する際のあらゆる疑問に関する公式なリソースです。Reactflix はたびたび訪れ、質問し、学ぶのにとてもよい場所ですので、是非参加してください！

[Stack Overflow](https://stackoverflow.com) で **[#redux tag](https://stackoverflow.com/questions/tagged/redux)** を付けて質問することもできます。

バグレポートやその他のフィードバックを残したい場合は [Github リポジトリに issue を作成してください](https://github.com/reduxjs/redux)。

## Redux を使うべきですか？

Redux はステートを管理するための価値あるツールですが、あなたの置かれている状況で本当に適切なのかを検討するべきです。**誰かが Redux を使うべきだと言ったというだけの理由で Redux を使うのは止めましょう。時間をかけて、Redux の利点とトレードオフを理解してください。**

Redux を使うとよいと言えそうなのは、例えば以下のような場合でしょう。

- 経時的に変化するデータがかなりの量で存在する
- ステートに信頼できる唯一の情報源 (single source of truth) が欲しい
- トップレベルコンポーネントで state を全部管理するのが適切と思えなくなってきた

> **Redux をどんな場面で使うべきかについて、更なる情報は以下を参照してください。**
>
> - **[Redux FAQ: When should I use Redux?](../faq/General.md#when-should-i-use-redux)**
> - **[You Might Not Need Redux](https://medium.com/@dan_abramov/you-might-not-need-redux-be46360cf367)**
>
> - **[The Tao of Redux, Part 1 - Implementation and Intent](http://blog.isquaredsoftware.com/2017/05/idiomatic-redux-tao-of-redux-part-1/)**
>
> - **[The Tao of Redux, Part 2 - Practice and Philosophy](http://blog.isquaredsoftware.com/2017/05/idiomatic-redux-tao-of-redux-part-2/)**
> - **[Redux FAQ](../FAQ.md)**
