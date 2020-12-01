---
id: style-guide
title: Style Guide
description: 'Redux Style Guide: recommended patterns and best practices for using Redux'
hide_title: true
sidebar_label: 'スタイルガイド：ベストプラクティス'
---

import { DetailedExplanation } from '../components/DetailedExplanation'

<div class="style-guide">

# Redux スタイルガイド

## はじめに

この記事が Redux のコードを書く際の公式なスタイルガイドです。**Redux アプリケーションを書く際に我々が推奨するパターン、ベストプラクティス、アプローチが並んでいます**。

Redux コアライブラリと Redux ドキュメントのほとんどの部分は unopinionated です。Redux には多くの使い方があり、多くの場合は唯一の「正しい」やり方というのは存在しません。

しかし時間とともに経験が蓄積され、いくつかのトピックにおいては特定のアプローチが他よりうまく動くということが分かってきました。また、多くの開発者が、意思決定の負担を軽減するための公式なガイダンスを求めています。

これを念頭に、**エラーや雑多な議論やアンチパターンを防ぐための推奨事項を一覧にまとめました**。一方でチームによって好みは異なり、プロジェクトによって要求は違うため、すべてに当てはまるようなスタイルガイドは存在しないということも理解しています。**以下の推奨事項に従うようお勧めしますが、時間をかけて自分の状況を評価し、あなたのニーズに合ったものか判断するようにしてください**。

