---
title: "PLATEAU SDK-AR-Extensions for Unityのサンプルを動かす"
emoji: "🏙️"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: [plateau, unity, ar, arcore, geospatialapi]
published: true
---

## 1. 概要

[PLATEAU SDK-AR-Extensions for Unity](https://github.com/Project-PLATEAU/PLATEAU-SDK-AR-Extensions-for-Unity) のサンプルを新規プロジェクトから作成した場合、動かすまでに一苦労があったためまとめる。

## 2. PLATEAU SDK-AR-Extensions for Unityとは

[PLATEAU](https://www.mlit.go.jp/plateau/)とは、[PLATEAU SDK-AR-Extensions for Unity](https://github.com/Project-PLATEAU/PLATEAU-SDK-AR-Extensions-for-Unity)とは何かはPLATEAU SDK-AR-Extensions for Unityの [README](https://github.com/Project-PLATEAU/PLATEAU-SDK-AR-Extensions-for-Unity/blob/main/README.md) を参照してください。

## 3. 環境

### OS
- macOS Sonoma 14.2.1

### Unity
- Unity 2021.3.35f1
  - URP(Universal Render Pipeline)

### PLATEAU
- PLATEAU SDK for Unity v2.3.2
- PLATEAU SDK-AR-Extensions for Unity v1.0.1

## 4. 手順 - 各種インストール

### 4.1. UnityのURPプロジェクト作成

1. Unity HubのNew Projectから `Universal 3D` のプロジェクトを作成する
    - この時、Editor Versionは `2021.3.35f1` になっていることを確認する

### 4.2. PLATEAU SDK for Unityのインストール（[参考](https://project-plateau.github.io/PLATEAU-SDK-for-Unity/manual/Installation.html#tgz%E3%83%95%E3%82%A1%E3%82%A4%E3%83%AB%E3%81%8B%E3%82%89%E5%B0%8E%E5%85%A5%E3%81%99%E3%82%8B%E6%96%B9%E6%B3%95)）

1. PLATEAU SDK for Unityの[Releaseページ](https://github.com/Project-PLATEAU/PLATEAU-SDK-for-Unity/releases)から `v2.3.2` の **PLATEAU-SDK-for-Unity-v2.3.2.tgz** をダウンロードする。
2. Unityのメニューバーから `Window` -> `Package Manager` を開く
3. Package Managerの `+` ボタンから `Add package from tarball...` を選択する
    ![](/images/articles/plateau-sdk-ar-sample-manual/4-2-1-1.png)
4. 1でダウンロードした**PLATEAU-SDK-for-Unity-v2.3.2.tgz** を選択する

:::message alert
PLATEAU SDK for Unityの[公式ドキュメント](https://project-plateau.github.io/PLATEAU-SDK-for-Unity/manual/Installation.html)には **GitのURL指定で導入する方法** もありますが、この方法でインストールすると下記のようなエラーが大量に発生するのでおすすめしません。
```
Could not create asset from Packages/com.synesthesias.plateau-unity-sdk/Images/AreaSelect/LOD2/bridge.png: File could not be read
```
:::

### 4.3. PLATEAU SDK-Toolkits for Unityのインストール（[参考](https://github.com/Project-PLATEAU/PLATEAU-SDK-Toolkits-for-Unity?tab=readme-ov-file#3-plateau-sdk-toolkits-for-unity-%E3%81%AE%E3%82%A4%E3%83%B3%E3%82%B9%E3%83%88%E3%83%BC%E3%83%AB)）

1. PLATEAU SDK-Toolkits for Unityの[Releaseページ](https://github.com/Project-PLATEAU/PLATEAU-SDK-Toolkits-for-Unity/releases)から `v1.0.1` の **com.unity.plateautoolkit-1.0.1.tgz** をダウンロードする。
2. Unityのメニューバーから `Window` -> `Package Manager` を開く
3. Package Managerの `+` ボタンから `Add package from tarball...` を選択する
    ![](/images/articles/plateau-sdk-ar-sample-manual/4-2-1-1.png)
4. 1でダウンロードした**com.unity.plateautoolkit-1.0.1.tgz** を選択する
5. 入力システムについての確認ダイアログが表示された場合は `Yes` を選択します。
    ![](/images/articles/plateau-sdk-ar-sample-manual/4-3-3-1.png)

### 4.4. Google ARCore Extensionsのインストール（[参考](https://www.mlit.go.jp/plateau/learning/tpc23-1/)）

Google ARCore Extensionsのインストールは [TOPIC 23｜3D都市モデルを使った位置情報共有ゲームを作る[1/2]｜基本のゲームを作る](https://www.mlit.go.jp/plateau/learning/tpc23-1/) を参考にしていただけれ問題ありませんが、こちらにも必要な部分をピックアップして記載します。

1. [AR Foundationのインストール](https://www.mlit.go.jp/plateau/learning/tpc23-1/#:~:text=%EF%BC%BB1%EF%BC%BDAR%20Foundation%E3%81%A8Geospatial%20API%E3%81%AE%E3%82%A4%E3%83%B3%E3%82%B9%E3%83%88%E3%83%BC%E3%83%AB)
2. [Apple ARKit XR Plugin、Google ARCore XR Pluginのインストール](https://www.mlit.go.jp/plateau/learning/tpc23-1/#:~:text=%EF%BC%BB2%EF%BC%BDApple%20ARKit%20XR%20Plugin%E3%80%81Google%20ARCore%20XR%20Plugin%E3%81%AE%E3%82%A4%E3%83%B3%E3%82%B9%E3%83%88%E3%83%BC%E3%83%AB)
3. [Geospatial APIのインストール](https://www.mlit.go.jp/plateau/learning/tpc23-1/#:~:text=%EF%BC%BB3%EF%BC%BDGeospatial%20API%E3%81%AE%E3%82%A4%E3%83%B3%E3%82%B9%E3%83%88%E3%83%BC%E3%83%AB)
4. [ARCoreおよびARKitの有効化](https://www.mlit.go.jp/plateau/learning/tpc23-1/#:~:text=%EF%BC%BB4%EF%BC%BDARCore%E3%81%8A%E3%82%88%E3%81%B3ARKit%E3%81%AE%E6%9C%89%E5%8A%B9%E5%8C%96)
5. [Geospatial APIのAPIキーを設定する](https://www.mlit.go.jp/plateau/learning/tpc23-1/#:~:text=%EF%BC%BB6%EF%BC%BDGeospatial%20API%E3%81%AEAPI%E3%82%AD%E3%83%BC%E3%82%92%E8%A8%AD%E5%AE%9A%E3%81%99%E3%82%8B)
6. [プラットフォームの設定を変更する](https://www.mlit.go.jp/plateau/learning/tpc23-1/#:~:text=%E3%81%A6%E3%81%8F%E3%81%A0%E3%81%95%E3%81%84%E3%80%82-,%EF%BC%BB7%EF%BC%BD%E3%83%97%E3%83%A9%E3%83%83%E3%83%88%E3%83%95%E3%82%A9%E3%83%BC%E3%83%A0%E3%81%AE%E8%A8%AD%E5%AE%9A%E3%82%92%E5%A4%89%E6%9B%B4%E3%81%99%E3%82%8B,-%EF%BC%BBPlayer%EF%BC%BD%E3%82%BF%E3%83%96%E3%81%A7)
7. [GeospatialConfigを修正する](https://www.mlit.go.jp/plateau/learning/tpc23-1/#:~:text=%EF%BC%BB8%EF%BC%BDGeospatialConfig%E3%82%92%E4%BF%AE%E6%AD%A3%E3%81%99%E3%82%8B)
8. [Renderer-Featuresを設定する](https://www.mlit.go.jp/plateau/learning/tpc23-1/#:~:text=%EF%BC%BB9%EF%BC%BDRenderer%2DFeatures%E3%82%92%E8%A8%AD%E5%AE%9A%E3%81%99%E3%82%8B)
    ［Project］ウィンドウで、［Assets］―［Settings］に含まれるURPの設定項目をクリックし、［Inspector］で、［Renderer Features］に［AR Background Renderer Feature］が設定されていない場合は、追加してください。使用するURP設定項目がわからなければ、Settings以下のURP-XXXXのすべてに対して追加してください。
    :::message
    URPプロジェクトの場合は、この設定を行わないとアプリでカメラ画像が真っ暗で表示されません。
    :::

### 4.5. PLATEAU SDK-AR-Extensions for Unityのインストール（[参考](https://github.com/Project-PLATEAU/PLATEAU-SDK-AR-Extensions-for-Unity?tab=readme-ov-file#plateau-sdk-ar-extensions-for-unity-%E3%81%AE%E3%82%A4%E3%83%B3%E3%82%B9%E3%83%88%E3%83%BC%E3%83%AB)）

1. PLATEAU SDK-AR-Extensions for Unityの[Releaseページ](https://github.com/Project-PLATEAU/PLATEAU-SDK-AR-Extensions-for-Unity/releases)から `v1.0.1` の **com.unity.plateautoolkit.ar-1.0.1.tgz** をダウンロードする。
2. Unityのメニューバーから `Window` -> `Package Manager` を開く
3. Package Managerの `+` ボタンから `Add package from tarball...` を選択する
    ![](/images/articles/plateau-sdk-ar-sample-manual/4-2-1-1.png)
4. 1でダウンロードした**com.unity.plateautoolkit.ar-1.0.1.tgz** を選択する
    :::message alert
    下記のようなエラーが出ます。4.6. Cesium for Unityのインストールのインストールを行なってください。
    ```
    Library/PackageCache/com.unity.plateautoolkit.ar@07e1ee401d81/PlateauToolkit.AR/Runtime/PlateauARPositioning.cs(7,7): error CS0246: The type or namespace name 'CesiumForUnity' could not be found (are you missing a using directive or an assembly reference?)
    Library/PackageCache/com.unity.plateautoolkit.ar@07e1ee401d81/PlateauToolkit.AR/Runtime/PlateauARPositioning.cs(51,26): error CS0246: The type or namespace name 'CesiumGeoreference' could not be found (are you missing a using directive or an assembly reference?)
    Library/PackageCache/com.unity.plateautoolkit.ar@07e1ee401d81/PlateauToolkit.AR/Runtime/PlateauARPositioning.cs(52,26): error CS0246: The type or namespace name 'Cesium3DTileset' could not be found (are you missing a using directive or an assembly reference?)
    Shader Graph at Packages/com.unity.plateautoolkit.ar/PlateauToolkit.AR/Runtime/Shaders/CesiumTilesetClippingShader.shadergraph has 8 error(s), the first is: Validation: Could not find Sub Graph asset with GUID 45c8c2a0ab2df934c8e0f63147f35d0e.
    ```
    :::
    
### 4.6. Cesium for Unityのインストール（[参考](https://github.com/Project-PLATEAU/PLATEAU-SDK-Maps-Toolkit-for-Unity?tab=readme-ov-file#cesium-for-unity-%E3%81%AE%E3%82%A4%E3%83%B3%E3%82%B9%E3%83%88%E3%83%BC%E3%83%AB)）

PLATEAU SDK-AR-Extensions for Unityの3DTilesを用いたサンプルを使わない場合は不要ですが、エラーが出るためCesium for Unityをインストールします。

1. Cesium for Unityの[Releaseページ](https://github.com/CesiumGS/cesium-unity/releases/tag/v1.6.3)から `v1.6.3` の **com.cesium.unity-1.6.3.tgz** をダウンロードする。
2. Unityのメニューバーから `Window` -> `Package Manager` を開く
3. Package Managerの `+` ボタンから `Add package from tarball...` を選択する
    ![](/images/articles/plateau-sdk-ar-sample-manual/4-2-1-1.png)
4. 1でダウンロードした**com.cesium.unity-1.6.3.tgz** を選択する

## 5. 手順 - サンプルの実行

PLATEAU SDK-AR-Extensions for Unityの[1. サンプルを用いたARアプリケーションの体験](https://github.com/Project-PLATEAU/PLATEAU-SDK-AR-Extensions-for-Unity?tab=readme-ov-file#1-%E3%82%B5%E3%83%B3%E3%83%97%E3%83%AB%E3%82%92%E7%94%A8%E3%81%84%E3%81%9Far%E3%82%A2%E3%83%97%E3%83%AA%E3%82%B1%E3%83%BC%E3%82%B7%E3%83%A7%E3%83%B3%E3%81%AE%E4%BD%93%E9%A8%93)を参考にしていただけれ問題ありませんが、こちらにも必要な部分をピックアップして記載します。

### 5.1. [AR Extensions サンプルのインポート](https://github.com/Project-PLATEAU/PLATEAU-SDK-AR-Extensions-for-Unity?tab=readme-ov-file#1-1-ar-extensions-%E3%82%B5%E3%83%B3%E3%83%97%E3%83%AB%E3%81%AE%E3%82%A4%E3%83%B3%E3%83%9D%E3%83%BC%E3%83%88)

### 5.2. [サンプルシーンを設定する](https://github.com/Project-PLATEAU/PLATEAU-SDK-AR-Extensions-for-Unity?tab=readme-ov-file#1-2-%E3%82%B5%E3%83%B3%E3%83%97%E3%83%AB%E3%82%B7%E3%83%BC%E3%83%B3%E3%82%92%E8%A8%AD%E5%AE%9A%E3%81%99%E3%82%8B)

Assets/Samples/PLATEAU AR Extensions for Unity/${AR Extensions バージョン}/AR Samples/Scenes/Sample01_PlateauSdkAR.unity を開きます。

### 5.3. [ビルド設定にシーンを追加する](https://github.com/Project-PLATEAU/PLATEAU-SDK-AR-Extensions-for-Unity?tab=readme-ov-file#1-5-%E3%83%93%E3%83%AB%E3%83%89%E8%A8%AD%E5%AE%9A%E3%81%AB%E3%82%B7%E3%83%BC%E3%83%B3%E3%82%92%E8%BF%BD%E5%8A%A0%E3%81%99%E3%82%8B)

### 5.4. [アプリケーションをビルドして端末にインストールする](https://github.com/Project-PLATEAU/PLATEAU-SDK-AR-Extensions-for-Unity?tab=readme-ov-file#1-6-%E3%82%A2%E3%83%97%E3%83%AA%E3%82%B1%E3%83%BC%E3%82%B7%E3%83%A7%E3%83%B3%E3%82%92%E3%83%93%E3%83%AB%E3%83%89%E3%81%97%E3%81%A6%E7%AB%AF%E6%9C%AB%E3%81%AB%E3%82%A4%E3%83%B3%E3%82%B9%E3%83%88%E3%83%BC%E3%83%AB%E3%81%99%E3%82%8B)

## 6. 最後に

始めて触ってみた時に下記がハマりポイントでしたのでまとめてみました。
これらを回避してPLATEAUで楽しんでみてください。

- 4.2. PLATEAU SDK for Unityのインストール
- 8. Renderer-Featuresを設定する
- 4.6. Cesium for Unityのインストール