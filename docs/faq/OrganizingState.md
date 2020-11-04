---
id: organizing-state
title: ステートの構成
hide_title: true
---

# Redux FAQ: ステートの構成

<!--
## Table of Contents

- [Redux FAQ: Organizing State](#redux-faq-organizing-state)
  - [Table of Contents](#table-of-contents)
  - [Organizing State](#organizing-state)
    - [Do I have to put all my state into Redux? Should I ever use React's `setState()`?](#do-i-have-to-put-all-my-state-into-redux-should-i-ever-use-reacts-setstate)
      - [Further information](#further-information)
    - [Can I put functions, promises, or other non-serializable items in my store state?](#can-i-put-functions-promises-or-other-non-serializable-items-in-my-store-state)
      - [Further information](#further-information-1)
    - [How do I organize nested or duplicate data in my state?](#how-do-i-organize-nested-or-duplicate-data-in-my-state)
      - [Further information](#further-information-2)
    - [Should I put form state or other UI state in my store?](#should-i-put-form-state-or-other-ui-state-in-my-store)
      - [Further Information](#further-information-3)
-->

## ステートの構成

### すべてのステートを Redux に入れる必要がありますか？ `setState()` を使ってもいいですか？

これに「正しい」回答はありません。ユーザーによっては、アプリケーションの完全にシリアル化され制御されたバージョンを常に維持するために、あらゆるデータを Redux に保持したいと考えています。「このドロップダウンは現在オープンか」などの重要でない UI のステートはコンポーネントの内部状態の中に保持することを好むユーザーもいます。

**ローカルのコンポーネントのステートを使用することに問題はありません**。開発者として、アプリケーションを構成するステートの種類、ステートの各パーツをどこに置くかを決定するのは、*あなた*の仕事です。自分に合ったバランスを見つけて、それに従ってください。

どのようなデータを Redux に入れるべきかを決定するための一般的な経験則がいくつかあります：

- アプリケーションの他の部分でこのデータを気にすることがあるか？
- この元データから派生的に別のデータを作成できる必要があるか？
- この同一データを、複数のコンポーネントを動かすために使うか？
- このステートをある特定の時点に戻せるということ（タイムトラベルデバッギング）に価値があるか？
- このデータをキャッシュする（つまり既にステート内にある場合は再リクエストせずにそちらを使う）必要があるか？
- UI コンポーネントのホットリローディングの際にもこのデータを保持しておきたいか？（コンポーネントが入れ替わると内部 state は失われる可能性がある）

コンポーネントごとのステートをローカルではなく Redux ストアに保管するための様々なアプローチを実装した、[redux-component](https://github.com/tomchentw/redux-component) や [redux-react-local](https://github.com/threepointone/redux-react-local) などのコミュニティパッケージが存在します。またローカルのコンポーネントステートの更新タスクに Redux リデューサの原理や概念を適用して `this.setState( (previousState) => reducer(previousState, someAction))` のようにすることも可能です。

#### 参考情報

**記事**

- [When (and when not) to reach for Redux](https://changelog.com/posts/when-and-when-not-to-reach-for-redux)
- [You Might Not Need Redux](https://medium.com/@dan_abramov/you-might-not-need-redux-be46360cf367)
- [Finding `state`'s place with React and Redux](https://medium.com/@adamrackis/finding-state-s-place-with-react-and-redux-e9a586630172)
- [A Case for setState](https://medium.com/@zackargyle/a-case-for-setstate-1f1c47cd3f73)
- [How to handle state in React: the missing FAQ](https://medium.com/react-ecosystem/how-to-handle-state-in-react-6f2d3cd73a0c)
- [Where to Hold React Component Data: state, store, static, and this](https://medium.freecodecamp.com/where-do-i-belong-a-guide-to-saving-react-component-data-in-state-store-static-and-this-c49b335e2a00)
- [The 5 Types of React Application State](http://jamesknelson.com/5-types-react-application-state/)
- [Shape Your Redux Store Like Your Database](https://hackernoon.com/shape-your-redux-store-like-your-database-98faa4754fd5)

**ディスカッション**

- [#159: Investigate using Redux for pseudo-local component state](https://github.com/reduxjs/redux/issues/159)
- [#1098: Using Redux in reusable React component](https://github.com/reduxjs/redux/issues/1098)
- [#1287: How to choose between Redux's store and React's state?](https://github.com/reduxjs/redux/issues/1287)
- [#1385: What are the disadvantages of storing all your state in a single immutable atom?](https://github.com/reduxjs/redux/issues/1385)
- [Twitter: Should I keep something in React component state?](https://twitter.com/dan_abramov/status/749710501916139520)
- [Twitter: Using a reducer to update a component](https://twitter.com/dan_abramov/status/736310245945933824)
- [React Forums: Redux and global state vs local state](https://discuss.reactjs.org/t/redux-and-global-state-vs-local-state/4187)
- [Reddit: "When should I put something into my Redux store?"](https://www.reddit.com/r/reactjs/comments/4w04to/when_using_redux_should_all_asynchronous_actions/d63u4o8)
- [Stack Overflow: Why is state all in one place, even state that isn't global?](http://stackoverflow.com/questions/35664594/redux-why-is-state-all-in-one-place-even-state-that-isnt-global)
- [Stack Overflow: Should all component state be kept in Redux store?](http://stackoverflow.com/questions/35328056/react-redux-should-all-component-states-be-kept-in-redux-store)

**ライブラリ**

- [Redux Addons Catalog: Component State](https://github.com/markerikson/redux-ecosystem-links/blob/master/component-state.md)

### ストアのステートに関数や Promise やその他シリアライズできない値を入れても構いませんか？

シリアライズ可能なオブジェクト、配列、プリミティブのみをストアに入れることを強くお勧めします。ストアにシリアライズ可能でないアイテムを挿入することは*技術的には*可能ですが、そうするとストアの内容を永続化・復帰したり、タイムトラベルデバッグを行ったりといった機能が壊れる原因になります。

永続化やタイムトラベルデバッグのようなものが意図した通り動作しない可能性があっても構わないのであれば、Redux ストアにシリアライズ可能ではないアイテムを入れるのは問題ありません。結局のところ、それは*あなたの*アプリケーションであり、どのように実装するかはあなた次第です。Redux に関する他の多くのことと同様に、どのようなトレードオフが関係しているのかを理解しておくようにしてください。

#### 参考情報

**ディスカッション**

- [#1248: Is it ok and possible to store a react component in a reducer?](https://github.com/reduxjs/redux/issues/1248)
- [#1279: Have any suggestions for where to put a Map Component in Flux?](https://github.com/reduxjs/redux/issues/1279)
- [#1390: Component Loading](https://github.com/reduxjs/redux/issues/1390)
- [#1407: Just sharing a great base class](https://github.com/reduxjs/redux/issues/1407)
- [#1793: React Elements in Redux State](https://github.com/reduxjs/redux/issues/1793)

### ステート内で重複していたりネストしていたりするデータをどう整理するのですか？

ID、入れ子、またはリレーションシップを持つようなデータは、一般的に「正規化された」方法で保存されるべきです。各オブジェクトは ID をキーにして一度だけ保存され、それを参照する他のオブジェクトは、オブジェクト全体のコピーではなく、ID のみを保存するようにします。ストアの一部をデータベースのようなものと考えて、アイテムタイプごとに個別の「テーブル」があると考えるといいかもしれません。[normalizr](https://github.com/paularmstrong/normalizr) や [redux-orm](https://github.com/tommikaikkonen/redux-orm) などのライブラリは、正規化されたデータを管理するための機能や抽象化を提供します。

#### Further information

**Documentation**

- [Redux Fundamentals: Async Logic and Data Flow](../tutorials/fundamentals/part-6-async-logic.md)
- - [Redux Fundamentals: Standard Redux Patterns](../tutorials/fundamentals/part-7-standard-patterns.md)
- [Examples: Real World example](../introduction/Examples.md#real-world)
- [Recipes: Structuring Reducers - Prerequisite Concepts](../recipes/structuring-reducers/PrerequisiteConcepts.md#normalizing-data)
- [Recipes: Structuring Reducers - Normalizing State Shape](../recipes/structuring-reducers/NormalizingStateShape.md)
- [Examples: Tree View](https://github.com/reduxjs/redux/tree/master/examples/tree-view)

**Articles**

- [High-Performance Redux](http://somebody32.github.io/high-performance-redux/)
- [Querying a Redux Store](https://medium.com/@adamrackis/querying-a-redux-store-37db8c7f3b0f)

**Discussions**

- [#316: How to create nested reducers?](https://github.com/reduxjs/redux/issues/316)
- [#815: Working with Data Structures](https://github.com/reduxjs/redux/issues/815)
- [#946: Best way to update related state fields with split reducers?](https://github.com/reduxjs/redux/issues/946)
- [#994: How to cut the boilerplate when updating nested entities?](https://github.com/reduxjs/redux/issues/994)
- [#1255: Normalizr usage with nested objects in React/Redux](https://github.com/reduxjs/redux/issues/1255)
- [#1269: Add tree view example](https://github.com/reduxjs/redux/pull/1269)
- [#1824: Normalising state and garbage collection](https://github.com/reduxjs/redux/issues/1824#issuecomment-228585904)
- [Twitter: state shape should be normalized](https://twitter.com/dan_abramov/status/715507260244496384)
- [Stack Overflow: How to handle tree-shaped entities in Redux reducers?](http://stackoverflow.com/questions/32798193/how-to-handle-tree-shaped-entities-in-redux-reducers)
- [Stack Overflow: How to optimize small updates to props of nested components in React + Redux?](http://stackoverflow.com/questions/37264415/how-to-optimize-small-updates-to-props-of-nested-component-in-react-redux)

### フォームの状態やその他の UI ステートをストアに入れるべきですか？

[Redux ストアに何を入れるかに関しての経験則](#do-i-have-to-put-all-my-state-into-redux-should-i-ever-use-reacts-setstate) はここでも当てはまります。

**これらの経験則に基づけば、ほとんどのフォームの状態は、コンポーネント間で共有されていないため、Redux に入る必要はありません**。しかし、この決定は常にあなたとあなたのアプリケーションに特有のものになります。元々ストアから来たデータを編集させたい、あるいは編集中の値がアプリケーションの他の場所のコンポーネントに反映される必要があるといった理由で、フォーム状態の一部を Redux に保存することにするかもしれません。一方で、フォームの状態をコンポーネントにローカルに保持しておき、ユーザーがフォーム操作を終えた後にそのデータをストアに格納するアクションをディスパッチする方が、ずっとシンプルかもしれません。

というわけで、大抵の場合は Redux ベースのフォーム管理ライブラリも必要ないでしょう。以下のアプローチを以下の順番で試みることをお勧めします。

- データが Redux ストアから来ている場合でも、まずはフォームロジックの手書きから始めて下さい。大抵の場合はこれが必要なすべてです。（[**Gosha Arinich による React でのフォームの扱い方に関する記事**](https://goshakkk.name/on-forms-react/)にこれについての素晴らしいガイダンスがあります）
- 手書きでフォームを書くのが難しすぎると分かった場合、[Formik](https://github.com/jaredpalmer/formik) や [React-Final-Form](https://github.com/final-form/react-final-form) のような React ベースのフォームライブラリを試してみてください.
- 他のアプローチではうまくいかないので Redux ベースのフォームライブラリが絶対に必要だという確証がある場合は、最終手段として [Redux-Form](https://github.com/erikras/redux-form) や [React-Redux-Form](https://github.com/davidkpiano/react-redux-form) のようなものを見てみてください。

Redux でフォームの状態を保持する場合、パフォーマンス特性について時間をかけて考慮するべきです。テキスト入力のすべてのキーストロークに対してアクションをディスパッチすることにはおそらく価値がないでしょうし、[ディスパッチ前にキーストロークをローカルにバッファリングする方法](https://blog.isquaredsoftware.com/2017/01/practical-redux-part-7-forms-editing-reducers/)についても調べてみてください。いつものように、あなた自身のアプリケーションの全体的なパフォーマンス要求を分析するために時間をかけてください。

他の種類の UI ステートも、上記の経験則に従います。典型的な例は `isDropdownOpen` フラグの管理です。大抵はアプリの他の部分はこれを気にしないので、ほとんどの場合はコンポーネントのステートに留めておくべきです。しかし、アプリケーションによっては、[ダイアログやその他のポップアップ](https://blog.isquaredsoftware.com/2017/07/practical-redux-part-10-managing-modals/)、タブ、開閉可能なパネルなどの状態管理に Redux を使用することが理にかなっているかもしれません。

#### 参考情報

**記事**

- [Gosha Arinich: Writings on Forms in React](https://goshakkk.name/on-forms-react/)
- [Practical Redux, Part 6: Connected Lists and Forms](https://blog.isquaredsoftware.com/2017/01/practical-redux-part-6-connected-lists-forms-and-performance/)
- [Practical Redux, Part 7: Form Change Handling](https://blog.isquaredsoftware.com/2017/01/practical-redux-part-7-forms-editing-reducers/)
- [Practical Redux, Part 10: Managing Modals and Context Menus](https://blog.isquaredsoftware.com/2017/07/practical-redux-part-10-managing-modals/)
- [React/Redux Links: Redux UI Management](https://github.com/markerikson/react-redux-links/blob/master/redux-ui-management.md)