最後に、[Vue Style Guide page](https://vuejs.org/v2/style-guide/) を書き、この記事を書くインスピレーションを与えてくださった Vue ドキュメントの著者らに感謝します。

## ルールカテゴリ

以下ではルールを 3 つのカテゴリに分けました。

### 優先度 A: 必須 (Essential)

**これらのルールはエラーを防ぐために役立つものであり、何としてでも身に着け、従うようにしてください**。例外は存在するかもしれませんが非常に稀であり、JavaScript と Redux の両方に関する専門的な知識がある場合にのみ認められるものです。

### 優先度 B: 強く推奨 (Strongly Recommended)

ほとんどのプロジェクトにおいて可読性や開発者体験を向上することが分かっているルールです。これらに違反してもコードは動作するでしょうが、違反はできるだけ避け、違反する場合でも正当な理由があるようにしてください。**合理的に可能な限りこれらのルールに従ってください**。

### 優先度 C: 推奨 (Recommended)

同等程度に良い選択肢が複数ある場合でも、一貫性を保証するためにどれか 1 つに決めることはできます。これらのルールにおいては、**可能な選択肢を説明したうえでデフォルトの選択肢を提案します**。すなわち、一貫性のある正当な理由があるのなら、あなたのコードベースでこれと異なる選択を行っても構わない、ということです。とはいえ、ちゃんと正当な理由があるようにしてください！

<div class="priority-rules priority-essential">

## 優先度 A のルール：必須

### ステートをミューテートしない

ステートを書き換えてしまうというのは、コンポーネントが正しく再レンダーされないといった Redux アプリのバグの最もよくある原因であり、Redux DevTools でのタイムトラベルデバッギングも動作しなくなります。リデューサ内でも、それ以外のアプリケーションコードのどんな場所でも、**ステート値を実際に書き換えることは常に避けてください**。

開発中のミューテートを捕捉するために [`redux-immutable-state-invariant`](https://github.com/leoasis/redux-immutable-state-invariant) のようなツールを使い、ステート更新中にうっかりミューテートしてしまうことを防ぐために [Immer](https://immerjs.github.io/immer/docs/introduction) を使ってください。

> **補足**：既存の値の*コピー*を変更することは構いません。それはイミュータブルな更新ロジックの一環として普通に行うことです。また、イミュターブルな更新に Immer ライブラリを使う場合は、Immer が安全に変更を追跡してイミュータブルに更新された値を内部で作成し、実際のデータの書き換えは行われないようにするため、「ミューテート的な」ロジックを書いても構いません。

### リデューサが副作用を持ってはいけない

リデューサ関数は `state` と `action` 引数に*のみ*依存し、これらの引数に基づいて新しいステート値を計算して返す以外のことをしてはなりません。**非同期的なロジック（AJAX コール、タイムアウト、Promise）を実行したり、ランダムな値（`Date.now()` や `Math.random()`）を生成したり、リデューサの外にある変数を変更したり、リデューサ関数の管轄外のものに影響するような外部のコードを呼んだりしてはなりません**。

> **補足**：ライブラリからインポートした関数やユーティリティ関数がこれと同じルールに従っている限りは、リデューサ内からそれらのリデューサ外で定義されている関数をコールしても構いません。

<DetailedExplanation>

このルールの目的は、リデューサが呼び出されたときに予測可能な動作をすることを保証することです。例えば、タイムトラベルデバッグを行う場合、ステートの「現在」値を生成するために、以前のアクションで何度もリデューサ関数が呼び出されることがあります。もしリデューサに副作用があると、デバッグ作業中にその副作用が実行され、アプリケーションが予期しない動作をすることになります。

このルールには若干のグレーゾーンがあります。厳密に言えば、`console.log(state)` のようなコードは副作用ですが、実際にはアプリケーションの動作には何の影響もありません。

</DetailedExplanation>

### ステートやアクションにシリアライズ不可能な値を入れてはならない

**Promise、シンボル、Map/Set、関数、クラスインスタンスのようなシリアライズできない値を Redux ストアのステートやディスパッチされるアクションに入れないようにしてください**。これにより、Redux DevTool を使ったデバッグなどが期待通り動作するようになります。また UI が期待通りに更新されることも保証されます。

> **例外**：リデューサに到達する前にミドルウェアによってアクションが捕捉されて処理される場合は、アクションにシリアライズできない値を入れても構いません。`redux-thunk` や `redux-prmise` のようなミドルウェアはこのような例です。

### アプリに Redux ストアは 1 つだけ

**標準的な Redux アプリでは Redux ストアインスタンスは 1 つだけにし、それがアプリ全体で使われるようにするべきです**。典型的には `store.js` のような独立したファイルで定義されるようにします。

理想的にはどんなアプリのロジックもストアを直接インポートしないようにするべきです。ストアは React コンポーネントツリーに `<Provider>` を通じて渡されるようにするか、thunk などから間接的に参照されるようにします。稀なケースではストアを他のロジックファイルに直接インポートする必要があるかもしれませんが、これは最終手段であるべきです。

</div>

<div class="priority-rules priority-stronglyrecommended">

## 優先度 B のルール：強く推奨

### Redux のロジックを書くのに Redux Toolkit を使う

**[Redux Toolkit](../redux-toolkit/overview.md) は Redux を使うにあたって我々が推奨するツールセットです**。ミューテーションを捕捉するようストアをセットアップしたり、Redux DevTools 拡張機能を有効にしたり、Immer を使ってイミュータブルな更新を簡単にしたり、といった我々が提唱するベストプラクティスを組み込むための機能が含まれています。

Redux で RTK を使う必要はありませんし、他のアプローチをとりたいならそうするのも自由ですが、**RTK を使うことであなたのロジックがシンプルになり、アプリが良いデフォルトでセットアップされるようになります**。

### イミュータブルな更新を記述するのに Immer を使う

イミュータブルなロジックを手書きするのはしばしば困難でありエラーの原因ともなります。[Immer](https://immerjs.github.io/immer/docs/introduction) を使うと「ミューテート的な」ロジックを使ってシンプルにイミュータブルな更新を記述できるようになり、開発時にはステートをフリーズすることでアプリの他の部分でミューテートが起こった時に気付けるようになります。**（できれば [Redux Toolkit](../redux-toolkit/overview.md) を使う一環として）Immer を使いイミュータブルな更新ロジックを書くことを推奨します**。

### ファイルを機能別に（Duck パターンで）配置する

Redux 自体はアプリのフォルダやファイルがどのように構成されえているのかを気にしません。しかしある機能に関わるロジックを 1 箇所にまとめることで通常はコードがメンテナンスしやすくなります。

Because of this, **we recommend that most applications should structure files using a "feature folder" approach** (all files for a feature in the same folder) **or the ["ducks" pattern](https://github.com/erikras/ducks-modular-redux)** (all Redux logic for a feature in a single file), rather than splitting logic across separate folders by "type" of code (reducers, actions, etc).

このため、コードのタイプ（リデューサやアクションなど）で分けてロジックを複数フォルダに分散させるのではなく、**ほとんどのアプリでは「機能別フォルダ」のアプローチ**（同じ機能のファイルは同じフォルダに置く）、や [**"ducks" パターン**](https://github.com/erikras/ducks-modular-redux)（同じ機能の Redux ロジックは同じファイルに書く）をお勧めします。

<DetailedExplanation>
フォルダ構成の例はこのようなものになります：

- `/src`
  - `index.tsx`
  - `/app`
    - `store.ts`
    - `rootReducer.ts`
    - `App.tsx`
  - `/common`
    - hooks, generic components, utils, etc
  - `/features`
    - `/todos`
      - `todosSlice.ts`
      - `Todos.tsx`

`/app` には他のフォルダのすべてに依存するようあなアプリ全体のセットアップやレイアウトコードを入れます。

`/common` には真に一般性があり再利用可能なユーティリティやコンポーネントを入れます。

`/features` には特定の機能に関係するようなすべてのコードを含めます。この例では `todosSlice.ts` は RTK の `createSlice()` 関数の呼び出しを含んだ "duck" スタイルのファイルであり、スライスリデューサやアクションクリエータをエクスポートしています。
</DetailedExplanation>

### ロジックはできるだけリデューサに書く

Wherever possible, **try to put as much of the logic for calculating a new state into the appropriate reducer, rather than in the code that prepares and dispatches the action** (like a click handler). This helps ensure that more of the actual app logic is easily testable, enables more effective use of time-travel debugging, and helps avoid common mistakes that can lead to mutations and bugs.

There are valid cases where some or all of the new state should be calculated first (such as generating a unique ID), but that should be kept to a minimum.

<DetailedExplanation>

The Redux core does not actually care whether a new state value is calculated in the reducer or in the action creation logic. For example, for a todo app, the logic for a "toggle todo" action requires immutably updating an array of todos. It is legal to have the action contain just the todo ID and calculate the new array in the reducer:

```js
// Click handler:
const onTodoClicked = (id) => {
    dispatch({type: "todos/toggleTodo", payload: {id}})
}

// Reducer:
case "todos/toggleTodo": {
    return state.map(todo => {
        if(todo.id !== action.payload.id) return todo;

        return {...todo, id: action.payload.id};
    })
}
```

And also to calculate the new array first and put the entire new array in the action:

```js
// Click handler:
const onTodoClicked = id => {
  const newTodos = todos.map(todo => {
    if (todo.id !== id) return todo

    return { ...todo, id }
  })

  dispatch({ type: 'todos/toggleTodo', payload: { todos: newTodos } })
}

// Reducer:
case "todos/toggleTodo":
    return action.payload.todos;
```

However, doing the logic in the reducer is preferable for several reasons:

- Reducers are always easy to test, because they are pure functions - you just call `const result = reducer(testState, action)`, and assert that the result is what you expected. So, the more logic you can put in a reducer, the more logic you have that is easily testable.
- Redux state updates must always follow [the rules of immutable updates](../recipes/structuring-reducers/ImmutableUpdatePatterns.md). Most Redux users realize they have to follow the rules inside a reducer, but it's not obvious that you _also_ have to do this if the new state is calculated _outside_ the reducer. This can easily lead to mistakes like accidental mutations, or even reading a value from the Redux store and passing it right back inside an action. Doing all of the state calculations in a reducer avoids those mistakes.
- If you are using Redux Toolkit or Immer, it is much easier to write immutable update logic in reducers, and Immer will freeze the state and catch accidental mutations.
- Time-travel debugging works by letting you "undo" a dispatched action, then either do something different or "redo" the action. In addition, hot-reloading of reducers normally involves re-running the new reducer with the existing actions. If you have a correct action but a buggy reducer, you can edit the reducer to fix the bug, hot-reload it, and you should get the correct state right away. If the action itself was wrong, you'd have to re-run the steps that led to that action being dispatched. So, it's easier to debug if more logic is in the reducer.
- Finally, putting logic in reducers means you know where to look for the update logic, instead of having it scattered in random other parts of the application code.

</DetailedExplanation>

### ステートの形はリデューサが決める

The Redux root state is owned and calculated by the single root reducer function. For maintainability, that reducer is intended to be split up by key/value "slices", with **each "slice reducer" being responsible for providing an initial value and calculating the updates to that slice of the state**.

In addition, slice reducers should exercise control over what other values are returned as part of the calculated state. **Minimize the use of "blind spreads/returns"** like `return action.payload` or `return {...state, ...action.payload}`, because those rely on the code that dispatched the action to correctly format the contents, and the reducer effectively gives up its ownership of what that state looks like. That can lead to bugs if the action contents are not correct.

> **Note**: A "spread return" reducer may be a reasonable choice for scenarios like editing data in a form, where writing a separate action type for each individual field would be time-consuming and of little benefit.

<DetailedExplanation>
Picture a "current user" reducer that looks like:

```js
const initialState = {
    firstName: null,
    lastName: null,
    age: null,
};

export default usersReducer = (state = initialState, action) {
    switch(action.type) {
        case "users/userLoggedIn": {
            return action.payload;
        }
        default: return state;
    }
}
```

In this example, the reducer completely assumes that `action.payload` is going to be a correctly formatted object.

However, imagine if some part of the code were to dispatch a "todo" object inside the action, instead of a "user" object:

```js
dispatch({
  type: 'users/userLoggedIn',
  payload: {
    id: 42,
    text: 'Buy milk'
  }
})
```

The reducer would blindly return the todo, and now the rest of the app would likely break when it tries to read the user from the store.

This could be at least partly fixed if the reducer has some validation checks to ensure that `action.payload` actually has the right fields, or tries to read the right fields out by name. That does add more code, though, so it's a question of trading off more code for safety.

Use of static typing does make this kind of code safer and somewhat more acceptable. If the reducer knows that `action` is a `PayloadAction<User>`, then it _should_ be safe to do `return action.payload`.

</DetailedExplanation>

### 保存されているデータに基づいてステートスライスの名前を付ける

As mentioned in [Reducers Should Own the State Shape ](#reducers-should-own-the-state-shape), the standard approach for splitting reducer logic is based on "slices" of state. Correspondingly, `combineReducers` is the standard function for joining those slice reducers into a larger reducer function.

The key names in the object passed to `combineReducers` will define the names of the keys in the resulting state object. Be sure to name these keys after the data that is kept inside, and avoid use of the word "reducer" in the key names. Your object should look like `{users: {}, posts: {}}`, rather than `{usersReducer: {}, postsReducer: {}}`.

<DetailedExplanation>
ES6 object literal shorthand makes it easy to define a key name and a value in an object at the same time:

```js
const data = 42
const obj = { data }
// same as: {data: data}
```

`combineReducers` accepts an object full of reducer functions, and uses that to generate state objects that have the same key names. This means that the key names in the functions object define the key names in the state object.

This results in a common mistake, where a reducer is imported using "reducer" in the variable name, and then passed to `combineReducers` using the object literal shorthand:

```js
import usersReducer from 'features/users/usersSlice'

const rootReducer = combineReducers({
  usersReducer
})
```

In this case, use of the object literal shorthand created an object like `{usersReducer: usersReducer}`. So, "reducer" is now in the state key name. This is redundant and useless.

Instead, define key names that only relate to the data inside. We suggest using explicit `key: value` syntax for clarity:

```js
import usersReducer from 'features/users/usersSlice'
import postsReducer from 'features/posts/postsSlice'

const rootReducer = combineReducers({
  users: usersReducer,
  posts: postsReducer
})
```

It's a bit more typing, but it results in the most understandable code and state definition.

</DetailedExplanation>

### リデューサをステートマシンとして取り扱う

Many Redux reducers are written "unconditionally". They only look at the dispatched action and calculate a new state value, without basing any of the logic on what the current state might be. This can cause bugs, as some actions may not be "valid" conceptually at certain times depending on the rest of the app logic. For example, a "request succeeded" action should only have a new value calculated if the state says that it's already "loading", or an "update this item" action should only be dispatched if there is an item marked as "being edited".

To fix this, **treat reducers as "state machines", where the combination of both the current state _and_ the dispatched action determines whether a new state value is actually calculated**, not just the action itself unconditionally.

<DetailedExplanation>

A [finite state machine](https://en.wikipedia.org/wiki/Finite-state_machine) is a useful way of modeling something that should only be in one of a finite number of "finite states" at any time. For example, if you have a `fetchUserReducer`, the finite states can be:

- `"idle"` (fetching not started yet)
- `"loading"` (currently fetching the user)
- `"success"` (user fetched successfully)
- `"failure"` (user failed to fetch)

To make these finite states clear and [make impossible states impossible](https://kentcdodds.com/blog/make-impossible-states-impossible), you can specify a property that holds this finite state:

```js
const initialUserState = {
  status: 'idle', // explicit finite state
  user: null,
  error: null
}
```

With TypeScript, this also makes it easy to use [discriminated unions](https://basarat.gitbook.io/typescript/type-system/discriminated-unions) to represent each finite state. For instance, if `state.status === 'success'`, then you would expect `state.user` to be defined and wouldn't expect `state.error` to be truthy. You can enforce this with types.

Typically, reducer logic is written by taking the action into account first. When modeling logic with state machines, it's important to take the state into account first. Creating "finite state reducers" for each state helps encapsulate behavior per state:

```js
import {
  FETCH_USER,
  // ...
} from './actions'

const IDLE_STATUS = 'idle';
const LOADING_STATUS = 'loading';
const SUCCESS_STATUS = 'success';
const FAILURE_STATUS = 'failure';

const fetchIdleUserReducer = (state, action) => {
  // state.status is "idle"
  switch (action.type) {
    case FETCH_USER:
      return {
        ...state,
        status: LOADING_STATUS
      }
    }
    default:
      return state;
  }
}

// ... other reducers

const fetchUserReducer = (state, action) => {
  switch (state.status) {
    case IDLE_STATUS:
      return fetchIdleUserReducer(state, action);
    case LOADING_STATUS:
      return fetchLoadingUserReducer(state, action);
    case SUCCESS_STATUS:
      return fetchSuccessUserReducer(state, action);
    case FAILURE_STATUS:
      return fetchFailureUserReducer(state, action);
    default:
      // this should never be reached
      return state;
  }
}
```

Now, since you're defining behavior per state instead of per action, you also prevent impossible transitions. For instance, a `FETCH_USER` action should have no effect when `status === LOADING_STATUS`, and you can enforce that, instead of accidentally introducing edge-cases.

</DetailedExplanation>

### 複雑な入れ子構造・リレーションを有するステートは正規化する

Many applications need to cache complex data in the store. That data is often received in a nested form from an API, or has relations between different entities in the data (such as a blog that contains Users, Posts, and Comments).

**Prefer storing that data in [a "normalized" form](../recipes/structuring-reducers/NormalizingStateShape.md) in the store**. This makes it easier to look up items based on their ID and update a single item in the store, and ultimately leads to better performance patterns.

### アクションはセッターではなくイベントとしてモデルする

Redux does not care what the contents of the `action.type` field are - it just has to be defined. It is legal to write action types in present tense (`"users/update"`), past tense (`"users/updated"`), described as an event (`"upload/progress"`), or treated as a "setter" (`"users/setUserName"`). It is up to you to determine what a given action means in your application, and how you model those actions.

However, **we recommend trying to treat actions more as "describing events that occurred", rather than "setters"**. Treating actions as "events" generally leads to more meaningful action names, fewer total actions being dispatched, and a more meaningful action log history. Writing "setters" often results in too many individual action types, too many dispatches, and an action log that is less meaningful.

<DetailedExplanation>
Imagine you've got a restaurant app, and someone orders a pizza and a bottle of Coke.  You could dispatch an action like:

```js
{ type: "food/orderAdded",  payload: {pizza: 1, coke: 1} }
```

Or you could dispatch:

```js
{
    type: "orders/setPizzasOrdered",
    payload: {
        amount: getState().orders.pizza + 1,
    }
}

{
    type: "orders/setCokesOrdered",
    payload: {
        amount: getState().orders.coke + 1,
    }
}
```

The first example would be an "event". "Hey, someone ordered a pizza and a pop, deal with it somehow".

The second example is a "setter". "I _know_ there are fields for 'pizzas ordered' and 'pops ordered', and I am commanding you to set their current values to these numbers".

The "event" approach only really needed a single action to be dispatched, and it's more flexible. It doesn't matter how many pizzas were already ordered. Maybe there's no cooks available, so the order gets ignored.

With the "setter" approach, the client code needed to know more about what the actual structure of the state is, what the "right" values should be, and ended up actually having to dispatch multiple actions to finish the "transaction".

</DetailedExplanation>

### 意味のあるアクション名を付ける

The `action.type` field serves two main purposes:

- Reducer logic checks the action type to see if this action should be handled to calculate a new state
- Action types are shown in the Redux DevTools history log for you to read

Per [Model Actions as "Events"](#model-actions-as-events-not-setters), the actual contents of the `type` field do not matter to Redux itself. However, the `type` value _does_ matter to you, the developer. **Actions should be written with meaningful, informative, descriptive type fields**. Ideally, you should be able to read through a list of dispatched action types, and have a good understanding of what happened in the application without even looking at the contents of each action. Avoid using very generic action names like `"SET_DATA"` or `"UPDATE_STORE"`, as they don't provide meaningful information on what happened.

### 多くのリデューサが同じアクションに反応することを許す

Redux reducer logic is intended to be split into many smaller reducers, each independently updating their own portion of the state tree, and all composed back together to form the root reducer function. When a given action is dispatched, it might be handled by all, some, or none of the reducers.

As part of this, you are encouraged to **have many reducer functions all handle the same action separately** if possible. In practice, experience has shown that most actions are typically only handled by a single reducer function, which is fine. But, modeling actions as "events" and allowing many reducers to respond to those actions will typically allow your application's codebase to scale better, and minimize the number of times you need to dispatch multiple actions to accomplish one meaningful update.

### 多くのアクションを連続してディスパッチしない

**Avoid dispatching many actions in a row to accomplish a larger conceptual "transaction"**. This is legal, but will usually result in multiple relatively expensive UI updates, and some of the intermediate states could be potentially invalid by other parts of the application logic. Prefer dispatching a single "event"-type action that results in all of the appropriate state updates at once, or consider use of action batching addons to dispatch multiple actions with only a single UI update at the end.

<DetailedExplanation>
There is no limit on how many actions you can dispatch in a row.  However, each dispatched action does result in execution of all store subscription callbacks (typically one or more per Redux-connected UI component), and will usually result in UI updates.

While UI updates queued from React event handlers will usually be batched into a single React render pass, updates queued _outside_ of those event handlers are not. This includes dispatches from most `async` functions, timeout callbacks, and non-React code. In those situations, each dispatch will result in a complete synchronous React render pass before the dispatch is done, which will decrease performance.

In addition, multiple dispatches that are conceptually part of a larger "transaction"-style update sequence will result in intermediate states that might not be considered valid. For example, if actions `"UPDATE_A"`, `"UPDATE_B"`, and `"UPDATE_C"` are dispatched in a row, and some code is expecting all three of `a`, `b`, and `c` to be updated together, the state after the first two dispatches will effectively be incomplete because only one or two of them has been updated.

If multiple dispatches are truly necessary, consider batching the updates in some way. Depending on your use case, this may just be batching React's own renders (possibly using [`batch()` from React-Redux](https://react-redux.js.org/api/batch)), debouncing the store notification callbacks, or grouping many actions into a larger single dispatch that only results in one subscriber notification. See [the FAQ entry on "reducing store update events"](../faq/Performance.md#how-can-i-reduce-the-number-of-store-update-events) for additional examples and links to related addons.

</DetailedExplanation>

### Evaluate Where Each Piece of State Should Live

The ["Three Principles of Redux"](../understanding/thinking-in-redux/ThreePrinciples.md) says that "the state of your whole application is stored in a single tree". This phrasing has been over-interpreted. It does not mean that literally _every_ value in the entire app _must_ be kept in the Redux store. Instead, **there should be a single place to find all values that _you_ consider to be global and app-wide**. Values that are "local" should generally be kept in the nearest UI component instead.

Because of this, it is up to you as a developer to decide what state should actually live in the Redux store, and what should stay in component state. **[Use these rules of thumb to help evaluate each piece of state and decide where it should live](../faq/OrganizingState.md#do-i-have-to-put-all-my-state-into-redux-should-i-ever-use-reacts-setstate)**.

### Use the React-Redux Hooks API

**Prefer using [the React-Redux hooks API (`useSelector` and `useDispatch`)](https://react-redux.js.org/api/hooks) as the default way to interact with a Redux store from your React components**. While the classic `connect` API still works fine and will continue to be supported, the hooks API is generally easier to use in several ways. The hooks have less indirection, less code to write, and are simpler to use with TypeScript than `connect` is.

The hooks API does introduce some different tradeoffs than `connect` does in terms of performance and data flow, but we now recommend them as the default.

<DetailedExplanation>

The [classic `connect` API](https://react-redux.js.org/api/connect) is a [Higher Order Component](https://reactjs.org/docs/higher-order-components.html). It generates a new wrapper component that subscribes to the store, renders your own component, and passes down data from the store and action creators as props.

This is a deliberate level of indirection, and allows you to write "presentational"-style components that receive all their values as props, without being specifically dependent on Redux.

The introduction of hooks has changed how most React developers write their components. While the "container/presentational" concept is still valid, hooks push you to write components that are responsible for requesting their own data internally by calling an appropriate hook. This leads to different approaches in how we write and test components and logic.

The indirection of `connect` has always made it a bit difficult for some users to follow the data flow. In addition, `connect`'s complexity has made it very difficult to type correctly with TypeScript, due to the multiple overloads, optional parameters, merging of props from `mapState` / `mapDispatch` / parent component, and binding of action creators and thunks.

`useSelector` and `useDispatch` eliminate the indirection, so it's much more clear how your own component is interacting with Redux. Since `useSelector` just accepts a single selector, it's much easier to define with TypeScript, and the same goes for `useDispatch`.

For more details, see Redux maintainer Mark Erikson's post and conference talk on the tradeoffs between hooks and HOCs:

- [Thoughts on React Hooks, Redux, and Separation of Concerns](https://blog.isquaredsoftware.com/2019/07/blogged-answers-thoughts-on-hooks/)
- [ReactBoston 2019: Hooks, HOCs, and Tradeoffs](https://blog.isquaredsoftware.com/2019/09/presentation-hooks-hocs-tradeoffs/)

Also see the [React-Redux hooks API docs](https://react-redux.js.org/api/hooks) for info on how to correctly optimize components and handle rare edge cases.

</DetailedExplanation>

### Connect More Components to Read Data from the Store

Prefer having more UI components subscribed to the Redux store and reading data at a more granular level. This typically leads to better UI performance, as fewer components will need to render when a given piece of state changes.

For example, rather than just connecting a `<UserList>` component and reading the entire array of users, have `<UserList>` retrieve a list of all user IDs, render list items as `<UserListItem userId={userId}>`, and have `<UserListItem>` be connected and extract its own user entry from the store.

This applies for both the React-Redux `connect()` API and the `useSelector()` hook.

### Use the Object Shorthand Form of `mapDispatch` with `connect`

The `mapDispatch` argument to `connect` can be defined as either a function that receives `dispatch` as an argument, or an object containing action creators. **We recommend always using [the "object shorthand" form of `mapDispatch`](https://react-redux.js.org/using-react-redux/connect-mapdispatch#defining-mapdispatchtoprops-as-an-object)**, as it simplifies the code considerably. There is almost never a real need to write `mapDispatch` as a function.

### Call `useSelector` Multiple Times in Function Components

**When retrieving data using the `useSelector` hook, prefer calling `useSelector` many times and retrieving smaller amounts of data, instead of having a single larger `useSelector` call that returns multiple results in an object**. Unlike `mapState`, `useSelector` is not required to return an object, and having selectors read smaller values means it is less likely that a given state change will cause this component to render.

However, try to find an appropriate balance of granularity. If a single component does need all fields in a slice of the state , just write one `useSelector` that returns that whole slice instead of separate selectors for each individual field.

### Use Static Typing

**Use a static type system like TypeScript or Flow rather than plain JavaScript**. The type systems will catch many common mistakes, improve the documentation of your code, and ultimately lead to better long-term maintainability. While Redux and React-Redux were originally designed with plain JS in mind, both work well with TS and Flow. Redux Toolkit is specifically written in TS and is designed to provide good type safety with a minimal amount of additional type declarations.

### Use the Redux DevTools Extension for Debugging

**Configure your Redux store to enable [debugging with the Redux DevTools Extension](https://github.com/zalmoxisus/redux-devtools-extension)**. It allows you to view:

- The history log of dispatched actions
- The contents of each action
- The final state after an action was dispatched
- The diff in the state after an action
- The [function stack trace showing the code where the action was actually dispatched](https://github.com/zalmoxisus/redux-devtools-extension/blob/master/docs/Features/Trace.md)

In addition, the DevTools allows you to do "time-travel debugging", stepping back and forth in the action history to see the entire app state and UI at different points in time.

**Redux was specifically designed to enable this kind of debugging, and the DevTools are one of the most powerful reasons to use Redux**.

### Use Plain JavaScript Objects for State

Prefer using plain JavaScript objects and arrays for your state tree, rather than specialized libraries like Immutable.js. While there are some potential benefits to using Immutable.js, most of the commonly stated goals such as easy reference comparisons are a property of immutable updates in general, and do not require a specific library. This also keeps bundle sizes smaller and reduces complexity from data type conversions.

As mentioned above, we specifically recommend using Immer if you want to simplify immutable update logic, specifically as part of Redux Toolkit.

<DetailedExplanation>
Immutable.js has been semi-frequently used in Redux apps since the beginning.  There are several common reasons stated for using Immutable.js:

- Performance improvements from cheap reference comparisons
- Performance improvements from making updates thanks to specialized data structures
- Prevention of accidental mutations
- Easier nested updates via APIs like `setIn()`

There are some valid aspects to those reasons, but in practice, the benefits aren't as good as stated, and there's multiple negatives to using it:

- Cheap reference comparisons are a property of any immutable updates, not just Immutable.js
- Accidental mutations can be prevented via other mechanisms, such as using Immer (which eliminates accident-prone manual copying logic, and deep-freezes state in development by default) or `redux-immutable-state-invariant` (which checks state for mutations)
- Immer allows simpler update logic overall, eliminating the need for `setIn()`
- Immutable.js has a very large bundle size
- The API is fairly complex
- The API "infects" your application's code. All logic must know whether it's dealing with plain JS objects or Immutable objects
- Converting from Immutable objects to plain JS objects is relatively expensive, and always produces completely new deep object references
- Lack of ongoing maintenance to the library

The strongest remaining reason to use Immutable.js is fast updates of _very_ large objects (tens of thousands of keys). Most applications won't deal with objects that large.

Overall, Immutable.js adds too much overhead for too little practical benefit. Immer is a much better option.

</DetailedExplanation>

</div>

<div class="priority-rules priority-recommended">

## Priority C Rules: Recommended

### Write Action Types as `domain/eventName`

The original Redux docs and examples generally used a "SCREAMING_SNAKE_CASE" convention for defining action types, such as `"ADD_TODO"` and `"INCREMENT"`. This matches typical conventions in most programming languages for declaring constant values. The downside is that the uppercase strings can be hard to read.

Other communities have adopted other conventions, usually with some indication of the "feature" or "domain" the action is related to, and the specific action type. The NgRx community typically uses a pattern like `"[Domain] Action Type"`, such as `"[Login Page] Login"`. Other patterns like `"domain:action"` have been used as well.

Redux Toolkit's `createSlice` function currently generates action types that look like `"domain/action"`, such as `"todos/addTodo"`. Going forward, **we suggest using the `"domain/action"` convention for readability**.

### Write Actions Using the Flux Standard Action Convention

The original "Flux Architecture" documentation only specified that action objects should have a `type` field, and did not give any further guidance on what kinds of fields or naming conventions should be used for fields in actions. To provide consistency, Andrew Clark created a convention called ["Flux Standard Actions"](https://github.com/redux-utilities/flux-standard-action) early in Redux's development. Summarized, the FSA convention says that actions:

- Should always put their data into a `payload` field
- May have a `meta` field for additional info
- May have an `error` field to indicate the action represents a failure of some kind

Many libraries in the Redux ecosystem have adopted the FSA convention, and Redux Toolkit generates action creators that match the FSA format.

**Prefer using FSA-formatted actions for consistency**.

> **Note**: The FSA spec says that "error" actions should set `error: true`, and use the same action type as the "valid" form of the action. In practice, most developers write separate action types for the "success" and "error" cases. Either is acceptable.

### Use Action Creators

"Action creator" functions started with the original "Flux Architecture" approach. With Redux, action creators are not strictly required. Components and other logic can always call `dispatch({type: "some/action"})` with the action object written inline.

However, using action creators provides consistency, especially in cases where some kind of preparation or additional logic is needed to fill in the contents of the action (such as generating a unique ID).

**Prefer using action creators for dispatching any actions**. However, rather than writing action creators by hand, **we recommend using the `createSlice` function from Redux Toolkit, which will generate action creators and action types automatically**.

### Use Thunks for Async Logic

Redux was designed to be extensible, and the middleware API was specifically created to allow different forms of async logic to be plugged into the Redux store. That way, users wouldn't be forced to learn a specific library like RxJS if it wasn't appropriate for their needs.

This led to a wide variety of Redux async middleware addons being created, and that in turn has caused confusion and questions over which async middleware should be used.

**We recommend [using the Redux Thunk middleware by default](https://github.com/reduxjs/redux-thunk)**, as it is sufficient for most typical use cases (such as basic AJAX data fetching). In addition, use of the `async/await` syntax in thunks makes them easier to read.

If you have truly complex async workflows that involve things like cancelation, debouncing, running logic after a given action was dispatched, or "background-thread"-type behavior, then consider adding more powerful async middleware like Redux-Saga or Redux-Observable.

### Move Complex Logic Outside Components

We have traditionally suggested keeping as much logic as possible outside components. That was partly due to encouraging the "container/presentational" pattern, where many components simply accept data as props and display UI accordingly, but also because dealing with async logic in class component lifecycle methods can become difficult to maintain.

**We still encourage moving complex synchronous or async logic outside components, usually into thunks**. This is especially true if the logic needs to read from the store state.

However, **the use of React hooks does make it somewhat easier to manage logic like data fetching directly inside a component**, and this may replace the need for thunks in some cases.

### Use Selector Functions to Read from Store State

"Selector functions" are a powerful tool for encapsulating reading values from the Redux store state and deriving further data from those values. In addition, libraries like Reselect enable creating memoized selector functions that only recalculate results when the inputs have changed, which is an important aspect of optimizing performance.

**We strongly recommend using memoized selector functions for reading store state whenever possible**, and recommend creating those selectors with Reselect.

However, don't feel that you _must_ write selector functions for every field in your state. Find a reasonable balance for granularity, based on how often fields are accessed and updated, and how much actual benefit the selectors are providing in your application.

### Avoid Putting Form State In Redux

**Most form state shouldn't go in Redux**. In most use cases, the data is not truly global, is not being cached, and is not being used by multiple components at once. In addition, connecting forms to Redux often involves dispatching actions on every single change event, which causes performance overhead and provides no real benefit. (You probably don't need to time-travel backwards one character from `name: "Mark"` to `name: "Mar"`.)

Even if the data ultimately ends up in Redux, prefer keeping the form edits themselves in local component state, and only dispatching an action to update the Redux store once the user has completed the form.

There are use cases when keeping form state in Redux does actually make sense, such as WYSIWYG live previews of edited item attributes. But, in most cases, this isn't necessary.

</div>

</div>
