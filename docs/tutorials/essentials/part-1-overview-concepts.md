---
id: part-1-overview-concepts
title: '必修チュートリアル 1：Redux の概要とコンセプト'
sidebar_label: 'Redux の概要とコンセプト'
hide_title: true
description: 'The official Essentials tutorial for Redux: learn how to use Redux, the right way'
---

import { DetailedExplanation } from '../../components/DetailedExplanation'

# 必修チュートリアル 1：Redux の概要とコンセプト

:::tip この章で学ぶこと

- Redux とは何で、なぜ使いたくなるものなのか
- 鍵となる Redux の用語とコンセプト
- Redux アプリでのデータの流れ

:::

## はじめに

Redux Essentials （必修）チュートリアルにようこそ！ **このチュートリアルでは Redux を紹介し、我々の最新の推奨とベストプラクティスに基づいてどのように Redux を正しく使うのかについて説明します**。これを終える頃には、ここで学んだツールやパターンを用いて自分の Redux アプリケーションが作成できるようになっているでしょう。

このチュートリアルのパート 1 では、Redux を使うために知っておくべき重要な概念と用語を取り上げ、[パート 2：Redux アプリの構造](./part-2-app-structure.md)では、基本的な React + Redux アプリを見ながら、各パーツがどのように組み合わされているかを見ていきます。

[Part 3: Basic Redux Data Flow](./part-3-data-flow.md)からは、その知識を使って、実際の機能を備えた小さなソーシャルメディアフィードアプリを構築し、実際にどのように動作するかを確認し、Redux を使用する際の重要なパターンとガイドラインについて説明します。

### このチュートリアルの読み進め方

このページでは、Redux の正しい*使い方*にフォーカスし、Redux アプリを正しく構築する方法を理解できるのに十分なだけの概念を説明します。

説明は初心者向けにしていますが、あなたが既に知っている知識について多少の前提を置く必要はあります。

:::important 前提となる知識

