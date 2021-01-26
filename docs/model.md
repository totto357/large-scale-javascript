# Model

## 原則

モデルは基本的にPlain Old TypeScript Object(POTO)で書く。  
ライブラリへの依存は必要最低限のものとする。  

InfraのAPIクライアントなどは外から受け取るようにし、ドメインがInfraに依存してはいけない。

現状モデルで依存しているライブラリは以下のもの。

- Immer: Immutableを書きやすくするため
- ddd-base: DDDにおけるEntityやValueObjectを書きやすくするため

## 役割

DDDでいうところのドメイン層。  
業務領域ごとに情報や機能がまとまっている。  
何か新機能が追加される場合や、既存の機能を変更する場合はここをいじることになる。  

## 作り方

### ドメインの分け方

すごい難しい。  
ライフサイクルや、複雑度合いなどを考慮してドメインを分ける。  
チームメンバーと相談し、都度判断する。  
基本的にはAPIで受け取るオブジェクトの形と1対1になるはず。  

### ディレクトリの切り方

コンテナごとに扱うモデルの形は異なるはずなので、コンテナ内で`/models`ディレクトリを作成し、
配下に並列でモデルを並べる。

## 振る舞い

モデルは振る舞いをもつ。  
データだけをもつドメインモデル貧血症にしない。  

## 例

協働学習のノートモデルの例。  

```ts
import { immerable, produce } from 'immer';

interface NoteProps {
    title: string;
    pages: Page[];
}

class Note {
    // Immerをクラスで利用する場合に必須
    // https://immerjs.github.io/immer/docs/complex-objects
    [immerable] = true;
    title: string;
    pages: Page[];

    constructor(props: NoteProps) {
        this.title = props.title;
        this.pages = props.pages;
    }

    // 自前でImmutableにする例
    addPage(page: Page) {
        return new Note({
            ...this,
            pages: this.pages.concat([page]);
        });
    }

    // ImmerでImmutableにする例
    addPage(page: Page) {
        return produce(this, (draft) => {
            draft.pages.push(page);
        });
    }
}
```
