# UseCase

## 役割

ComponentとModelをつなぐ。

### Fluxパターンの場合

- 新規にModelを作成し、Stateに向けてイベントを発行する
- 受け取ったModelと内容を用いて、モデルに変更を加え、Stateに向けてイベントを発行する

その際に必要な情報をInfraのAPIを使い外部と通信を行い取得したりする。

### CQRSパターンの場合

- 新規にModelを作成し、Repositoryに保存
- Repositoryから既存のModelを取りだす
- Modelに変更を加えて、Repository保存

その際に必要な情報をInfraのAPIを使い外部と通信を行い取得したりする。

## 原則

1UseCase1ファイル。

UseCaseは入れ子|拡張とすることもできるが、できるかぎりUseCaseの再利用は避ける。
モデルが正しく振る舞いを持っていれば、UseCaseの再利用をするケースは少なくなる。

### UseCaseを再利用する場合

例) OuterUseCase#executeがInnerUseCaseを呼びだす。

`InnerUseCase#execute()` の返り値(resolvedされた値)を使うOuterUseCaseは許容しない。
execute関数では、非同期ならPromiseを返す、そうでないなら何も返さない。
その返した値を利用することはできるだけ避ける。(then/catchの成功/失敗だけを見る)

## 作り方

### 命名規則

```
動詞 + 名詞(対象) + UseCase
```

命名例は次のとおり。

```
ChangeTitleUseCase
EnterFullScreenUseCase
```

UseCaseはDomainの流れを意識した命名にする。
UIを意識したClickButtonUseCaseといった名前は付けない。

### ディレクトリの切り方

基本的にはコンテナ内で閉じるものなので、
モデルと同様にコンテナ内で`/usecases`ディレクトリを作成し、
配下に並列でユースケースを並べる。

### dispatch 関数

execute内で、this.dispatchを叩くことで、UseCaseからイベント(Payload)を発することができる。  
State#reduceを実装することで、UseCaseからのイベントとのつなぎ込みを行う。  

### 作成ステップ

1. 対象ディレクトリがない場合は作成
1. 対象ディレクトリ下に `HogeFugaUseCase.js` を配置
1. constructorで依存するAPIなどを受け取る
1. executeで実行に必要な値を受け取る
1. execute内部で通信が発生する場合は、そのPromiseを返すようにする
1. Stateへ直接イベントを送りたい場合はthis.dispatchを利用する

# Payload

dispatch関数の引数に渡すイベントオブジェクト。  

## 命名規則

Payloadはイベントで起きたことを表すものなので、過去形の動詞を基本とする。

```
動詞(過去形) + 名詞(対象) + Payload
```

命名例は次のとおり。

```
ChangedTitlePayload
EnteredFullScreenPayload
```

type属性にはPayload名と同じものを定義する。

## 属性

イベントなので好きな属性を設定することができるが、渡されたものに手を加えてはならない。  
渡されたものを保持しておくだけの箱として利用する。  
(コンストラクタで初期化などはNG)
