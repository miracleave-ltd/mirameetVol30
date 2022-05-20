# おまけ  

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