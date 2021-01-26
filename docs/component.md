## コンポーネントの分類について

### components/ 以下に配置するもの

いわゆる Presentation Component です。

- アプリ固有のコンポーネント
- components以下は並列的なディレクトリ構造となっている
- Stateオブジェクトではなくできるだけプリミティブな値に分解し `props` で受け取る
- ユーザーアクションのハンドラは定義せず、Containerから受け取る
- componentコンポーネントの中で完結するハンドラは定義して利用してよい
- コンテナ内で閉じるものは/containers以下に配置する
    - 詳しくは [Container](./container.md) を参照してください

### containers/ 以下に配置するもの

いわゆる Container Component です。

- components以下のコンポーネントを使ってページをレイアウトするコンポーネント
- UseCaseに依存し、ユーザーアクションのハンドラ定義を行う
- Stateオブジェクトを扱うことができるコンポーネント
- containerにおいてはReact Context経由で値を受け取ってよい

### その他

プロジェクトによらないすべてのアプリで共通して利用するコンポーネントは smile-next-uikit に定義する。

## 作り方

コンポーネントの実装_React編、コンポーネントの実装_スタイル編を参照。

