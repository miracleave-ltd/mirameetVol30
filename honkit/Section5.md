# プログラム実行  
これまでの手順で作成したプログラムを実行し、一連の流れで最終確認を行います。  
実行環境はCloud Shell Editerです。  

----
 
![](img/mirameetvol30.drawio_4.png) 
  
## 動作確認
1. 追加で[Cloud Storage](https://console.cloud.google.com/storage/browser)にJSONデータを配置します。  
既に２件のデータ（スカイツリー、東京タワー）が登録されているので、追加で３件のデータを  
登録し、５件のデータが検索出来ることと  検索結果に応じた画面表示できることを確認します。  
追加のJSONデータは、以下リンクよりダウンロードください。   
以下３つの住所情報が入っています。  
[ダウンロードリンク](https://drive.google.com/file/d/1X8QXCKnN_sWgWR83a_F7JPSo9FXAedN6/view?usp=sharing)    
   - ディズニーランド
   - ユニバーサルスタジオジャパン
   - エトワール凱旋門

    ![](img/section5-1.png)   

1. [Cloud Shell Editer](https://console.cloud.google.com/cloudshelleditor)から、 "http://localhost:3001/" を開きます。  


2. リクエストパラメーターを付け加え、検索条件値を入れて条件を絞ってみましょう。  
検索条件に応じて東京タワーやスカイツリーが表示されるかと思います。  
    ![](img/section5-3.png)   
※パラメータ内容
    ```
    &address=ディズニー
    ```
    ```
    &address=ユニバーサル
    ```  
検索条件に応じて東京タワーやスカイツリーが表示されるかと思います。  
また、前手順で設定したスタイル設定も反映されていることを確認します。  
    ![](img/section5-2.png)   
