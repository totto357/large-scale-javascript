## 役割

- components以下のコンポーネントを使ってページをレイアウトするコンポーネント
- UseCaseに依存し、ユーザーアクションのハンドラ定義を行う
- Stateオブジェクトを扱うことができるコンポーネント
- containerにおいてはReact Context経由で値を受け取ってよい

## ディレクトリ構成

```
/src
    /containers
        /コンテナ名
            /コンポーネントの種類名
                コンポーネント.tsx
                ステート.ts
                /components
                /infra/api
                /models
                ...
            /components
            /infra/api
            /models
            ...
```

### コンテナ名

第1階層は、コンテナ名とする。  
基本的にはページ単位となるので、「ノート一覧(NoteList)」や「ページ一覧(PageList)」となる。  

### コンポーネントの種類名

第2階層は、コンポーネントの種類名とする。  
これは、コンテナにおけるコンポーネントの役割で、ページ(Page)やダイアログ(Dialog)などが該当する。  
複数のコンテナコンポーネントが存在する場合は、並列に配置する。  

### コンポーネント.tsx

コンテナコンポーネントそのもの。  
命名としては、`{コンテナ名 + コンポーネントの種類名}.tsx`とする。  

### ステート.ts

コンテナコンポーネントで扱うステートクラス。
命名としては、`{コンテナ名 + コンポーネントの種類名 + State}.ts`とする。  

### /components

コンテナ内部で扱う Presentation Component を集約するディレクトリ。  
同一コンテナの複数のコンテナコンポーネントから呼ばれる場合は、コンテナ名直下に配置する。  
単一のコンテナコンポーネントからのみ呼ばれる場合は、コンポーネントの種類名直下に配置する。  

### 上記以外

コンテナ内で扱うものを、`/src`ディレクトリ以下での分け方と同じようにディレクトリ分けをする。  
使われる広さが異なる場合は、上記の`/components`と同じ考え方で配置する。  

### 構成例

ファイル一覧画面で、複雑なフィルターを設定するダイアログのようなものがある想定。

```
/src
    /FileList
        /Page
            FileListPage.tsx
            FileListPageState.ts
            /components
                FileListCard.tsx
            /infra
                /api
                    FileApi.ts
            /usecases
                FetchFileListUseCase.ts
        /Filter
            FileListFilter.tsx
            FileListFilterState.ts
            /components
                FileListFilterForm.tsx
            /infra
                /api
                    NamelistApi.ts
        /models
            File.ts
```

## 作り方

- `useUseCase`に初期状態のステートを渡します
- ステートから必要な情報をコンポーネントに`props`経由で渡します
- コンポーネントのイベントハンドラでUseCaseを実行します

```tsx
const initialState = new FileListPageState();

export const FileListPage = () => {
    const { usecase, state } = useUseCase(initialState);

    const handleClick = useCallback(() => {
        usecase(new FetchFileListUseCase()).execute();
    // usecaseは一意になるように設定されています
    }, [usecase]);

    return (
        <>
            <AppHeader>
                <Typography variant="title">ファイル一覧</Typography>
                <Spacer>
                <Button onClick={handleClick}>最新に更新</Button>
            </AppHeader>
            <Container>
                <FileList files={state.files} />
            </Container>
        </>
    );
}
```

### ページコンポーネントの場合

`props`を`RouteComponentProps`という型で受け取ります。  

```tsx
import { RouteComponentProps } from '@reach/router';

export const FileListPage = (props: RouteComponentProps) => {
    // 略
}
```

詳しくは[reach-routerのTypeScriptのページ](https://reach.tech/router/typescript)や、実装済みのページコンポーネントを参照してください。
