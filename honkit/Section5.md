# おまけ  
最後に、APIの無効化と作成Cloud Functionsを削除をします。  

※無料期間が終了しても自動で課金されることはありません  
　GCP内をCleanUpしたい方は以下手順を行ってください  

----

## GCPのAPI無効化  
1. 以下URLまたは、検索バーから「APIとサービス」と検索し、APIとサービス画面を表示します。  
https://console.cloud.google.com/apis/dashboard

1. 以下APIを選択し、『APIを無効化する』を選択します。
   - Maps JavaScript API
   - Cloud Build API
   - Cloud Functions API

## Cloud Functionsの削除
1. Cloud Functionsを開きます。  
https://console.cloud.google.com/functions/  

2. 作成したCloud Function にチェックを入れ、『削除』を選択します。
