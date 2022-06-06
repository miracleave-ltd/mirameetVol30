# はじめに  
## 今回のゴール
BigQueryに溜め込んだデータ（住所情報）を、 Maps JavaScript APIを使って画面表示します。  
完成系はこんな感じです。  
![](img/githubgoal.gif)   
今回はデータの溜め込みや、画面表示をさせるために、Cloud StorageやCloud Functionsを使用します。  
今回は大きく３機能作成しますが、全てGCP上で開発が出来るハンズオンとなります。  
また、PCへのインストール不要なので、ブラウザ一つで開発するハンズオンを体験頂ければ幸いです。  

## 事前に準備頂きたいこと
- GCPアカウントの作成
  - BigQueryのデータセット作成
    - データセット名：任意　※手順の中でソースコードに埋め込みます
    - リージョン：us-central1
  - Cloud Storageのバケット作成
    - バケット名：任意　　　※手順の中でソースコードに埋め込みます
    - リージョン：us-central1

## 手順 
全体手順としては次の流れで進めます。  
 　0.事前準備  
 　1.データ登録機能作成  
 　2.検索機能作成  
 　3.Map画面作成  
 　4.プログラム実行  
 　5.最後に  
 　6.おまけ  
 ![](img/mirameetvol30.drawio_0.png)   

## 技術要素
使用する技術要素については、以下となります。  
- フロントエンド
  - TypeScript
  - Node.js
  - Cloud Shell Editer（開発環境兼、実行環境）
- バックエンド
  - JavaScript
  - Node.js
  - Cloud Functions（サーバーレス）
- API
  - Maps JavaScript API
  - BigQuery API
  - Cloud Storage API
- データベース
  - BigQuery
- ストレージ
  - Cloud Storage

以下は技術要素の参考サイトです。  
- [Maps JavaScript API](https://developers.google.com/maps/documentation/javascript?hl=ja)
  - [WebGL Overlay (ThreeJS wrapper)](https://developers.google.com/maps/documentation/javascript/examples/webgl/threejs-overlay-simple?hl=ja)のサンプルコードをベースにしています。
- [BigQuery API](https://cloud.google.com/bigquery/docs/reference/libraries-overview)
- [Cloud Storage API](https://cloud.google.com/storage/docs/apis?hl=ja)
- [Cloud Functions](https://cloud.google.com/functions/docs/concepts/overview?hl=ja)
