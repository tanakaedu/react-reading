# Reactとは
Facebookが開発しているWebのユーザーインターフェースを開発するためのJavaScriptライブラリです。

詳しくはこちらなど -> [https://app.codegrid.net/entry/react-1:title]

双方向バインディングという、データが変更されたら自動的に内容が変更されるような使いやすいUIを簡単に開発できるライブラリの一つです。データを一方向にして設計をシンプルにすることや、React NativeというWebページをアプリ化するためのライブラリなどもあり、Webサービスのフロントエンド開発で様々なところで採用されています。

気になる評価もあります -> [https://www.infoq.com/jp/articles/no-more-mvc-frameworks:title]

アプリケーションの設計手法のMVCがすでに古い、とあちこちで議論されていますが、基本的な考え方は知っておいて損はありません。それとReactの関係など -> [http://postd.cc/is-mvc-dead-for-the-frontend/:title]

ゲーム開発でも、状態の変化と出力をどのように連携させるかは重要なテーマです。Reactはデータを変更するとすぐに結果が変わるなどの面白い動きを見れるので体験しておきましょう。まずは公式のトップページ( [https://reactjs.org/:title] )を見ていきます。

# 目次
[:contents]

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

上記の部分がReactコンポーネントの定義です。`render()`メソッドの戻り値として、表示させたいWebページのデータを`return`で返しています。戻り値はJSXで指定しています。HTMLに似ていますが別のもので、属性の書き方が異なったり、`{``}`で囲んだ部分にプロパティを設定することで、動的にページを生成することができます。

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

例の概要です。

- ReactコンポーネントTimerを作成
- クラスを生成する時に実行されるconstructorメソッド
  - super()メソッドにpropsを渡して、React.Componentの親クラスのコンストラクターを呼び出し
  - Reactコンポーネントでは、`this.state`に設定したプロパティーが状態データとして扱われる。ここでは`seconds`プロパティーを定義して、0で初期化している
- tick()メソッドはユーザーメソッド


```javascript
class Timer extends React.Component {
  constructor(props) {
    super(props);
    this.state = { seconds: 0 };
  }

  tick() {
    this.setState(prevState => ({
      seconds: prevState.seconds + 1
    }));
  }

  componentDidMount() {
    this.interval = setInterval(() => this.tick(), 1000);
  }

  componentWillUnmount() {
    clearInterval(this.interval);
  }

  render() {
    return (
      <div>
        Seconds: {this.state.seconds}
      </div>
    );
  }
}

ReactDOM.render(<Timer />, mountNode);
```


# 補足
## JSXについて
JSXはReactを使うのに必須ではなく、同様のことをJavaScriptで書くことができます。サンプルのJSXのチェックを外すか、[https://babeljs.io/repl/#?presets=react&code_lz=MYewdgzgLgBApgGzgWzmWBeGAeAFgRgD4AJRBEAGhgHcQAnBAEwEJsB6AwgbgChRJY_KAEMAlmDh0YWRiGABXVOgB0AczhQAokiVQAQgE8AkowAUPGDADkdECChWeASl4AlOMOBQAIgHkAssp0aIySpogoaFBUQmISdC48QA:title]で、JSXをJavaScriptに変換した例を確認できます。






