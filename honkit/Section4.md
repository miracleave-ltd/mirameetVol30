# Map画面作成  
この手順では、GoogleMapJavaSciriptAPIを使い、BigQueryの情報を絞り込んでピン止めするプログラムを作成してきます。  
また、開発したプログラムはCloud Shell Editerで実行し、動作確認をしていきます。  

![](img/mirameetvol30.drawio_3.png) 
----
## ソースコードの取得  
1. 以下URLより、GoogleのSampleコードを取得します。  
https://ssh.cloud.google.com/cloudshell/editor?cloudshell_git_repo=https%3A%2F%2Fgithub.com%2Fgooglemaps%2Fjs-samples&cloudshell_git_branch=sample-threejs-overlay-simple&cloudshell_tutorial=cloud_shell_instructions.md&cloudshell_workspace=.&hl=ja  
1. Cloud Shellの説明が表示されたら、『続行』を選択します。
2. Cloneするソースコードのリポジトリの確認が求められたら、『確認』を選択します。
    ![](img/section4-1.png)   
3. Cloud Shell Editerが起動され、Smapleコードのプロジェクトが開いていることを確認します。
    ![](img/section4-2.png)   

## Map画面を作成する。
1. Cloud Shell Editerからindex.tsを開き、以下コードに書き換えます。  
ソースコードの【トリガーURL】の部分は、Cloud Functionで作成したbigquerySelectのトリガーURLを記載します。  
このプログラムは、手順[2.]で作成したAPIを呼び出し、検索結果をGoogle Map JavaScrit APIに反映しています。  
Google Map JavaScript APIの設定オプションは今回はデフォルトで行います。  
※バックエンド機能がJavaScriptに対し、フロントはTypeScriptですが深い意味はありません。  
    ![](img/section4-3.png)   
   - ソースコード
    ```
    import * as THREE from "three";
    import { GLTFLoader } from "three/examples/jsm/loaders/GLTFLoader";
    import { ThreeJSOverlayView } from "@googlemaps/three";

    let map: google.maps.Map;

    function initMap(): void {
        let url = new URL(window.location.href);
        let params = url.searchParams;
        let triggerUrl=`【トリガーURL】`;
        if (params.get('address') != null) {
            triggerUrl=triggerUrl+`?address=`+params.get('address');
        } 
        let mapOptions;
        let data = new Array();
        fetch(triggerUrl)
        .then(response => {
            console.log(response.status);
            response.json().then(userInfo => {
                for (var i = 0; i < userInfo.length; i++) {
                    mapOptions = {
                        tilt: 0,
                        heading: 0,
                        zoom: 18,
                        center: { lat: Number(userInfo[i].Latitude), lng: Number(userInfo[i].Longitude) },
                        mapId: "15431d2b469f209e",
                        disableDefaultUI: true,
                        gestureHandling: "none",
                        keyboardShortcuts: true,
                    };
                    const mapDiv = document.getElementById("map") as HTMLElement;

                    map = new google.maps.Map(mapDiv, mapOptions);

                    const scene = new THREE.Scene();

                    const ambientLight = new THREE.AmbientLight(0xffffff, 0.75);

                    scene.add(ambientLight);

                    const directionalLight = new THREE.DirectionalLight(0xffffff, 0.25);

                    directionalLight.position.set(0, 10, 50);
                    scene.add(directionalLight);

                    // Load the model.
                    const loader = new GLTFLoader();
                    const url =
                        "https://raw.githubusercontent.com/googlemaps/js-samples/main/assets/pin.gltf";

                    loader.load(url, (gltf) => {
                        gltf.scene.scale.set(10, 10, 10);
                        gltf.scene.rotation.x = Math.PI / 2;
                        scene.add(gltf.scene);

                        let { tilt, heading, zoom } = mapOptions;

                        const animate = () => {
                        if (tilt < 67.5) {
                            tilt += 0.5;
                        } else if (heading <= 360) {
                            heading += 0.2;
                            zoom -= 0.0005;
                        } else {
                            // exit animation loop
                            return;
                        }

                        map.moveCamera({ tilt, heading, zoom });

                        requestAnimationFrame(animate);
                        };

                        requestAnimationFrame(animate);
                    });

                    new ThreeJSOverlayView({
                        map,
                        scene,
                        anchor: { ...mapOptions.center, altitude: 100 },
                        THREE,
                    });
                };
            });
        });
    }

    declare global {
    interface Window {
        initMap: () => void;
    }
    }
    window.initMap = initMap;
    export { initMap };

    ```
1. ターミナルから、以下コマンドを実行し、npmを初期化します。
    ```
    npm i 
    ```
## 動作確認
1. ターミナルから、以下コマンドを実行します。  
    ```
    npm start
    ```
2. ターミナル上の実行ログに表示される"http://localhost:300x/"をクリックし、動作確認を行います。  
※port（300x）は人によって異なります。  
    ![](img/section4-4.png)   
3. リクエストパラメーターにaddressを付け加え、検索条件値を入れて条件を絞ってみましょう。  
検索条件に応じて東京タワーやスカイツリーが表示されるかと思います。  
※【URL】の部分は各GCPアカウントによって異なります。  
    ```
    https://【URL】&address=東京タワー
    https://【URL】&address=スカイツリー
    ```
    ![](img/section4-5.png)   

## スタイルの変更
1. Google Map JavaScript APIではレイアウトの変更がプログラム修正なしで可能です。  
以下、URLにアクセスし、レイアウトを作成する場合はマップIDを作成します。  
https://console.cloud.google.com/google/maps-apis/studio/maps
2. 『マップIDを作成』を選択します。  
    ![](img/section4-6.png)   
3. 以下設定内容で、『保存』を選択します。
    |  設定項目  |  設定値  |
    | ---- | ---- |
    |  環境  |  第１世代  |
    |  名前  |  任意  |
    |  地図の種類  |  JavaScript  |
    |  地図の種類  |  ラスター  |
4. 次に、スタイルを作成します。  
左メニューから『地図のスタイル』を選択し、『スタイルを作成』を選択します。
    ![](img/section4-7.png)   
5. 中央にあるバリエーションから任意のレイアウトを選択し、スタイルの名前を入力して『保存』を選択します。  
※デフォルトでもOKです。  
    ![](img/section4-8.png)   
6. 『ADD MAP IDS』を選択し、先ほどのマップIDに紐づけます。
    ![](img/section4-9.png)   
7. 左メニューから『マップ管理』を選択し、先ほどのマップIDをコピーし、再度Cloud Shell Editerを開きます。  
    ![](img/section4-10.png)   
8. index.tsを開き、mapOptionsのmapIdに設定し、保存します。
    ![](img/section4-11.png)   
## 一連の動作確認
1. スタイルの設定まで出来たら、Cloud Storageに追加のJSONデータを配置し、一連の流れでデータが登録されてMap上に反映されるまで確認してみましょう。  
    ※追加のJSONデータをコチラで用意していますので、以下リンクよりダウンロードください。  
      ダウンロードリンク：https://drive.google.com/file/d/1X8QXCKnN_sWgWR83a_F7JPSo9FXAedN6/view?usp=sharing
    追加のデータには、以下３つの住所情報は入っています。
        - ディズニーランド
        - ユニバーサルスタジオジャパン
        - エトワール凱旋門


2. "http://localhost:300x/"を開き、スタイルが反映されていることを確認します。  
※port（300x）は人によって異なります。  