---
id: part-2-app-structure
title: '必修チュートリアル 2：Redux アプリの構造'
sidebar_label: 'Redux アプリの構造'
hide_title: true
description: 'The official Redux Essentials tutorial: learn the structure of a typical React + Redux app'
---

import { DetailedExplanation } from '../../components/DetailedExplanation'

# 必修チュートリアル 2：Redux アプリの構造

:::tip この章で学ぶこと

- 典型的な React + Redux アプリの構造
- Redux DevTools 拡張機能でステートの変化を見る方法

:::

## はじめに

[必修チュートリアル 1：Redux の概要とコンセプト](./part-1-overview-concepts.md)では、Redux が何故有用なのか、Redux コード内のあちこちで使われる用語やコンセプト、そして Redux アプリ内でデータがどのように流れるのかを見てきました。

では、実際に動作するサンプルを見ながら、これらの部品がどのように組み合わされるのか学んでいきましょう。

## カウンタによるサンプル

これから見ていくサンプルプロジェクトは、ボタンをクリックして数字を足したり引いたりできる小さなカウンタアプリです。あまりエキサイティングなものではないかもしれませんが、実際に動く React + Redux アプリケーションにおける重要な要素が入っています。

このプロジェクトは、[Create-React-App の公式 Redux テンプレート](https://github.com/reduxjs/cra-template-redux)を使って作成されています。標準的な Redux アプリケーションの構成が作成時点で設定されており、Redux ストアとロジックを作成するために [Redux Toolkit](https://redux-toolkit.js.org) を、Redux ストアと React コンポーネントを接続するために [React-Redux](https://react-redux.js.org) を使用しています。

以下がプロジェクトのライブバージョンです。右側のアプリプレビューでボタンをクリックして遊んだり、左側のソースファイルを参照したりすることができます。

<iframe
  class="codesandbox"
  src="https://codesandbox.io/embed/github/reduxjs/redux-essentials-counter-example/tree/master/?fontsize=14&hidenavigation=1&module=%2Fsrc%2Ffeatures%2Fcounter%2FcounterSlice.js&theme=dark"
  title="redux-essentials-example"
  allow="geolocation; microphone; camera; midi; vr; accelerometer; gyroscope; payment; ambient-light-sensor; encrypted-media; usb"
  sandbox="allow-modals allow-forms allow-popups allow-scripts allow-same-origin"
></iframe>

このプロジェクトを自分のコンピュータで試してみたい場合は、我々の Redux テンプレートを用いて[新しい Create-React-App プロジェクトを作成](https://create-react-app.dev/docs/getting-started#selecting-a-template)できます。

```
npx create-react-app redux-essentials-example --template redux
```

### カウンタアプリを使ってみる

このカウンタアプリは使いながら内部の様子を見られるよう、既に設定されています。

ブラウザの DevTools を開いてください。そして、DevTools の中の「Redux」タブを選択し、右上のツールバーの「State」ボタンをクリックします。以下のようなものが表示されるはずです。

![Redux DevTools: initial app state](/img/tutorials/essentials/devtools-initial.png)

右側を見ると、Redux ストアが以下のようなアプリのステートから始まっていることが分かります。

```js
{
  counter: {
    value: 0
  }
}
```

DevTools を使うとアプリを使いながらステートがどのように変化しているのかを見ることができます。

まずはこのツールで遊んでみて何ができるのかを見てみましょう。アプリの "+" ボタンをクリックした後に、Redux DevTools の "Diff" タブを見てください。

![Redux DevTools: first dispatched action](/img/tutorials/essentials/devtools-first-action.png)

2 つの重要なことが見て取れます。

- "+" ボタンをクリックすると、`"counter/increment"` というタイプのアクションがストアにディスパッチされた
- このアクションがディスパッチされると、`state.counter.value` フィールドは `0` から `1` に変化した

では以下を試してみてください：

- もう一度 "+" ボタンをクリックします。表示されている値が 2 になっているはずです。
- "-" ボタンを 1 回クリックします。表示されている値が 1 になるはずです。
- "Add Amount" ボタンをクリックします。表示されている値が 3 になります。
- テキストボックスの数字 "2" を "3" に変更します。
- "Add Async" ボタンをクリックします。プログレスバーがボタンに表示され、数秒後に表示される値が 6 に変わります。

Redux DevTools に戻ってください。ボタンをクリックするたびに、合計 5 つのアクションがディスパッチされているのがわかるはずです。ここで、左側のリストから最後の `"counter/incrementByAmount"` エントリを選択し、右側の "Action" タブをクリックします。

![Redux DevTools: done clicking buttons](/img/tutorials/essentials/devtools-done-clicking.png)

このアクションオブジェクトは以下のようなものであることが分かります：

```js
{
  type: 'counter/incrementByAmount',
  payload: 3
}
```

また、「Diff」タブをクリックすると、このアクションに反応して `state.counter.value` フィールドの値が `3` から `6` に変化していることがわかります。

アプリの内部で何が起こっているのか、ステートが時間の経過とともにどのように変化しているのかを確認できる機能は非常に強力なものです！

DevTools には、アプリのデバッグに役立つコマンドやオプションがまだいくつかあります。右上の「Trace」タブをクリックしてみてください。パネルに JavaScript 関数スタックトレースが表示され、アクションがストアに到達したときに実行されていた行を示すソースコードの断片が表示されます。特に、`<Counter>` コンポーネントからこのアクションをディスパッチしたコードの行がハイライトされているはずです。

![Redux DevTools: action stack traces](/img/tutorials/essentials/devtools-action-stacktrace.png)

これによりコードのどの部分が特定のアクションをディスパッチしたのかが分かりやすくなります。

## アプリケーションの中身

アプリが何をするのかが分かったところで、どのように動いているのか見てみましょう。

こちらが、このアプリケーションを構成している重要なファイルです：

- `/src`
  - `index.js`: アプリのスタートポイント
  - `App.js`: 最上位の React コンポーネント
  - `/app`
    - `store.js`: Redux ストアインスタンスを作成する
  - `/features`
    - `/counter`
      - `Counter.js`: カウンタ機能に関する UI を表示する React コンポーネント
      - `counterSlice.js`: カウンタ機能に関する Redux ロジック

Redux ストアがどのように作成されているのかから見ていきます。

### Redux ストアの作成

`app/store.js` を開いてください。中はこのようになっています：

```js title="app/store.js"
import { configureStore } from '@reduxjs/toolkit'
import counterReducer from '../features/counter/counterSlice'

export default configureStore({
  reducer: {
    counter: counterReducer
  }
})
```

Redux ストアは、Redux Toolkit の `configureStore` 関数で作成されています。`configureStore` には `reducer` 引数を渡す必要があります。

アプリケーションには多くの異なる機能があるかもしれず、それぞれの機能が独自のリデューサ関数を持っているかもしれません。`configureStore` を呼び出すときには、それら別々のリデューサを 1 つのオブジェクトに入れて渡すことができます。このオブジェクトのキー名は最終的なステートの値のキーになります。

カウンタロジック用のリデューサ関数をエクスポートしている `features/counter/counterSlice.js` という名前のファイルがあります。その `counterReducer` 関数をインポートして、ストアを作成する際に含めるようにします。

`{counter: counterReducer}` のようなオブジェクトを渡すと、「Redux ステートオブジェクトに `state.counter` というセクションが必要で、`counterReducer` という関数がこの `state.counter` セクションの担当であり、アクションがディスパッチされた時にこれを更新するかどうか、更新する場合はどう更新するのかを決定する」という意味になります。

Redux では、複数の種類のプラグイン（「ミドルウェア」と「エンハンサ」）でストアのセットアップをカスタマイズすることができます。`configureStore` は、よい開発者体験を提供するために、デフォルトでストアのセットアップにいくつかのミドルウェアを自動的に追加し、Redux DevTools 拡張機能がその内容を確認できるようにストアを設定します。

#### Redux スライス (slice)

**スライスとは、あるひとつの機能に対応する Redux のリデューサロジックとアクションとをひとまとめにしたもの**です。これは典型的には 1 つのファイル内でまとめて定義されます。ルートとなる Redux ステートオブジェクトを複数の「薄切り」に分割するものという意味で、このような名前がついています。

例えば、ブログアプリでは、ストアの構成は以下のようになるでしょう：

```js
import { configureStore } from '@reduxjs/toolkit'
import usersReducer from '../features/users/usersSlice'
import postsReducer from '../features/posts/postsSlice'
import commentsReducer from '../features/comments/commentsSlice'

export default configureStore({
  reducer: {
    users: usersReducer,
    posts: postsReducer,
    comments: commentsReducer
  }
})
```

この例では `state.users`、`state.posts` および `state.comments` がそれぞれ、Redux ステートの別々の「スライス」になります。`usersReducer` は `state.users` の更新が責務ですので、これを「スライスリデューサ」のように呼びます。

<DetailedExplanation title="詳細説明：リデューサとステートの構造">

Redux ストアを作る際は単一の「ルートリデューサ」関数を渡す必要があります。ではたくさんのスライスリデューサがある場合に、そこからどうやって単一のルートリデューサが得られ、それはどのように Redux ストアのステートを決めるのでしょうか。

スライスリデューサの呼び出しを手書きしようと思ったら、このようなものになります：

```js
function rootReducer(state = {}, action) {
  return {
    users: usersReducer(state.users, action),
    posts: postsReducer(state.posts, action),
    comments: commentsReducer(state.comments, action)
  }
}
```

これは、それぞれのリデューサを個別に呼び出して Redux ステートのスライスを渡し、それぞれの戻り値を新たな Redux ステートオブジェクトにまとめて返しています。

Redux にはこれを自動的に行う [`combineReducers`](../../api/combineReducers.md) という関数があります。スライスリデューサが含まれたオブジェクトを引数にとり、アクションがディスパッチされた時にスライスリデューサをそれぞれ呼び出すような関数を返します。それぞれのスライスリデューサからの戻り値は、1 つのオブジェクトにまとめられて最終結果になります。前述の例は `combineReducers` を使っても同様に行えます：

```js
const rootReducer = combineReducers({
  users: usersReducer,
  posts: postsReducer,
  comments: commentsReducer
})
```

`configureStore` にスライスリデューサの含まれたオブジェクトを渡す場合、この関数はそれを `combineReducers` に渡してルートリデューサを生成します。

以前見たように、`reducer` 引数にはリデューサ関数を直接渡すこともできます：

```js
const store = configureStore({
  reducer: rootReducer
})
```

</DetailedExplanation>

### スライスリデューサとアクションの作成

`counterReducer` 関数は `features/counter/counterSlice.js` ファイルにあるということが分かっていますので、そのファイルに何があるのか、ひとつひとつ見てみましょう。

```js title="features/counter/counterSlice.js"
import { createSlice } from '@reduxjs/toolkit'

export const counterSlice = createSlice({
  name: 'counter',
  initialState: {
    value: 0
  },
  reducers: {
    increment: state => {
      // Redux Toolkit allows us to write "mutating" logic in reducers. It
      // doesn't actually mutate the state because it uses the immer library,
      // which detects changes to a "draft state" and produces a brand new
      // immutable state based off those changes
      state.value += 1
    },
    decrement: state => {
      state.value -= 1
    },
    incrementByAmount: (state, action) => {
      state.value += action.payload
    }
  }
})

export const { increment, decrement, incrementByAmount } = counterSlice.actions

export default counterSlice.reducer
```

さきほど、UI 上の各ボタンをクリックすることで 3 種類の Redux アクションタイプがディスパッチされることを確認しました：

- `{type: "counter/increment"}`
- `{type: "counter/decrement"}`
- `{type: "counter/incrementByAmount"}`

既に我々は、アクションとは `type` フィールドを有するプレーンなオブジェクトであり、`type` は必ず文字列型であり、通常は「アクションクリエータ」関数を作ってアクションオブジェクトを作成して返すようにする、ということを知っています。ではこれらのアクションオブジェクトや type 文字列やアクションクリエータは、どのように定義されるのでしょう？

これらは毎回手で書いても*構いません*。しかしこれは大変です。それに Redux にとって*本当に*重要なのはリデューサ関数であり、そこにある新しいステートを計算するためのロジックです。

Redux Toolkit には `createSlice` という関数があり、アクションのタイプ文字列、アクションクリエータ関数、そしてアクションオブジェクトの作成について面倒を見てくれます。スライスに名前を付けて、リデューサ関数の含まれたオブジェクトを書くだけで、対応するアクションのコードを自動的に作成してくれます。それぞれのアクションタイプの前半部分には `name` オプションの文字列が使われ、後半部分には各リデューサ関数のキー名が使われます。従って、`"counter"` という name と `"increment"` というリデューサ関数名から `{type: "counter/increment"}` というアクションタイプが作成されました。（まあコンピュータがこれをやれるんだったら手書きをする理由はないですよね！）

`name` フィールドに加え、`createSilce` にはリデューサが初めて呼ばれる際に `state` が存在するよう、初期ステートを渡す必要があります。今回のケースでは、0 から始まる `value` フィールドを持ったオブジェクトを渡しています。

ここでは 3 つのリデューサ関数があり、それらが 3 つのボタンをクリックした際にディスパッチされるそれぞれのアクションタイプに対応しているということが分かります。

`createSlice` は、我々が書いたリデューサ関数と同名のアクションクリエータを自動的に作成します。このことは、作成されたものをコールして何が返ってくるか見てみることで確認できます。

```js
console.log(counterSlice.actions.increment())
// {type: "counter/increment"}
```

また、これらの全アクションタイプにどう対応すべきか知っている、スライスリデューサも作成されます：

```js
const newState = counterSlice.reducer(
  { value: 10 },
  counterSlice.actions.increment()
)
console.log(newState)
// {value: 11}
```

### リデューサのルール

以前、リデューサは**常に**以下の特別なルールに従わなければならないと言いました：

- `state` と `action` 引数に基づいて新しいステートを計算する以外のことをしてはいけない。
- 既存の `state` を書き換えてはいけない。代わりに既存の `state` をコピーし、コピーに変更を加えるという、*イミュータブルな更新*を行わなければならない。
- 非同期なロジックを実行したり、その他「副作用 (side effect)」を引き起こしてはいけない。

これらのルールはなぜ重要なのでしょうか。理由はいくつかあります：

- Redux の目的のひとつは、コードを予測可能にすることです。関数の出力が入力である引数のみから計算されるのであれば、どのように動いているのか理解しやすくなり、テストもしやすくなります。
- 一方で関数が外部にある変数に依存している場合やランダムに動作する場合、実行した場合に何が起こるのかが分からなくなります。
- 関数が（それ自体に渡された引数も含む）ほかの値を変更する場合、予想外の形でアプリケーションの挙動が変わってしまうことがあります。これは「ステートを更新したのに UI が変わらない！」のようなよくあるバグの原因となります。
- Redux DevTools の機能のうちいくつかは、リデューサがこれらのルールに正しく従っているということに依存しています。

特に「イミュータブルな更新」に関するルールは重要なので、ここでより詳しく述べておくべきでしょう。

### リデューサとイミュータブルな更新

以前「ミューテーション」（既存のオブジェクトや配列の中身を書き換えること）と「イミュータビリティ」（値を不変のものとして扱うこと）について説明しました。

Redux において、**リデューサは*絶対に*元の（あるいは現在の）ステートの値を書き換えてはいけません！**

```js
// ❌ Illegal - don't do this in a normal reducer!
state.value = 123
```

:::

Redux でステートを書き換えてはいけない理由はいくつかあります：

- UI が最新の値を表示するよう更新されないといったバグを引き起こす
- ステートがなぜどのように更新されたのか理解しづらくなる
- テストが書きづらくなる
- 「タイムトラベルデバッギング」が正しく動作しなくなる
- なんにせよ Redux の精神と利用パターンに違反している

オリジナルのステートを書き換えられないなら、どのようにして更新されたステートを返すのでしょうか？

:::tip

**リデューサは元の値の*コピー*を作成することしかできず、逆にそのコピーに対しては書き換えを行えます。**

```js
// ✅ This is safe, because we made a copy
return {
  ...state,
  value: 123
}
```

:::

JavaScript の配列/オブジェクトスプレッド構文や元の値のコピーを返す関数を使えば、[イミュータブルな更新を手書きで書く](./part-1-overview-concepts.md#immutability)ことができることはすでに見てきました。しかし、「この方法でイミュータブルな更新を手書きで書くのは、覚えるのが大変そうだし、正しくやるのも大変そう」と思っているなら...はい、全くその通りです :)

イミュータブルな更新ロジックを手書きで書くのは*確かに*難しく、リデューサ内でステートを書き換えてしまうことは Redux ユーザがやってしまう最もよくあるミスです。

**そしてこれこそが、Redux Toolkit の `createSlice` 関数を使えばイミュータブルな更新をより簡単に書けるようになっている理由です！**

`createSlice` は、内部で [Immer](https://immerjs.github.io/immer/docs/introduction) というライブラリを使用しています。Immer は `Proxy` と呼ばれる特別な JS ツールを使ってあなたのデータをラップし、ラップされたデータを「書き換え」るようなコードを書けるようにしてくれます。しかし、**Immer はあなたが行ったすべての変更を追跡し、その変更のリストを使って安全にイミュータブルに更新された値**を返し、まるで自分でイミュータブルな更新のロジックを手書きしたかのようにしてくれるのです。

なので、こう書く代わりに：

```js
function handwrittenReducer(state, action) {
  return {
    ...state,
    first: {
      ...state.first,
      second: {
        ...state.first.second,
        [action.someId]: {
          ...state.first.second[action.someId],
          fourth: action.someValue
        }
      }
    }
  }
}
```

このようなコードを書くことができます：

```js
function reducerWithImmer(state, action) {
  state.first.second[action.someId].fourth = action.someValue
}
```

これはずっと読みやすいですね！

しかし以下のことは*非常に*重要ですので覚えておいてください：

:::warning

**Redux Toolkit の `createSlice` と `createReducer` では内部で Immer を使用しているため、「ミューテート的な」ロジックを書くことができます。Immer を使用していないリデューサでミューテートを伴うロジックを書くと、ステートの書き換えが*本当に*生じてバグが発生します！**

:::

これを念頭に置いて、カウンタスライスの実際のリデューサに戻って確認してみましょう。

```js title="features/counter/counterSlice.js"
export const counterSlice = createSlice({
  name: 'counter',
  initialState: {
    value: 0
  },
  reducers: {
    increment: state => {
      // Redux Toolkit allows us to write "mutating" logic in reducers. It
      // doesn't actually mutate the state because it uses the immer library,
      // which detects changes to a "draft state" and produces a brand new
      // immutable state based off those changes
      state.value += 1
    },
    decrement: state => {
      state.value -= 1
    },
    incrementByAmount: (state, action) => {
      // highlight-next-line
      state.value += action.payload
    }
  }
})
```

`increment` リデューサは `state.value` に常に 1 を加算することがわかります。ドラフトである `state` オブジェクトに変更が加わったことが Immer には分かるので、ここで実際に何かを返す必要はありません。同様に、`decrement` リデューサは 1 を減算します。

どちらのリデューサでも、コードが `action` オブジェクトを見る必要はありません。オブジェクトは何にせよ渡されますが、必要ないので、`action` をリデューサのパラメータとして宣言しないで構いません。

一方、`incrementByAmount` リデューサは、カウンタの値にいくら加算すべきかを知る必要があります。そこで、リデューサを `state` と `action` の両方の引数を持つ形で宣言します。今回は、テキストボックスに入力した値が `action.payload` フィールドに入力されることがわかっているので、それを `state.value` に加算できます。

:::info もっと知りたい場合

イミュータビリティとイミュータブルな更新についての詳細は、["Immutable Update Patterns" ドキュメント](../../recipes/structuring-reducers/ImmutableUpdatePatterns.md)と [The Complete Guide to Immutability in React and Redux](https://daveceddia.com/react-redux-immutability-guide/) を参照してください。

:::

### 非同期なロジックを thunk で記述する

これまでのところ、私たちのアプリケーションのすべてのロジックは同期的でした。アクションがディスパッチされ、ストアがリデューサを実行して新しいステートを計算し、ディスパッチ関数が終了します。しかし、JavaScript 言語には非同期のコードを書くための様々な方法があり、我々のアプリにも通常は API からのデータフェッチなどの非同期なロジックがあります。Redux アプリの中に非同期ロジックを置く場所が必要です。

**Thunk** とは、非同期ロジックを含むことができる、ある種の Redux 関数のことです。Thunk は 2 つの関数を組み合わせて書きます：

- `dispatch` と `getState` を引数で受け取る内側の thunk 関数
- thunk 関数を作成して返す外側のクリエータ関数

以下で `counterSlice` からエクスポートしている関数が、thunk 型アクションクリエータの例です。

```js title="features/counter/counterSlice.js"
// The function below is called a thunk and allows us to perform async logic.
// It can be dispatched like a regular action: `dispatch(incrementAsync(10))`.
// This will call the thunk with the `dispatch` function as the first argument.
// Async code can then be executed and other actions can be dispatched
export const incrementAsync = amount => dispatch => {
  setTimeout(() => {
    dispatch(incrementByAmount(amount))
  }, 1000)
}
```

これは普通の Redux アクションクリエータと同じように使えます：

```js
store.dispatch(incrementAsync(5))
```

ただし thunk を使用するには、ストアを作成する際に `redux-thunk` _ミドルウェア_（Redux 用のプラグインの一種）をストアに追加する必要があります。幸い、Redux Toolkit の `configureStore` 関数はこれを既に自動的に設定してくれていますので、このまま thunk を使うことができます。

サーバからデータを取得するために AJAX コールを行う必要がある場合、そのコールを thunk の中に置くことができます。ここに少し長めに書いた例がありますので、どのように定義されているか見てください：

```js title="features/counter/counterSlice.js"
// the outside "thunk creator" function
const fetchUserById = userId => {
  // the inside "thunk function"
  return async (dispatch, getState) => {
    try {
      // make an async call in the thunk
      const user = await userAPI.fetchById(userId)
      // dispatch an action when we get the response back
      dispatch(userLoaded(user))
    } catch (err) {
      // If something went wrong, handle it here
    }
  }
}
```

thunk の使われかたは[パート 5: Async Logic and Data Fetching](./part-5-async-logic.md) で見ていきます。

<DetailedExplanation title="詳細：thunk と非同期ロジック">

リデューサに非同期ロジックを入れてはいけないことはご存じですね。しかし、その非同期なロジックはどこかには存在する必要があります。

Redux ストアにアクセスできるのであれば、何か非同期コードを書いて、終わったときに `store.dispatch()` を呼び出すことができるでしょう。

```js
const store = configureStore({ reducer: counterReducer })

setTimeout(() => {
  store.dispatch(increment())
}, 250)
```

しかし、実際の Redux アプリでは、他のファイル、特に React コンポーネントにストアをインポートすることはできません。それをするとコードはテストや再利用が難しくなってしまいます。

さらに、*何らかの*ストアで使われるが*どの*ストアで使われるのか分からない状態で非同期ロジックを書かないといけないこともあります。

Redux ストアは「ミドルウェア」を使って拡張することができます。これは機能を追加できるアドオンやプラグインのようなものです。ミドルウェアを使う最も一般的な理由は、非同期ロジックを持ちつつ同時にストアと通信できるコードを書けるようにするためです。それらはストアの挙動を変更し、`dispatch()` をコールした時に関数や Promise のようなプレーンなアクションオブジェクト*ではない*値を渡せるようにもできます。

Redux Thunk ミドルウェアは、ストアの挙動を変更して `dispatch` に関数を渡せるようにします。実際に、そのためのコードはここに全部ペーストできるほどに短いものです：

```js
const thunkMiddleware = ({ dispatch, getState }) => next => action => {
  if (typeof action === 'function') {
    return action(dispatch, getState, extraArgument)
  }

  return next(action)
}
```

このミドルウェアは、`dispatch` に渡された「アクション」が実際にはプレーンなアクションオブジェクトではなく関数であるかどうかを判定します。関数だった場合はその関数をコールしてその結果を返します。そうでない場合、普通のアクションオブジェクトなのでストアにそのまま送り届けます。

これにより、`dispatch` や `getState` へのアクセスを保ったままで、同期的なコードでも非同期的なコードでも好きなものを書くことができるようになります。

</DetailedExplanation>

このファイルにはもうひとつ関数がありますが、これについてはすぐ後で `<Counter>` UI コンポーネントを見るときにお話しします。

:::info もっと知りたい場合

詳しい情報は [Redux Thunk ドキュメント](https://github.com/reduxjs/redux-thunk)、ブログ記事 [What the heck is a thunk?](https://daveceddia.com/what-is-a-thunk/)、および [Redux FAQ の "why do we use middleware for async?"](../../faq/Actions.md#how-can-i-represent-side-effects-such-as-ajax-calls-why-do-we-need-things-like-action-creators-thunks-and-middleware-to-do-async-behavior) を参照してください。

:::

### React の Counter コンポーネント

以前、React 単体での `<Counter>` コンポーネントがどのようなものかは見ました。我々の React + Redux アプリにも `<Counter>` コンポーネントがありますが、幾つかの動作が異なっています。

まずは `Counter.js` コンポーネントファイルを見てみましょう：

```jsx title="features/counter/Counter.js"
import React, { useState } from 'react'
import { useSelector, useDispatch } from 'react-redux'
import {
  decrement,
  increment,
  incrementByAmount,
  incrementAsync,
  selectCount
} from './counterSlice'
import styles from './Counter.module.css'

export function Counter() {
  const count = useSelector(selectCount)
  const dispatch = useDispatch()
  const [incrementAmount, setIncrementAmount] = useState('2')

  return (
    <div>
      <div className={styles.row}>
        // highlight-start
        <button
          className={styles.button}
          aria-label="Increment value"
          onClick={() => dispatch(increment())}
        >
          +
        </button>
        // highlight-end
        <span className={styles.value}>{count}</span>
        <button
          className={styles.button}
          aria-label="Decrement value"
          onClick={() => dispatch(decrement())}
        >
          -
        </button>
      </div>
      {/* omit additional rendering output here */}
    </div>
  )
}
```

以前のプレーンな React での例と同様、`Counter` という名前の関数コンポーネントがあり、`useState` フックに何かのデータを保持しています。

でもこのコンポーネントでは、カウンタの現在値をステートとして保持していないように見えますね。`count` という名前の変数自体はありますが、それは `useStete` フックからは来ていないようです。

React には `useState` や `useEffect` のような組み込みフックが幾つかありますが、他のライブラリも React のフックを使ってカスタムロジックを組み立てる[カスタムフック](https://reactjs.org/docs/hooks-custom.html)を作成することができます。

[React-Redux ライブラリ](https://react-redux.js.org/)には、[React コンポーネントが Redux ストアとやりとりできるようにするためのカスタムフックが幾つかあります](https://react-redux.js.org/api/hooks)。

#### `useSelector` でデータを読み取る

まず `useSelector` フックですが、これはコンポーネントが Redux ストア内のステートからデータの任意の一部分を取り出せるようにしてくれるものです。

以前、`state` を引数として受け取ってステート値の一部分を返す「セレクタ」関数というものを書けるという話をしました。

`counterSlice.js` の下の方に、このセレクタ関数が書かれています：

```js  title="features/counter/counterSlice.js"
// The function below is called a selector and allows us to select a value from
// the state. Selectors can also be defined inline where they're used instead of
// in the slice file. For example: `useSelector((state) => state.counter.value)`
export const selectCount = state => state.counter.value
```

もし Redux ストアにアクセス可能な状態であれば、カウンタの現在値は以下のように取得できるところです：

```js
const count = selectCount(store.getState())
console.log(count)
// 0
```

Redux ストアをコンポーネントファイルにインポートしてはならないため、我々のコンポーネントは Redux ストアと直接やりとりすることはできません。しかし `useSelector` は、我々の代わりに裏で Redux ストアとやりとりしてくれます。セレクタ関数を渡せば、`someSelector(store.getState())` を呼んで、その結果を返してくれるのです。

従って、現在のストア内のカウンタの値は以下のようにして取得できます：

```js
const count = useSelector(selectCount)
```

どこかからエクスポート済みのセレクタ*しか*使えないというわけでもありません。例えば `useSelector` の引数としてインラインでセレクタ関数を書くこともできます：

```js
const countPlusTwo = useSelector(state => state.counter.value + 2)
```

アクションがディスパッチされ Redux ストアが更新されると常に `useSelector` がセレクタ関数を再実行します。もしセレクタが前回と異なる値を返した場合、`useSelector` はコンポーネントが新しい値で確実に再レンダーされるようにします。

#### `useDispatch` でアクションをディスパッチする

同様に、もしも Redux ストアにアクセスできれば、`store.dispatch(increment())` のようにアクションクリエータを使ってアクションをディスパッチできます。我々は今ストア自体にアクセスできないので、`dispatch` メソッドだけにアクセスできる何らかの方法が必要です。

`useDispatch` フックがそれを行います。Redux ストアから実物の `dispatch` メソッドを渡してくれます。

```js
const dispatch = useDispatch()
```

これがあれば、ユーザがボタンをクリックするなど、何かを行った時にアクションをディスパッチできます：

```jsx  title="features/counter/Counter.js"
<button
  className={styles.button}
  aria-label="Increment value"
  onClick={() => dispatch(increment())}
>
  +
</button>
```

### コンポーネントステートとフォーム

ここまで読んで、「アプリのステートは常に全部 Redux ストアに入れないといけないのか？」と疑問に思っているかもしれません。

答えは **NO** です。**アプリ全体で必要となるようなグローバルなステートは Redux ストアに入れるべきです。が、1 箇所だけで必要となるようなステートはコンポーネントの state に保持しておくべきです**。

以下の例には、カウンタの次の値として加算すべき数値をタイプできるテキスト入力ボックスがあります。

```jsx title="features/counter/Counter.js"
const [incrementAmount, setIncrementAmount] = useState('2')

// later
return (
  <div className={styles.row}>
    <input
      className={styles.textbox}
      aria-label="Set increment amount"
      value={incrementAmount}
      onChange={e => setIncrementAmount(e.target.value)}
    />
    <button
      className={styles.button}
      onClick={() => dispatch(incrementByAmount(Number(incrementAmount) || 0))}
    >
      Add Amount
    </button>
    <button
      className={styles.asyncButton}
      onClick={() => dispatch(incrementAsync(Number(incrementAmount) || 0))}
    >
      Add Async
    </button>
  </div>
)
```

この数値を表す文字列の現在値を Redux ストアに入れ、テキストボックスの `onChange` ハンドラでアクションをディスパッチしてリデューサで管理すること*も*、確かに可能です。しかし、そうする利点は何もありません。このテキスト文字列が使われるのはここだけ、この `<Counter>` コンポーネントの中だけなのです。（まあ今回の例では他に `<App>` しかコンポーネントがないのですが。それでも、アプリが大きくなって多くのコンポーネントができても、この入力値を気にするのは `<Counter>` コンポーネントだけです。）

ですから、この値は `<Counter>` コンポーネントの `useState` フック内で保持しておくのが理にかなっています。

同様に、`isDropdownOpen` のような真偽値のフラグがある場合、アプリ内の他のコンポーネントがそれを気にすることはないのですから、コンポーネントのローカルに留めておくべきです。

**React + Redux アプリでは、グローバルなステートは Redux ストアに書き、ローカルなステートは React コンポーネント内に留めるべきです。**

どこにデータを置くべきか分からない場合、どのようなデータを Redux に入れるかに決めるにあたって以下のようないくつかの経験則があります：

- アプリケーションの他の部分でこのデータを気にすることがあるか？
- この元データから派生的に別のデータを作成できる必要があるか？
- この同一データを、複数のコンポーネントを動かすために使うか？
- このステートをある特定の時点に戻せるということ（タイムトラベルデバッギング）に価値があるか？
- このデータをキャッシュする（つまり既にステート内にある場合は再リクエストせずにそちらを使う）必要があるか？
- UI コンポーネントのホットリローディングの際にもこのデータを保持しておきたいか？（コンポーネントが入れ替わると内部 state は失われる可能性がある）

また、これは Redux におけるフォームについての一般的な考え方を示す良い例ともなっています。**フォームのステートのほとんどは、おそらく Redux に入れる必要がありません。**代わりに、ユーザが編集している間はデータをフォームコンポーネントに入れるようにし、ユーザの編集が終わったときに Redux のアクションをディスパッチしてストアをアップデートするようにしてください。

先に進む前にもう一点。`counterSlice.js` の `incrementAsync` について覚えていますか？ それをこのコンポーネントで使っていますが、他の普通のアクションクリエータと同じように使っている、ということに気をつけてください。このコンポーネントは、普通のアクションをディスパッチしているのか、何か非同期なロジックを開始しようとしているのかを気にしません。ボタンがクリックされると何かをディスパッチするということを知っているだけなのです。

### ツリーへのストアの公開

コンポーネントが `useSelector` と `useDispatch` フックを使って Redux ストアとやり取りできることはわかりました。しかし、ストアをインポートしていないのに、これらのフックはどの Redux ストアとやりとりするのか、どうやって知るのでしょうか？

ここまでこのアプリの様々な部品のすべてを見てきましたが、このアプリケーションの開始地点に戻って、パズルの最後のピースがどのようにはまるのかを見てみましょう。

```jsx  title="index.js"
import React from 'react'
import ReactDOM from 'react-dom'
import './index.css'
import App from './App'
import store from './app/store'
// highlight-next-line
import { Provider } from 'react-redux'
import * as serviceWorker from './serviceWorker'

ReactDOM.render(
  // highlight-start
  <Provider store={store}>
    <App />
  </Provider>,
  // highlight-end
  document.getElementById('root')
)
```

`ReactDOM.render(<App />)` を呼び出して React に `<App>` をレンダーし始めるよう伝える部分は、常にやることです。`useSelector` のようなフックが正しく動作するためには、`<Provider>` というコンポーネントを使って Redux ストアを裏側で渡しておき、フックで使えるようにする必要があります。

ストアは `app/store.js` で既に作っていますので、それをインポートします。それから `<Provider>` を `<App>` の周りに配置して、ストアをこのようにして渡します：`<Provider store={store}>`

これで、`useSelector` や `useDispatch` を呼び出す React コンポーネントは、`<Provider>` に与えた Redux ストアとやりとりするようになります。

## 学んだこと

このカウンタアプリは非常に小さなものですが、React + Redux アプリが統合して動作するための鍵となるピースを全て示しています。以下のようなことを学びました。

:::tip Summary

- **Redux store は Redux Toolkit の `configureStore` API で作成できる**
  - `configureStore` は名前付き引数として `reducer` 関数を受け取る
  - `configureStore` はストアを便利なデフォルト設定つきでセットアップする
- **Redux のロジックは典型的には「スライス」と呼ばれるファイルで管理される**
  - 「スライス」には Redux ステートの特定の機能/部分に関連するリデューサロジックやアクションが含まれている
  - Redux Toolkit の `createSlice` API は、あなたが渡した個々のリデューサ関数のそれぞれに対し、アクションクリエータとアクションタイプを生成する
- **Redux のリデューサは特定のルールに従わなければならない**
  - `state` と `action` 引数から新しいステート値を計算する以外のことをしてはならない
  - 既存のステートコピーすることで*イミュータブルな更新*をしなければならない
  - 非同期ロジックやその他の「副作用」を含んでいてはならない
  - Redux Toolkit の `createSlice` API は Immer を使っているので「ミューテート方式」でイミュータブルな更新ができる
- **非同期ロジックは通常 thunk と呼ばれる特別な関数で書く**
  - thunk は `dispatch` と `getState` を引数として受け取る
  - Redux Toolkit は `redux-thunk` ミドルウェアをデフォルトで有効化する
- **React-Redux を使うことで React コンポーネントは Redux ストアとやりとりできる**
  - アプリを `<Provider store={store}>` でラップすることで全コンポーネントがストアを使えるようになる
  - グローバルなステートは Redux ストアに書き、ローカルなステートは React コンポーネントに残す

:::

## 次は？

Redux アプリのすべての部品が動作する様子を見終えたので、自分自身でアプリを書く時です。このチュートリアルの残りの部分では、Redux を使用してより大きなサンプルアプリを作成していきます。そうしながら、Redux を正しい方法で使用するために知っておくべき重要なアイデアをすべて説明していきます。

[Part 3: Basic Redux Data Flow](./part-3-data-flow.md)に進んで、サンプルアプリの作成を始めましょう。
