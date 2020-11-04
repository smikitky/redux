---
id: store-setup
title: ストアのセットアップ
hide_title: true
---

# Redux FAQ: ストアのセットアップ

<!--
## Table of Contents

- [Redux FAQ: Store Setup](#redux-faq-store-setup)
  - [Table of Contents](#table-of-contents)
  - [Store Setup](#store-setup)
    - [Can or should I create multiple stores? Can I import my store directly, and use it in components myself?](#can-or-should-i-create-multiple-stores-can-i-import-my-store-directly-and-use-it-in-components-myself)
      - [Further information](#further-information)
    - [Is it OK to have more than one middleware chain in my store enhancer? What is the difference between `next` and `dispatch` in a middleware function?](#is-it-ok-to-have-more-than-one-middleware-chain-in-my-store-enhancer-what-is-the-difference-between-next-and-dispatch-in-a-middleware-function)
      - [Further information](#further-information-1)
    - [How do I subscribe to only a portion of the state? Can I get the dispatched action as part of the subscription?](#how-do-i-subscribe-to-only-a-portion-of-the-state-can-i-get-the-dispatched-action-as-part-of-the-subscription)
      - [Further information](#further-information-2)
-->

## ストアのセットアップ

### 複数のストアを作成できる、あるいはそうすべきですか？ ストアを直接インポートしてコンポーネント内で使っても構いませんか？

オリジナルの Flux パターンには、アプリ内に複数の「ストア」を持ち、それぞれが異なる領域のデータを保持するというパターンの説明があります。これはあるストアが更新のために別のストアを "`waitFor`" しなければならないなどの問題が発生する可能性を生じます。Redux では、データドメイン間の分離は、単一のリデューサをより小さなリデューサに分割することですでに達成されているため、このようなものは必要ありません。

他のいくつかの質問と同様、ページ内に複数の異なる Redux ストアを作成すること自体は*可能です*が、意図されているのは、単一のストアのみを持つというパターンです。単一のストアのみを持つことで、Redux DevTools の使用が可能になり、データの永続化と復旧がより簡単になり、購読に関するロジックが簡素化されます。

Redux で複数のストアを使ってもよい理由としては以下のようなものがあります：

- アプリのプロファイリングで、ステートの一部があまりに頻繁に更新されるためパフォーマンスの問題が生じていると確認できたとき
- Redux アプリをより大きなアプリケーションの中の部品として分離しておきたいとき。その場合はルートコンポーネントごとにストアを作成したくなるかもしれません。

しかしながら、特にあなたが Flux の背景をお持ちの場合、新しいストアを作るということを直感的な第一選択としてはなりません。リデューサのコンポジションを最初に試し、それが問題解決にならない場合にのみストアを複数使うようにしてください。

同様に、ストアインスタンスを直接インポートして参照すること自体は*可能です*が、Redux では推奨されていないパターンです。ストアインスタンスを作成してモジュールからエクスポートすると、それはシングルトンになってしまいます。これでは Redux アプリをより大きなアプリの部品として分離することが難しくなります（そのようなことが必要だとして）。またサーバーレンダリングではでリクエストごとに別々のストアインスタンスを作成する必要があるため、それを行うことも難しくなります。

[React Redux](https://github.com/reduxjs/react-redux) では、`connect()` により作成されるラッパクラスは実際に `props.store` があるかを見ていますが、ベストなのはルートコンポーネントを `<Provider store={store}>` で囲んで、ストアを受け渡す部分の処理を React に任せることです。これによりコンポーネントはストアモジュールをインポートする心配をしなくてよくなり、後で Redux アプリを分離したりサーバレンダリングをしたくなった時にもずっと簡単にやれるようになります。

#### 参考情報

**ドキュメント内**

- [API: Store](../api/Store.md)

**ディスカッション**

- [#1346: Is it bad practice to just have a 'stores' directory?](https://github.com/reduxjs/redux/issues/1436)
- [Stack Overflow: Redux multiple stores, why not?](http://stackoverflow.com/questions/33619775/redux-multiple-stores-why-not)
- [Stack Overflow: Accessing Redux state in an action creator](http://stackoverflow.com/questions/35667249/accessing-redux-state-in-an-action-creator)
- [Gist: Breaking out of Redux paradigm to isolate apps](https://gist.github.com/gaearon/eeee2f619620ab7b55673a4ee2bf8400)

### ストアエンハンサにミドルウェアチェインが複数あってもいいですか？ ミドルウェア関数の `next` と `dispatch` の違いは何ですか？

Redux のミドルウェアはリンクリストのように動作します。各ミドルウェア関数は `next(action)` を呼び出してリスト内の次のミドルウェアにアクションを渡すか、`dispatch(action)` を呼び出してリストの先頭から処理をやり直させるか、あるいは何もしなければアクションの処理をそこで止めることができます。

このミドルウェアのチェインは、ストアを作成する際に使用される `applyMiddleware` 関数に渡される引数によって定義されます。複数のチェインを定義すると、別々の `dispatch` への参照を持つことになり各チェインのリンクが途切れてしまうため、うまく動かなくなります。

#### 参考情報

**ドキュメント内**

- [Redux Fundamentals: Store - Middleware](../tutorials/fundamentals/part-4-store.md#middleware)
- [API: applyMiddleware](../api/applyMiddleware.md)

**ディスカッション**

- [#1051: Shortcomings of the current applyMiddleware and composing createStore](https://github.com/reduxjs/redux/issues/1051)
- [Understanding Redux Middleware](https://medium.com/@meagle/understanding-87566abcfb7a)
- [Exploring Redux Middleware](http://blog.krawaller.se/posts/exploring-redux-middleware/)

### ステートの一部だけを購読するにはどうすればいいですか？ ディスパッチされたアクションが何なのかを購読することはできますか？

Redux は、ストアが更新されたことをリスナに通知するための単一の `store.subscribe` メソッドを提供します。リスナであるコールバックは現在のステートを引数として受け取ることはありません。単純に*何か*が変わったと分かるだけです。その後サブスクライバロジックは `getState()` を呼び出して現在のステートの値を取得します。

この API は、依存関係や複雑さを排した低レベルのプリミティブとして作られており、より高レベルのサブスクリプションロジックを構築するために使用することができます。React Redux のような UI バインディングは、接続されたコンポーネントごとにサブスクリプションを作成することができます。また、新旧のステートを賢く比較して、特定の部分が変更された場合に追加のロジックを実行する関数を書くことも可能です。例としては、[redux-watch](https://github.com/jprichardson/redux-watch)、[redux-subscribe](https://github.com/ashaffer/redux-subscribe)、[redux-subscriber](https://github.com/ivantsov/redux-subscriber)があり、これらはサブスクリプションの指定や変更の処理に異なるアプローチを提供しています。

新ステートがリスナに渡されないのは、Redux DevTools のようなストアエンハンサの実装を簡単にするためです。加えて、購読する側はアクションではなくステートの値そのものに応答するべきです。アクション自体が重要であり特定の処理が必要だという場合は、ミドルウェアを使うことができます。

#### 参考情報

**ドキュメント内**

- [Fundamentals: Store](../tutorials/fundamentals/part-4-store.md)
- [API: Store](../api/Store.md)

**ディスカッション**

- [#303: subscribe API with state as an argument](https://github.com/reduxjs/redux/issues/303)
- [#580: Is it possible to get action and state in store.subscribe?](https://github.com/reduxjs/redux/issues/580)
- [#922: Proposal: add subscribe to middleware API](https://github.com/reduxjs/redux/issues/922)
- [#1057: subscribe listener can get action param?](https://github.com/reduxjs/redux/issues/1057)
- [#1300: Redux is great but major feature is missing](https://github.com/reduxjs/redux/issues/1300)

**ライブラリ**

- [Redux Addons Catalog: Store Change Subscriptions](https://github.com/markerikson/redux-ecosystem-links/blob/master/store.md#store-change-subscriptions)
