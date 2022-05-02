# データ登録機能作成  
今回は、データ（住所情報）は、JSON形式のデータを使用します。  
JSONデータは、BigQueryに自動で取り込めるよう、Cloud Storageに配置し、  
配置されたことトリガーとして、プログラムでBigQueryに自動登録するようにします。  

![](img/mirameetvol30.drawio_1.png) 
----
## テストデータ登録機能を作成する
1. Cloud Functionを開きます。  
https://console.cloud.google.com/functions/  
2. 『関数の作成』を選択します。
![](img/section2-1.png)   
3. 必要なAPIの有効化が求められるので、『有効にする』を選択します。
![](img/section2-2.png)   
4. 以下設定内容で、基本設定とトリガー設定を行います。  
※以下以外は全てデフォルト設定
    |  設定項目  |  設定値  |
    | ---- | ---- |
    |  環境  |  第１世代  |
    |  関数名  |  bigqueryLoad  |
    |  リージョン  |  us-central1  |
    |  トリガータイプ  |  Cloud Storage  |
    |  EventType  |  ファイナライズ / 作成  |
    |  バケット  |  *指定のバケット名  |
4. 『保存』→『次へ』を選択します。   
    ![](img/section2-3.png)   
5. ランタイムは"Node.js 16"を選択します。
6. index.jsを開き、以下コードに書き換えます。また、bucketNameとdatasetIdは指定の値に書き換えます。  
このプログラムは、GCSに配置されたトリガー情報からファイル名を取得し、  
指定のGCSバケットからBigQueryのテーブルにロードを行っています。  
GCSとBigQueryのロケーションは同じである必要があります。今回は"us-central1"で統一しています。  
そして、エントリポイントは、JavaScriptのプログラムに合わせ、"main"に変更します。
![](img/section2-4.png)   

   - ソースコード
    ```
    const { BigQuery  } = require('@google-cloud/bigquery');
    const { Storage } = require('@google-cloud/storage');
    const bigquery = new BigQuery();
    const storage = new Storage();

    exports.main = async (event, context) => {
        const gcsEvent = event
        const bucketName = 【指定のバケット名】
        const metadata = {
            sourceFormat: 'NEWLINE_DELIMITED_JSON',
            schema: {
                fields: [
                    {"name": "type","type": "STRING","mode": "NULLABLE"},
                    {
                    "name": "features",
                    "type": "RECORD",
                    "mode": "REPEATED",
                    "fields": [
                        {
                        "name": "geometry","type": "RECORD","mode": "REPEATED",
                        "fields": [
                            {
                            "name": "coordinates",
                            "type": "NUMERIC",
                            "mode": "REPEATED",
                            "fields": []
                            },
                            {
                            "name": "type",
                            "type": "STRING",
                            "mode": "NULLABLE"
                            }
                        ]
                        },
                        {
                        "name": "properties",
                        "type": "RECORD",
                        "mode": "REPEATED",
                        "fields": [
                            {
                            "name": "GoogleMapsURL",
                            "type": "STRING",
                            "mode": "NULLABLE"
                            },
                            {
                            "name": "Location",
                            "type": "RECORD",
                            "mode": "REPEATED",
                            "fields": [
                                {
                                "name": "Address",
                                "type": "STRING",
                                "mode": "NULLABLE"
                                },
                                {
                                "name": "BusinessName",
                                "type": "STRING",
                                "mode": "NULLABLE"
                                },
                                {
                                "name": "CountryCode",
                                "type": "STRING",
                                "mode": "NULLABLE"
                                },
                                {
                                "name": "GeoCoordinates",
                                "type": "RECORD",
                                "mode": "REPEATED",
                                "fields": [
                                    {
                                    "name": "Latitude",
                                    "type": "STRING",
                                    "mode": "NULLABLE"
                                    },
                                    {
                                    "name": "Longitude",
                                    "type": "STRING",
                                    "mode": "NULLABLE"
                                    }
                                ]
                                }
                            ]
                            },
                            {
                            "name": "Published",
                            "type": "STRING",
                            "mode": "NULLABLE"
                            },
                            {
                            "name": "Title",
                            "type": "STRING",
                            "mode": "NULLABLE"
                            },
                            {
                            "name": "Updated",
                            "type": "STRING",
                            "mode": "NULLABLE"
                            }
                        ]
                        },
                        {
                        "name": "type",
                        "type": "STRING",
                        "mode": "NULLABLE"
                        }
                    ]
                    }
                ],
            },
            location: 'us-central1',
        }
        const datasetId = 【指定のデータセット名】
        const tableId = 'TEST_TABLE'
        const [job] = await bigquery
            .dataset(datasetId)
            .table(tableId)
            .load(storage.bucket(bucketName).file(`${gcsEvent.name}`), metadata)
    };
    ```
1. package.jsonを開き、dependenciesにgoogle-cloud/storageとgoogle-cloud/bigQueryを追記します。  
    ```
    {
      "name": "sample-cloud-storage",
      "version": "0.0.1",
      "dependencies": {
        "@google-cloud/storage": "^5.8.1",
        "@google-cloud/bigquery": "^5.10.0"
      }
    }
    ```
    ![](img/section2-5.png)   
2.  『デプロイ』を選択し、デプロイされるのを待ちます。約１分ほどでデプロイが完了します。  
    ![](img/section2-6.png)  

## テストデータの作成  
1. GoogleMapを開きます。  
https://www.google.co.jp/maps  
2. お気に入りの場所を選択し、『スター付き』を選択します。  
    ![](img/section2-7.png)   
3. 複数スターを付けていきます。  
4. Googleデータエクスポートを開きます。  
https://takeout.google.com/settings/takeout  
「選択をすべて解除」選択し、「マップ（マイプレイス）」のみ選択します。  
    ![](img/section2-8.png)   
5. 「次のステップ」　＞　「エクスポート作成」をクリックします。  
6. 「ダウンロード」をクリックします。  
7. ダウンロードしたZIPファイルを解凍し、JSON形式でスターを付けた情報が存在することを確認します。  
8. ダウンロードしたJSONデータですがカラム名にスペースが混在しており、BigQueryとしては都合が悪いため、置換して削除します。  
以下コマンドをGitBash等のTerminalで実行します。
    ```
    cd *JSONデータをダウンロードしたディレクトリ

    sed -z 's/\n//g' 保存した場所.json | sed -z 's/Google Maps URL/GoogleMapsURL/g' | sed -z 's/Business Name/BusinessName/g' | sed -z 's/Geo Coordinates/GeoCoordinates/g' | sed -z 's/Country Code/CountryCode/g' > test_data.json
    ```
    ※今回は上記JSONデータをコチラで用意していますので、以下リンクよりダウンロードください。  
      ダウンロードリンク：https://drive.google.com/file/d/1LA-Uh3oEF1iGj_ClTeIVZ7_F9V5iyKuJ/view?usp=sharing
## テストデータを登録する
1. GCSを開きます。  
https://console.cloud.google.com/storage/browser  

2. 先ほどのプログラムに記述したバケット名を選択します。
3. 『ファイルをアップロード』を選択し、"test_data.json"をアップロードします。  
アップロード完了後、作成したCloud Functionがアップロードされたファイルをトリガーに、BigQueryに登録されます。  
    ![](img/section2-9.png)   
4. BigQueryを開き、SQLエディターから、テストデータが登録されているかを確認します。  
https://console.cloud.google.com/bigquery
    ```
    SELECT * FROM *指定のデータセット名.TEST_TABLE
    ```
    ![](img/section2-10.png)   

