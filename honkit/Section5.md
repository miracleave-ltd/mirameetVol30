# おまけ  
最後に、APIの無効化と作成Cloud Functionsを削除をします。  

※無料期間が終了しても自動で課金されることはありません  
　GCP内をCleanUpしたい方は以下手順を行ってください  

----
## Cloud Functionsの削除
1. 以下URLまたは、検索バーから「Cloud Functions」と検索し、Cloud Functionsを開きます。  
https://console.cloud.google.com/functions/  

2. 「bigquerySelect」にチェックボックスを入れ、『権限』を選択します。  
    ![](img/section5-1.png)   

3. 「Cloud Functions 開発者」から、allUsersの『ゴミ箱マーク』を選択します。
    ![](img/section5-2.png)   

4. 削除の確認が求められるので、『削除』を選択します。
    ![](img/section5-3.png)   

5. 作成したCloud Functions にチェックを入れ、『削除』を選択します。
    ![](img/section5-4.png)   

6. 削除の確認が求められるので、『削除』を選択します。
    ![](img/section5-5.png)   

## GCPのAPI無効化  
1. 以下URLまたは、検索バーから「APIとサービス」と検索し、APIとサービス画面を表示します。  
https://console.cloud.google.com/apis/dashboard

1. 以下APIを選択します。  
   - Cloud Functions API
   - Cloud Build API
   - Maps JavaScript API

    ![](img/section5-6.png)   

2. 各APIの画面で『APIを無効化する』、または『無効にします』を選択します。
    ![](img/section5-7.png)   

    ![](img/section5-8.png)   

    ![](img/section5-9.png)   
