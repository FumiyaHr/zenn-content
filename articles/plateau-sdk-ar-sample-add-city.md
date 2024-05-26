---
title: "PLATEAU SDK-AR-Extensions for Unityのサンプルに都市モデルを追加する"
emoji: "🏙"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: [plateau, unity, ar, arcore, geospatialapi]
published: true
---

## 1. 概要

[PLATEAU SDK-AR-Extensions for Unity](https://github.com/Project-PLATEAU/PLATEAU-SDK-AR-Extensions-for-Unity) のサンプルにPLAtEAU SDK for Unityから都市モデルを追加した場合、動かすまでに一苦労があったためまとめます。
注意点として、`PLATEAU SDK-AR-Extensions for Unityのサンプルを動かす`を実施済みを前提で説明します。

https://zenn.dev/fumiyahr/articles/plateau-sdk-ar-sample-manual

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

### 事前環境
https://zenn.dev/fumiyahr/articles/plateau-sdk-ar-sample-manual

## 4. 都市モデルのインポート（[参考](https://project-plateau.github.io/PLATEAU-SDK-for-Unity/manual/ImportCityModels.html)）

PLATEAU SDK for UnityのManualにあるある `都市モデルのインポート` を参考に都市モデルのインポートを行なってください。
なお本記事はサーバーからインポートで試しています。

https://project-plateau.github.io/PLATEAU-SDK-for-Unity/manual/ImportCityModels.html

1. 都道府県とデータセットを選択する
    ![](/images/articles/plateau-sdk-ar-sample-add-city/4-1.png)

2. 基準座標系を選択し、範囲選択をする
3. 一括設定と建築物を設定する
    
    |設定|項目|設定|説明|
    |--|--|--|--|
    |一括設定|テクスチャを含める|チェックを外す|遮蔽オブジェクトとして使用するだけであれば不要|
    |一括設定|テスクチャを結合する|チェックを外す|PLATEAU-SDK-Toolkits-forUnityと相性が悪そう|
    |一括設定|その他|自由||
    |建築物|LOD描画設定|LOD1のみ|遮蔽オブジェクトとして使用するだけであればLOD1のみ|
    |建築物|デフォルトマテリアル|ZWite|Packages > PLATEAU SDK-AR-Extensions for Unity > PlateauToolkit.AR > Runtime > Materials > ZWrite|
    |建築物|その他|自由||

    ![](/images/articles/plateau-sdk-ar-sample-add-city/4-2.png)

4. その他は`インポートする`のチェックを外して、`モデルをインポート`を選択する
    ![](/images/articles/plateau-sdk-ar-sample-add-city/4-3.png)

## 5. モデルの設定（[参考](https://github.com/Project-PLATEAU/PLATEAU-SDK-AR-Extensions-for-Unity?tab=readme-ov-file#3-2-plateau-sdk%E3%81%A7%E3%82%A4%E3%83%B3%E3%83%9D%E3%83%BC%E3%83%88%E3%81%97%E3%81%9F3d%E9%83%BD%E5%B8%82%E3%83%A2%E3%83%87%E3%83%AB%E3%82%92%E4%BD%BF%E7%94%A8%E3%81%99%E3%82%8B%E5%A0%B4%E5%90%88)）

1. Hierarchyにインポートしたモデルがあります
2. その子オブジェクトから`LOD1`を選択する
3. Inspectorのゲームオブジェクト名の横にある`Static`にチェックが入っているので外す
    ![](/images/articles/plateau-sdk-ar-sample-add-city/5-1.png)
4. `Yes, change children`を選択する
    ![](/images/articles/plateau-sdk-ar-sample-add-city/5-2.png)
5. インポートしたモデルを`PlateauSdkCityModel`の子に移動する
6. インポートしたモデルのレイヤーを遮蔽する側のレイヤー `AR Occluder`に変更する
    ![](/images/articles/plateau-sdk-ar-sample-add-city/5-3.png)
4. `Yes, change children`を選択する
    ![](/images/articles/plateau-sdk-ar-sample-add-city/5-4.png)
5. `PlateauSdkCityModel`にアタッチされている`Plateau AR Positioning`の`Plateau City Model`にインポートしたモデルを設定する
    ![](/images/articles/plateau-sdk-ar-sample-add-city/5-5.png)
6. 13100_tokyo23-ku_2020_citygml_3_2_op を削除 or 非表示にする
7. unitychanの位置をインポートしたモデルに合わせて調整する

## 6. 最後に

始めて触ってみた時にインポートした都市モデルが`Static`になっているハマりポイントがありましたのでまとめてみました。
これが原因でGeospatial APIで位置合わせができてもインポートした都市モデルだけが表示されない現象が発生しました。
こちらを回避してPLATEAUで楽しんでみてください。