- [HTML と CSS](https://internetingishard.com/) に関する多少の知識
- [ES6 構文と機能](https://www.taniarascia.com/es6-syntax-and-feature-overview/) に関する多少の知識
- React 用語に関する知識：[JSX](https://reactjs.org/docs/introducing-jsx.html)、[state](https://reactjs.org/docs/state-and-lifecycle.html)、[関数コンポーネントと props](https://reactjs.org/docs/components-and-props.html)、[フック](https://reactjs.org/docs/hooks-intro.html)
- [JavaScript の非同期処理](https://javascript.info/promise-basics)に関する知識と [AJAX リクエスト作成](https://javascript.info/fetch)の方法

:::

**もしこれらのトピックにまだ慣れていないのであれば、まず少々時間をかけてこれらに慣れてから Redux について学びに戻ってきてください**。準備ができるまでここでお待ちしています！

ブラウザに React DevTools と Redux DevTools 拡張機能がインストールされていることを確認してください。

- React DevTools 拡張機能：
  - [React DevTools Extension for Chrome](https://chrome.google.com/webstore/detail/react-developer-tools/fmkadmapgofadopljbjfkapdkoienihi?hl=en)
  - [React DevTools Extension for Firefox](https://addons.mozilla.org/en-US/firefox/addon/react-devtools/)
- Redux DevTools 拡張機能：
  - [Redux DevTools Extension for Chrome](https://chrome.google.com/webstore/detail/redux-devtools/lmhkpmbekcpmknklioeibfkpmmfibljd?hl=en)
  - [Redux DevTools Extension for Firefox](https://addons.mozilla.org/en-US/firefox/addon/reduxdevtools/)

## Redux とは？

そもそもこの "Redux" とやらが何なのかを理解するところから始めましょう。何をするもので、どんな問題を解決してくれ、なぜ使いたくなるのでしょうか。

**Redux は、「アクション」と呼ばれるイベントを使用して、アプリケーションのステートを管理・更新するためのパターンであり、ライブラリです。**アプリケーション全体で使用する必要がある状態を保持するための一元化されたストアとして機能し、状態が予測可能な方法でのみ更新されることを保証するルールを備えています。

### Redux を学ぶべき理由

Redux は「グローバル」なステート、すなわちアプリの様々な場所から必要とされるステートの管理をする場合に役立ちます。

**Redux が提供するパターンとツールを使うことで、いつ、どこで、なぜ、どのようにしてアプリケーションの状態が更新されるのか、また、それらの変更が発生したときにアプリケーションロジックがどのように振る舞うのかを容易に理解することができるようになります。**Redux が、予測可能でテスト可能なコードを書くようガイドしてくれるため、アプリケーションが期待通りに動作するという確信が得られます。

### Redux を学ぶべきタイミング

Redux は共有されるステートを管理するのに役立ちますが、他のツールと同様、トレードオフも存在します。学ぶべき概念は増えますし、書くべきコードは増えます。また、コードがいくらか間接的なものになり、特定の制限に従うことが求められます。短期的な生産性と長期的な生産性のトレードオフなのです。

Redux は以下のような場合に有用です：

- アプリ内の多くの場所で必要とされるステートが大量に存在する
- アプリの状態は時間とともに頻繁に更新される
- その状態を更新するロジックが複雑になりうる
- アプリのコードベースが中規模～大規模であり、多くの人が作業する可能性がある

**すべてのアプリに Redux が必要なわけではありません。あなたが作っているアプリがどんな種類なのかを時間をかけて考え、取り組んでいる問題を解決するためにどのツールが最適なのか決めるようにしてください。**

:::info もっと知りたい場合

あなたのアプリに Redux を使って良いのか不明な場合は、以下のリソースに更なるガイダンスがあります。

- **[When (and when not) to reach for Redux](https://changelog.com/posts/when-and-when-not-to-reach-for-redux)**
- **[The Tao of Redux, Part 1 - Implementation and Intent](http://blog.isquaredsoftware.com/2017/05/idiomatic-redux-tao-of-redux-part-1/)**
- **[Redux FAQ: When should I use Redux?](../../faq/General.md#when-should-i-use-redux)**
- **[You Might Not Need Redux](https://medium.com/@dan_abramov/you-might-not-need-redux-be46360cf367)**

:::

### Redux ライブラリとツール

Redux 自体は単体の小さな JS ライブラリです。しかし他のパッケージとよく組み合わせて使用されます。

#### React-Redux

Redux はあらゆる UI フレームワークと統合できますが、React で最も使用されています。公式パッケージである [**React-Redux**](https://react-redux.js.org/) は、React コンポーネントが Redux ストアとやりとりし、ストアからステートを部分的に読み取ったりストアを更新するためのアクションをディスパッチしたりできるようにします。

#### Redux Toolkit

[**Redux Toolkit**](https://redux-toolkit.js.org) は、Redux ロジックを書くために我々が推奨するアプローチです。Redux Toolkit には、Redux アプリを構築するのに我々が必須だと考えているパッケージや関数が含まれています。Redux Toolkit を使うことで、推奨されるベストプラクティスに準拠し、多くの Redux のタスクを簡略化し、よくあるミスを防ぎ、Redux アプリをより簡単に書けるようになります。

#### Redux DevTools 拡張機能

[**Redux DevTools 拡張機能**](https://github.com/zalmoxisus/redux-devtools-extension) は Redux のストアに生じたステート変化の履歴を表示します。これにより効率的にアプリケーションをデバッグできるようになり、これには「タイムトラベルデバッギング」と呼ばれるパワフルなテクニックも含まれています。

## Redux 用語とコンセプト

実際のコードに取りかかる前に、Redux を使う際に知る必要がある用語や概念について少しお話しします。

### ステート管理

この小さな React カウンタコンポーネントを眺めるところから始めましょう。コンポーネントの state として数字を保持しており、ボタンがクリックされたらそれをインクリメントします：

```jsx
function Counter() {
  // State: a counter value
  const [counter, setCounter] = useState(0)

  // Action: code that causes an update to the state when something happens
  const increment = () => {
    setCounter(prevCounter => prevCounter + 1)
  }

  // View: the UI definition
  return (
    <div>
      Value: {counter} <button onClick={increment}>Increment</button>
    </div>
  )
}
```

これは自己完結したアプリであり、以下のような要素を含んでいます：

- 我々のアプリを動かす信頼できる情報源 (souce of truth) となる**ステート**
- 現在のステートに対応する UI を宣言的に記述する**ビュー**
- ユーザ入力に応じてアプリ内で発生しステートの更新をトリガするイベントである**アクション**

これは、**「単一方向データフロー」**のシンプルな例となっています：

- ステートはある特定のタイミングにおけるアプリの状態を記述する
- UI はそのステートに応じてレンダーされる
- 何か（ユーザのクリックなど）が起こったときに、起こったことに応じてステートが更新される
- UI はその新しいステートに応じて再レンダーされる

![One-way data flow](/img/tutorials/essentials/one-way-data-flow.png)

しかし、**同じステートを複数のコンポーネントが共有する**必要が出てきて、特にそれらのコンポーネントがアプリの様々な場所に置かれるようになってくると、このシンプルさは失われてしまいます。これは親コンポーネントに[「ステートをリフトアップ」](https://reactjs.org/docs/lifting-state-up.html)することで解決できることもありますが、これがいつもうまく行く訳ではありません。

これを解決する 1 つの方法は、共有されるステートをコンポーネントから抽出して、コンポーネントツリーの外側のどこか一元的な場所に置くことです。これによりコンポーネントツリーは 1 個の大きな「ビュー」になり、どのコンポーネントも、ツリーのどこにいるかに関係なく、状態にアクセスしたり、アクションをトリガしたりできるようになります！

状態管理に関わる概念を定義して分離し、ビューとステートの間の独立性を維持するルールを適用することで、より構造化されたコードとなり、保守性が向上します。

これが Redux の基本的な考え方です。Redux とはアプリケーションのグローバルなステートを一元的に格納するための場所のことであり、コードを予測可能にするためにステート更新時に従うべき特定のパターンのことです。

### イミュータビリティ (immutability)

「ミュータブル (mutable)」とは「書き換わる可能性がある」という意味です。何かが「イミュータブル (immutable)」であるとは、それが書き換わる可能性がないという意味です。

JavaScript のオブジェクトや配列はデフォルトですべてミュータブルです。オブジェクトを作成したら、そのフィールドの内容を変更することができますね。同様に配列を作成したら、その内容を変更することができます：

```js
const obj = { a: 1, b: 2 }
// still the same object outside, but the contents have changed
obj.b = 3

const arr = ['a', 'b']
// In the same way, we can change the contents of this array
arr.push('c')
arr[1] = 'd'
```

これをオブジェクトや配列の*ミューテート* (mutate; 書き換え) と呼びます。メモリ上で参照しているのは同じオブジェクトないし配列ですが、その中身が変わっています。

**イミュータブルに値を更新する場合は、あなたのコードで既存のオブジェクト／配列の*コピー*を作成し、そのコピーを変更するようにします**。

これを手でやる場合は、JavaScript の配列・オブジェクトスプレッド構文を使うこともできますし、元の配列を書き換えずに新しい配列のコピーを返す配列のメソッドを使うこともできます。

```js
const obj = {
  a: {
    // To safely update obj.a.c, we have to copy each piece
    c: 3
  },
  b: 2
}

const obj2 = {
  // copy obj
  ...obj,
  // overwrite a
  a: {
    // copy obj.a
    ...obj.a,
    // overwrite c
    c: 42
  }
}

const arr = ['a', 'b']
// Create a new copy of arr, with "c" appended to the end
const arr2 = arr.concat('c')

// or, we can make a copy of the original array:
const arr3 = arr.slice()
// and mutate the copy:
arr3.push('c')
```

**Redux は、すべてのステート更新がイミュータブルに行われることを期待します**。これがいつどのように重要なのかと、イミュータブルな更新ロジックをもっと簡単に記述する方法については、少し後で説明します。

:::info もっと知りたい場合

JavaScript でイミュータビリティがどのように働くのかについては、以下を参照してください：

- [A Visual Guide to References in JavaScript](https://daveceddia.com/javascript-references/)
- [Immutability in React and Redux: The Complete Guide](https://daveceddia.com/react-redux-immutability-guide/)

:::

### 用語

先に進む前に慣れ親しんでおいて欲しい、重要な Redux 用語がいくつかあります。

#### アクション (action)

**アクション** とは `type` フィールドを持つ JavaScript オブジェクトです。**アクションとは、アプリで何が起きたのかを記述するイベントであると考えることができます**。

`type` フィールドは、`"todos/todoAdded"` のような、このアクションの説明となる文字列であるべきです。この type 文字列は、アクションが属する機能ないしカテゴリを表す domain と、起こったことの内容を表す eventName を使って、`"domain/eventName"` のような形式で書きます。

アクションオブジェクトは、何が起こったのかを表す追加の情報を保持するフィールドを持つことができます。規約として、この情報を `payload` というフィールドに入れることにします。

典型的なアクションオブジェクトは以下のような見た目になります：

```js
const addTodoAction = {
  type: 'todos/todoAdded',
  payload: 'Buy milk'
}
```

#### アクションクリエータ (action creator)

**アクションクリエータ**とは、アクションオブジェクトを作成してそれを返す関数のことです。アクションオブジェクトを毎回手書きしないで済むように、典型的にはこのようなものを使います。

```js
const addTodo = text => {
  return {
    type: 'todos/todoAdded',
    payload: text
  }
}
```

#### リデューサ (reducer)

リデューサは、現在の `state` と `action` オブジェクトを受け取って、必要に応じてステートをどう更新するかを決め、新しいステートを返すような関数（`(state, action) => newState`）です。**リデューサは、受け取ったアクション（イベント）の type に応じて処理を行う、イベントリスナの一種であると考えることができます**。

:::info

これが "reducer" 関数という名前になっているのは、[`Array.reduce()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/reduce) に渡す類のコールバック関数と類似しているからです。

:::

リデューサは*常に*特定のルールに従う必要があります：

- `state` と `action` 引数に基づいて新しいステートを計算する以外のことをしてはいけない。
- 既存の `state` を書き換えてはいけない。代わりに既存の `state` をコピーし、コピーに変更を加えるという、*イミュータブルな更新*を行わなければならない。
- 非同期なロジックを実行したり、ランダムな値の計算を行ったり、その他「副作用 (side effect)」を引き起こしてはいけない。

これが重要である理由や、これを守るための方法など、リデューサのルールの詳細については後で説明します。

リデューサ関数内のロジックは、典型的には以下のようなステップとなっています。

- リデューサが当該アクションに対応する必要があるかチェックする
  - 対応する必要がある場合、ステートをコピーして、そのコピーを新しい値で書き換えて、それを返す
- そうでない場合、既存のステートを変更せずに返す

以下の小さなリデューサの例で、リデューサが守るべきステップを示しています。

```js
const initialState = { value: 0 }

function counterReducer(state = initialState, action) {
  // Check to see if the reducer cares about this action
  if (action.type === 'counter/increment') {
    // If so, make a copy of `state`
    return {
      ...state,
      // and update the copy with the new value
      value: state.value + 1
    }
  }
  // otherwise return the existing state unchanged
  return state
}
```

リデューサの内部では新しいステートの中身を決めるのに `if/else`、`switch`、ループなどの好きなロジックを使うことができます。

<DetailedExplanation title="詳細説明：なぜリデューサという名前なのか?" >

[`Array.reduce()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/reduce) メソッドは値の配列を受け取り、その配列内の要素を 1 つずつ処理して、単一の最終結果を返します。これは「配列を 1 つの値へと短くする (reduce)」処理だと考えることができます。

`Array.reduce()` は引数としてコールバック関数を受け取り、それは配列内の各要素に対して 1 度ずつ呼び出されます。コールバック関数は 2 つの引数を受け取ります。

- `previousResult`: 前回コールバックが返した値
- `currentItem`: 現在の配列内の要素

コールバックが最初に呼ばれる際には `previousResult` はまだありませんので、初回の `previousResult` として使うための初期値も渡す必要があります。

例えば数値の配列の中身を足し合わせて合計を知りたい場合は、reduce callback として以下のようなものを書きます：

```js
const numbers = [2, 5, 8]

const addNumbers = (previousResult, currentItem) => {
  console.log({ previousResult, currentItem })
  return previousResult + currentItem
}

const initialValue = 0

const total = numbers.reduce(addNumbers, initialValue)
// {previousResult: 0, currentItem: 2}
// {previousResult: 2, currentItem: 5}
// {previousResult: 7, currentItem: 8}

console.log(total)
// 15
```

ここで `addNumber` という "reduce callback" 関数は、それ自体が何かを覚えておく必要がないということに注意してください。`previousResult` と `currentItems` という引数を受けとり、これらを使って何か処理をして、新しい値を返しているだけです。

**Redux のリデューサ関数もこの "reduce callback" 関数と全く同じ発想によるものです！** 「前回のステート」(`state`) と 「現在の要素」(`action`) とを受け取って、これらに基づいて新しいステートを決めて、その新しいステートを返します。

もし Redux のアクションが配列になっていれば、`reduce()` メソッドにリデューサ関数を入れて呼び出せば、同じようにして最終結果のステートが手に入るでしょう：

```js
const actions = [
  { type: 'counter/increment' },
  { type: 'counter/increment' },
  { type: 'counter/increment' }
]

const initialState = { value: 0 }

const finalResult = actions.reduce(counterReducer, initialState)
console.log(finalResult)
// {value: 3}
```

つまり **Redux のリデューサは（時間とともに発生する）アクションの組み合わせを単一のステートに reduce するものだ**ということです。違いは、`Array.reduce()` ではそれが一度に起きる一方で、Redux では実行中のアプリのライフタイムに渡ってそれが起きるということです。

</DetailedExplanation>

#### ストア (store)

Redux アプリケーションの現在のステートは、ストアと呼ばれるオブジェクトに入っています。

ストアはリデューサを渡して作られるものでああり、現在のステートの値を返す `getState` というメソッドを持っています。

```js
import { configureStore } from '@reduxjs/toolkit'

const store = configureStore({ reducer: counterReducer })

console.log(store.getState())
// {value: 0}
```

#### ディスパッチ (dispatch)

Redux ストアには `dispatch` というメソッドがあります。**ステートを更新する唯一の方法は、`store.dispatch()` を呼び出してアクションオブジェクトを渡すことです**。ストアがリデューサ関数を呼び出し、内部に新しいステートを保存し、`getState()` でその更新後の値を取得できるようにします。

```js
store.dispatch({ type: 'counter/increment' })

console.log(store.getState())
// {value: 1}
```

アプリケーションにおける**アクションのディスパッチとは「イベントをトリガする」ようなものだと考えて構いません**。何かが起こり、ストアにそのことを伝えたいと思ったとします。リデューサはイベントリスナのように働くので、リデューサが自分の興味のあるアクションをリッスンしたら、それに対応してステートを更新します。

典型的には正しいアクションをディスパッチできるよう、アクションクリエータを呼び出します：

```js
const increment = () => {
  return {
    type: 'counter/increment'
  }
}

store.dispatch(increment())

console.log(store.getState())
// {value: 2}
```

#### セレクタ (selector)

セレクタとは、ストア内のステート値から情報の特定の一部を取り出すための関数です。アプリケーションが大きくなるにつれ、アプリの様々な場所から同じ値を取り出す際に起こるロジックの重複を回避できるようになります。

```js
const selectCounterValue = state => state.value

const currentValue = selectCounterValue(store.getState())
console.log(currentValue)
// 2
```

### Redux アプリにおけるデータの流れ

先ほど、以下のようなステップでアプリの更新を行う「単一方向データフロー (one-way date flow)」についてお話ししました。

- ステートはある特定のタイミングにおけるアプリの状態を記述する
- UI はそのステートに応じてレンダーされる
- 何か（ユーザのクリックなど）が起こったときに、起こったことに応じてステートが更新される
- UI はその新しいステートに応じて再レンダーされる

Redux 特有の話も含めてより詳細に書くとこうなります。

- 初回のセットアップ：
  - ルートリデューサ関数を使って Redux のストアが作成される
  - ストアはルートリデューサを呼び出し、その返り値を初期 `state` として保存する
  - UI が最初にレンダーされる時、UI コンポーネントは Redux ストア内の現在のステートにアクセスし、そのデータを使って何をレンダーするか決定する。また将来のストアの更新を購読 (subscribe) してステートが更新された時に気づくようにする。
- 更新：
  - ユーザがボタンをクリックするなど、アプリで何かが起こる
  - アプリコードが Redux ストアに向けて `dispatch({type: 'counter/increment'})` のようにしてアクションをディスパッチする
  - ストアは以前の `state` と現在の `action` でリデューサを再び呼び出し、返り値を新しい `state` として保存する
  - ストアはそれを購読しているすべての UI 部品に対して、ストアに更新があったことを通知する
  - ストアからのデータを必要とするそれぞれの UI コンポーネントが、ステートのうち自分が必要としている部分に変更があったかどうかを確認する
  - 自分の関わるデータに変更があったコンポーネントは新しいデータを使って強制的に再レンダーされ、画面に表示されるものが更新される

視覚的にはデータフローはこのようなものになります：

![Redux data flow diagram](/img/tutorials/essentials/ReduxDataFlowDiagram.gif)

## 学んだこと

Redux には覚えるべき用語とコンセプトがそこそこの量あります。これまで見てきたことをおさらいしましょう：

:::tip まとめ

- **Redux はアプリケーションのステートを管理するライブラリである**
  - Redux は典型的には、Redux と React を統合するためのライブラリである React-Redux とともに使われる
  - Redux ロジックを記述するのに Redux Toolkit を使うことが推奨される
- **Redux アプリは単一方向データフローという構造をとる**
  - ステートはある一時点におけるアプリの状態を記述し、UI はそのステートに応じてレンダーされる
  - アプリ内で何かが起こった場合：
    - UI がアクションをディスパッチする
    - ストアはリデューサを実行し、起こったことに基づいてステートが更新される
    - ストアが UI にステートが変わったことを通知する
  - 新しいステートで UI が再レンダーされる
- **Redux では幾つかの種類のコードが使われる**
  - _アクション_ とは `type` フィールドを有するプレーンオブジェクトであり、アプリで「何が起きたのか」を記述する
  - _リデューサ_ とは一つ前のステートとアクションとの組み合わせから新しいステートを計算する関数である
  - Redux の *ストア*　が、アクションが*ディスパッチ*される度にルートリデューサを実行する

:::

## 次は？

Redux アプリにおける各々の部品について見てきました。次は[パート 2：アプリの構成](./part-2-app-structure.md)に進み、完全に動作するサンプルを通じて、これらの部品がどのように協調して働くのかを見ていきましょう。
