# Reactとは
Facebookが開発しているWebのユーザーインターフェースを開発するためのJavaScriptライブラリです。

詳しくはこちらなど -> https://app.codegrid.net/entry/react-1

双方向バインディングという、データが変更されたら自動的に内容が変更されるような使いやすいUIを簡単に開発できるライブラリの一つです。データを一方向にして設計をシンプルにすることや、React NativeというWebページをアプリ化するためのライブラリなどもあり、Webサービスのフロントエンド開発で様々なところで採用されています。

気になる評価もあります -> https://www.infoq.com/jp/articles/no-more-mvc-frameworks

アプリケーションの設計手法のMVCがすでに古い、とあちこちで議論されていますが、基本的な考え方は知っておいて損はありません。それとReactの関係など -> http://postd.cc/is-mvc-dead-for-the-frontend/

ゲーム開発でも、状態の変化と出力をどのように連携させるかは重要なテーマです。Reactはデータを変更するとすぐに結果が変わるなどの面白い動きを見れるので体験しておきましょう。まずは公式のトップページ( https://reactjs.org/ )を見ていきます。

# シンプルなコンポーネント(A Simple Component)
Reactコンポーネントは表示する内容を返す`render()`メソッドを実装します。以下の例は<b>JSX</b>というXMLに似た構文を利用しています。ReactDOM.render()で渡したデータは、Reactコンポーネントでは`this.props`でアクセスできます。

```javascript
class HelloMessage extends React.Component {
  render() {
    return (
      <div>
        Hello {this.props.name}
      </div>
    );
  }
}
```

上記の部分がReactコンポーネントの定義です。`render()`メソッドの戻り値として、表示させたいWebページのデータを`return`で返しています。戻り値はJSXで指定しています。HTMLに似ていますが別のもので、属性の書き方が異なったり、 `{` `}` で囲んだ部分にプロパティを設定することで、動的にページを生成することができます。

```javascript
ReactDOM.render(
  <HelloMessage name="Taylor" />,
  mountNode
);
```

Reactの開始点です。`HelloMessage`タグで、先に定義したReact.Componentを継承したHelloMessageクラスを指定しています。`name`属性の値は、HelloMessageクラスの戻り値内で`{this.props.name}`でアクセスできます。結果として`Hello Taylor`と表示されます。`name`属性の値を変更すると出力も自動的に変わります。試して見てください。

`mountNode`は、`HelloMessage`タグを出力する先のHTMLページのノードです。HTMLページ全体ではなく、指定する箇所にReactを出力することができます。

# 状態をコンポーネント(A Stateful Component)
入力データはthis.propsに加えて、コンポーネントが管理できる内部の状態データが使えます。`this.state`でアクセスできます。コンポーネントの状態データが変更されると、自動的にrender()が再実行されて画面が更新されます。

```javascript
class Timer extends React.Component {
```

Timerという名前でReactコンポーネントを宣言します。

```javascript
  constructor(props) {
    super(props);
    this.state = { seconds: 0 };
  }
```

クラスを生成する時に実行されるconstructorの定義です。プロパティーを`props`引数で受け取って、親クラスのコンストラクターに渡しています。また、Timerコンポーネントが管理する状態データとして`seconds`を初期値`0`で宣言しています。`this.state`にオブジェクトを保存すると、状態として保存され、これが更新されるとRender()が自動的に再構築されます。

```javascript
  tick() {
    this.setState(prevState => ({
      seconds: prevState.seconds + 1
    }));
  }
```

ユーザーメソッドです。`this.setState()`は、現在の状態を変更する際に利用するメソッドです。`this.state`を変更する場合は、`this.state`を直接変更することはせず、このような手順を踏みます。これも設計思想の一つです。

`prevState => ({seconds: prevState.seconds+1})` は[アロー関数](https://developer.mozilla.org/ja/docs/Web/JavaScript/Reference/arrow_functions)などと呼ばれるものです。 `=>` は[ラムダ演算子](https://docs.microsoft.com/ja-jp/dotnet/csharp/language-reference/operators/lambda-operator)などと呼ばれます。この書式により名前のない以下のような関数を表しています。

```javascript
無名(prefState) {
    return {
        seconds: prefState.seconds+1
    };
}
```

`this.setState()`は、新しく設定したい状態を返す関数か、オブジェクトを引数に渡す仕様になっていて、ここでは関数で次の状態を返させています。引数に、現在の状態を渡してくれるので、それを`prefState`で受け取って、`seconds`を1増やしているのです。

```javascript
  componentDidMount() {
    this.interval = setInterval(() => this.tick(), 1000);
  }
```

`componentDidMount()` は、コンポーネントの準備が完了した時にReactが自動的に呼び出す関数で、1秒ごとに先ほど宣言した`tick()`メソッドを呼び出すように`setInterval()`で設定しています。

```javascript
  componentWillUnmount() {
    clearInterval(this.interval);
  }
```

`componentWillUnmount()`は、コンポーネントが破棄される直前にReactが自動的に呼び出す関数で、ここでは設定したタイマーを停止させる処理をしています。

```javascript
  render() {
    return (
      <div>
        Seconds: {this.state.seconds}
      </div>
    );
  }
}
```

先の例にも登場した`render()`メソッドです。このReactコンポネントを組み込んだ場所に、「Seconds: 」と表示した後ろに`this.state.seconds`に記録されている値を出力しています。

```javascript
ReactDOM.render(<Timer />, mountNode);
```

Reactのエントリーポイントです。`mountNode`で指定するHTMLの要素に、ReactコンポーネントTimerを描画するよう設定しています。

以上で、1秒ごとに数値が書き換わるようになります。この構造の中に、<b>再描画を明示的に書いているコードがない</b>ことに注目してください。やっていることは、1秒ごとに`this.setState()`を呼び出して、状態`seconds`の値を1増やしているだけです。状態の変更が発生すると、それをReactが把握して、自動的にrender()を呼び出し直して、画面を再描画しているのです。

# アプリケーション(An Application)
<b>props</b>と<b>state</b>を使うことで、データベースやファイルを使わなくても、簡単なTODOアプリを作ることができます。この例では、stateを使って、ユーザーが入力したテキストと、TODOのリストを管理します。テキストボックスの変更や、ボタンが押されたなどのイベントは、個別のコンポーネントから上位のコンポーネントに伝播されます。

```javascript
class TodoApp extends React.Component {
  constructor(props) {
    super(props);
    this.state = { items: [], text: '' };
    this.handleChange = this.handleChange.bind(this);
    this.handleSubmit = this.handleSubmit.bind(this);
  }
```

TodoAppというReactコンポーネントを定義して、コンストラクターで `items` と `text` の2つの状態を宣言しています。

`handleChange` と `handleSubmit` への代入は、コンストラクターのインスタンスをそれぞれメソッドの`this`に割り当てるものです。JavaScriptはインスタンスの扱いに癖があり、それに関するものです。

```javascript
  render() {
    return (
      <div>
        <h3>TODO</h3>
        <TodoList items={this.state.items} />
        <form onSubmit={this.handleSubmit}>
          <input
            onChange={this.handleChange}
            value={this.state.text}
          />
          <button>
            Add #{this.state.items.length + 1}
          </button>
        </form>
      </div>
    );
  }
```

ページを描画するための`render()`関数です。以下、重要な部分です。

- ToDoListというもう一つのReactコンポーネントに`items`状態を渡しています
- フォームでAddボタンを押した時に`handleSubmit`メソッドを呼び出すように設定しています
- テキストボックスが変更されたとき( `onChange` )に、`handleChange`メソッドを呼び出すように設定しています
- テキストボックスに表示する値は、`text`の状態です
- ボタンには`items`の配列数を「Add #」の後ろに表示します

以上、コードと見比べてみてください。

```javascript
  handleChange(e) {
    this.setState({ text: e.target.value });
  }
```

テキストボックスが変更された時に実行するメソッド。変更点が`e`に渡されて、`e.target.value`で中身を確認できるので、`text`の状態を現在のテキストボックスの中身にします。状態がこれで変わるので、自動的にrender()が再描画されてテキストボックスの表示と状態が一致することになります。Reactで重要な動作の一つです。

```javascript
  handleSubmit(e) {
    e.preventDefault();
    if (!this.state.text.length) {
      return;
    }
    const newItem = {
      text: this.state.text,
      id: Date.now()
    };
    this.setState(prevState => ({
      items: prevState.items.concat(newItem),
      text: ''
    }));
  }
}
```

Addボタンが押された時の処理です。以下のような処理をしています。

- ボタンが押された時のデフォルトの動作をキャンセルします(`e.preventDefault()`)
- テキストボックスが空だった(長さが0)ら何もしない
- `newItem`に新しく追加するデータをオブジェクトにして代入。重複しないように、表示はしていないが`id`に現在時間を設定しています
- 先の例と同様に、無名関数で新しい`items`の状態と`text`を空欄にするためのオブジェクトを返しています

```javascript
class TodoList extends React.Component {
  render() {
    return (
      <ul>
        {this.props.items.map(item => (
          <li key={item.id}>{item.text}</li>
        ))}
      </ul>
    );
  }
}
```

2つ目のReactコンポーネントです。`this.props.items`とすれば、TodoAppコンポーネントが渡してくる`items`を参照できます。

`map`はfor文に代わる機能で、`配列.map( 関数(データ) )`とすると、配列の要素をデータに渡しながら、関数を配列の数だけ呼び出してくれるものです。これにより、`items`の全ての要素について、`key`属性に`items.id`を指定して、`item.text`を表示することになります。

```javascript
ReactDOM.render(<TodoApp />, mountNode);
```

`mountNode`要素にTodoAppコンポーネントを出力するように最後に指定しています。

# コンポーネントで外部プラグインを利用する(A Component Using External Plugins)
Reactを他のライブラリーやフレームワークと組み合わせる例です。[remarkable](https://github.com/jonschlinkert/remarkable)というライブラリを使って、テキストエリアの内容をリアルタイムに装飾します。

ポイントは`getRawMarkup()`メソッドです。この中でRemarkable()のインスタンスを生成して、現在の状態の`value`をレンダリングして、`__html`属性として返しています。

これを、`render()`内の`dangerouslySetInnerHTML`に渡しています。この属性はReactが定義しているもので、代入された文字列をそのままブラウザーに出力するものです。この処理は、タグを解釈することからセキュリティー的に危険な行為なため、`dangerously`という名前になっています。

`defaultValue`属性を指定すると、その要素の初期値を設定できます。

それ以外は、ここまで登場したものですので、意味を読み取ってみてください。

# まとめ
Reactの例をいくつか見て、お約束などを確認しました。様々な処理が暗黙に行われるため、ルールを把握していないと理解するのが難しいのですが、設計は楽になりますし、実装するコード数も減ります。

ReactやPhaserといったWeb系の技術の強いところは、Webページ上で直接動かせる点にあります。Unityのように開発ツールをインストールしなくても、テキストエディターとWebブラウザーにインターネットがあれば動かすことができますし、GitHub Pagesで作ったものを動かすことができます。PC、スマホのどちらでも動きます。処理速度はどんどん高速化し、WebGLを使えばシェーダーも使えるので表現力も進化しています。

ReactはテキストエディターとWebブラウザーとインターネットがあれば動きますが、より楽に開発をして、コードを小さくするなどのツールやテスト環境をまとめたパッケージがあります。次は、 https://reactjs.org/tutorial/tutorial.html をもとに、Reactの開発環境を整えて、実際にアプリを作ってみましょう。

# 補足
## JSXについて
JSXはReactを使うのに必須ではなく、同様のことをJavaScriptで書くことができます。サンプルのJSXのチェックを外すか、[https://babeljs.io/repl/#?presets=react&code_lz=MYewdgzgLgBApgGzgWzmWBeGAeAFgRgD4AJRBEAGhgHcQAnBAEwEJsB6AwgbgChRJY_KAEMAlmDh0YWRiGABXVOgB0AczhQAokiVQAQgE8AkowAUPGDADkdECChWeASl4AlOMOBQAIgHkAssp0aIySpogoaFBUQmISdC48QA:title]で、JSXをJavaScriptに変換した例を確認できます。

# 参考URL
- [React公式ページ](https://reactjs.org/)
- [CodeGrid. 第1回 React.jsとは](https://app.codegrid.net/entry/react-1)
- [作者 Jean-Jacques Dubray , 翻訳者 徳武 聡. 私がMVCフレームワークをもはや使わない理由](https://www.infoq.com/jp/articles/no-more-mvc-frameworks)
- [Alex Moldovan. フロントエンドにおいてModel-View-Controllerは死んだのか？]()
- [jonschlinkert. remarkable](https://github.com/jonschlinkert/remarkable)
