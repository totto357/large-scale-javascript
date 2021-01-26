# Infra

インフラストラクチャの層。

DBや外部APIなどのやり取りを実装したものを置く。

- 各種APIクライアント
- Repositoryの実装
- Loggerの実装

ModelはInfraに直接依存はせずに、外から受け取る形にする。

## Repository

Repositoryはデータをコレクションのように扱えるインターフェイスをもつ。

## 永続的なもの

Socket APIのように永続化する必要があるクライアントなどが該当する。  
初期化する[usecase](./usecase.md)においてインスタンス化し、
ライフサイクルが一致する[model](./model.md)の初期化時に渡す。
