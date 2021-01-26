# State

## 役割

Containerコンポーネントで扱う状態を保持する。  
Modelの情報を、Componentが利用しやすい形に変換する。  
UseCaseのイベントに対する処理をもつ。  

## 原則

- Modelが入れ子になっている場合などは、そのまま利用してもよい
- Component用に変換が必要な場合は、単純なオブジェクトや、変換用のクラスを用意してもよい

理由としては、変更がある度に新規インスタンスを返す仕組みとイベント周りのつなぎ込みが複雑になりそうなためと、Modelの情報からStateは作れるはずなため。

## 作り方

### 命名規則

```
名詞(コンテナ名) + State
```

container コンポーネントと1対1で作成するため、コンテナ名がそのまま入る。

### 配置場所

container コンポーネントの真横に配置する。  
例：`/containers/Hoge/Page/HogePageState.ts`

### Component を意識したプロパティの作成

たとえばComponentのHogeButtonの一部を非表示にしたい場合は次のようにする。

Containerに渡すHogePageStateへ`isHidden`のようなプロパティを追加する。

```ts
export default class HogePageState {
    isHidden = false;

    constructor(props: HogePageStateProps){
        this.isHidden = props.isHidden;
    }
}
```

そして、HogeButtonの親のcontainerで次のようにpropsとして渡す。

```tsx
<HogeButton hidden={state.isHidden}/>
```

HogeButtonComponent内では、hiddenの値を見てrenderの処理を行う。  
特別な理由がない場合は、メソッドを生やしてrender毎に動的に計算するよりかは、constructorで計算したものを定義しておいた方が負荷的に良さそう。  
(stateはuseReducerによってキャッシュされる)

### UseCase のイベント処理

UseCase から発せられるイベントは `reduce` メソッドで受けることができる。  
StateはImmutableなクラスとして作成するが、reduceに関してはmutabeなメソッドのように書くことができる。  
これは基底クラスの `State` で [Immer](https://immerjs.github.io/immer/docs/introduction) を使っているためである。

```ts
export class HogePageState extends State {
    hoges: Hoge[] = [];

    reduce(this: State<this>, payload: Payload) {
        if (payload instanceof FetchedHogePayload) {
            this.hoge = payload.hoge;
            return;
        }
    }
}
```

※ `reduce` 内のif文ではelse if を使ってもよいが、アーリーリターンをしたほうが見やすくなる。  
※ ネストしたモデルを利用する場合、State が新しくならない場合がある。その場合は、モデルも Immutable な設計にすると良い。
