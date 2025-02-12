---
title: "Android XR向けのUIデザインについて"
emoji: "😎"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["Android", "AndroidXR"]
published: true 
publication_name: "hololab"
---

## 1. はじめに

2024年12月にGoogleから拡張現実（XR）ヘッドセット（将来的にはグラスも）向けに構築された新しいオペレーティングシステム Android XR が発表されました。
https://blog.google/products/android/android-xr/
https://www.youtube.com/watch?v=Pn5uG1ys-pE

他のデバイスと同様にAndroid XRを搭載したヘッドセットでは、没入型仮想空間とパススルーを行うことができます。
特にアピールされていたのはAIアシスタントのGeminiを使用して、見ているものについて会話したり、デバイスを操作したりすることができる点でした。Geminiはユーザーの意図を理解し、計画やトピックの調査などユーザーを補助してくれるそうです。

まだデバイスが出ていないため実際の操作感等はわかりませんが、Android XR SDKが公開されています。
https://android-developers.googleblog.com/2024/12/introducing-android-xr-sdk-developer-preview.html

今回はこの中のAndroid XR向けUIデザインのガイドを参考にまとめます。
https://developer.android.com/design/ui/xr/guides/get-started?hl=ja

## 2. [2つのスペースモード](https://developer.android.com/design/ui/xr/guides/foundations?hl=ja)

### ホームスペース

![](/images/articles/androidxr-design/xr-foundations-1.jpeg =500x)

- 複数のアプリを並べてマルチタスクを実行
- 対応のモバイルアプリを実行
- システム環境をサポート
- **空間パネル、3Dモデル、アプリの空間環境（後述）はサポートしていない**
- アプリに制限付きの境界がある
- アプリのサイズ
    - デフォルト : 1,024 x 720 dp
    - 最小 : 385 x 595 dp
    - 最大 : 2560 x 1800 dp
- アプリはユーザーから 1.75m の距離で起動
- **Unity、OpenXR、WebXRアプリは動かない**

### フルスペース

![](/images/articles/androidxr-design/xr-foundations-2.jpeg =500x)

- 一度に実行されるアプリは1つ
    - 他のアプリは非表示になる
- 既存のAndroidアプリを空間化できる
- **空間パネル、3Dモデル、空間環境、空間オーディオをサポート**
- アプリに境界はない
- 起動位置を指定でき、移動とサイズ変更を行うことができる
- アプリを直接開くことができる
- **Unity、OpenXR、WebXRアプリが動作する**

### 所感
ホームスペースとフルスペースの2つのスペースが存在します。
ホームスペースはPCの延長という感じで、PCの画面を複数空間に並べるようなイメージと考えています。

フルスペースは3Dモデルを表示したり空間環境を利用する時と、映像などに没入する時に利用すると考えています。
またXR界隈でよく利用するUnityのアプリはフルスペースにする必要があります。
ここは注意が必要そうです。

## 3. [アプリの種類](https://developer.android.com/design/ui/xr/guides?hl=ja)

### XR対応モバイルアプリ

![](/images/articles/androidxr-design/xr-mobile.jpg =500x)

- 既存のモバイル向けアプリ
- Android XRでサポートされていない機能を必要としていない限り互換性がある

### XR対応大画面アプリ

![](/images/articles/androidxr-design/xr-large-screens.jpg =500x)

- 大画面対応したアプリ

### XR向けの差別化アプリ

![](/images/articles/androidxr-design/xr-differentiated.jpg =500x)

- XR向けに作成されたアプリ
- 空間パネルや3Dモデルの表示を行うことができる
- フルスペースにする必要がある

### 所感
既存のモバイルアプリが利用できるためAndroid XRデバイスが出た時に最初から複数のアプリが利用できそうです。
しかしAndroid XRをフルで活かすためには、XR向けの差別化アプリを作成する必要があります。

## 4. [XR向けの差別化アプリの要素](https://developer.android.com/design/ui/xr/guides?hl=ja)

XR向けの差別化アプリの要素は全てフルスペースのみ対応しています。

### [空間パネル](https://developer.android.com/design/ui/xr/guides/spatial-ui?hl=ja)

![](/images/articles/androidxr-design/xr-spatial.jpeg =500x)

- Android XRアプリの基本的な構成要素
- 複数の空間パネルを並べることができ、パネル数に制限はない
- パネルのレイアウトは特定の位置や任意の位置に設定することができる
    ![](/images/articles/androidxr-design/spatial-ui-flat.jpg =500x)
    *フラット行レイアウト*

    ![](/images/articles/androidxr-design/spatial-ui-curved.jpg =500x)
    *曲線行レイアウト*

    ![](/images/articles/androidxr-design/spatial-ui-arbitrary.jpg =500x)
    *任意の位置のレイアウト*
  
- オービターという空間パネル内のコンテンツを制御するフローティングUI要素がある
    ![](/images/articles/androidxr-design/spatial-ui-nav-rail-1.jpg =500x)
    ![](/images/articles/androidxr-design/spatial-ui-nav-rail-2.jpg =500x)
    ![](/images/articles/androidxr-design/spatial-ui-nav-bar-1.jpg =500x)
    ![](/images/articles/androidxr-design/spatial-ui-nav-bar-2.jpg =500x)

### [空間環境](https://developer.android.com/design/ui/xr/guides/environments?hl=ja)

![](/images/articles/androidxr-design/xr-environments.jpeg =500x)

- パススルーで表示するか、没入型仮想空間で空間環境をオーバーライドできる
- 空間環境はEXRパノラマ画像、周囲の3Dジオメトリ、追加の3Dジオメトリ、またはそれらの組み合わせで作成できる
    ![](/images/articles/androidxr-design/env-panaromic.jpeg =500x)
    *EXRパノラマ画像*
    ![](/images/articles/androidxr-design/env-3d-geometry.jpg =500x)
    *周囲の3Dジオメトリ*
    ![](/images/articles/androidxr-design/env-additional.jpeg =500x)
    *追加の3Dジオメトリ*
- 周囲の3Dジオメトリ、追加の3Dジオメトリは `.gltf` または `.glb` のファイル拡張子をサポート

### [3Dモデル](https://developer.android.com/design/ui/xr/guides/3d-content?hl=ja)

![](/images/articles/androidxr-design/videoframe_4069.png =500x)

- `.glTF` または `.glb` のファイル拡張子を持つ3Dモデルをサポート
- 3Dモデルを表示するには `Scene Viewer` と `Scene Core API` を使う方法の2種類がある

#### Scene Viewer

- 別のアプリとして実行される
  - Scene Viewerを起動したアプリは非表示なる
- 回転、移動、スケーリングなどの基本的な操作を行うことができる
  - 独自のインタラクションを行うことはできない
- 一つの3Dモデルのみ表示できる
- 固定の起動位置で表示される

#### Scene Core API

- アプリ内で3Dモデルを表示できる
  - 別のアプリではない
- 回転、移動、スケーリングなど、独自のインタラクションを行うことができる
- 複数の3Dモデルを表示できる
- 起動位置をカスタマイズできる
- パネルや他の3Dモデルと親子関係を作ることができる
    ![](/images/articles/androidxr-design/videoframe_1448.png =500x)
- 現実世界の水平面や垂直面、または床や壁などの特定の面に3Dモデルを固定することができる
    ![](/images/articles/androidxr-design/videoframe_4040.png =500x)

#### 所感

手軽に3Dモデルのみを表示させたい時はScene Viewerを使い、諸々カスタマイズしたい時はScene Core APIを使えば良さそうです。
Scene Core APIは特定の面であれば3Dモデルを固定することができるので、3Dモデルを固定したい時にも役立ちそう。

## 5. 参考サイト

https://developer.android.com/design/ui/xr?hl=ja
※本記事の画像は全て上記サイトの画像を利用させていただいています

## 6. まとめ

ヘッドセットやグラスのXRデバイスを利用する際に今後必要になると考えている、見ているものについて会話したり、デバイスを操作したりすることができるAndroid XRには期待したいところです。

また、Android XRはGoogleが提供しているため、同じくGoogleが提供している[ARCore Geospatial API](https://developers.google.com/ar/develop/geospatial?hl=ja)との組み合わせも期待しています。
組み合わせることで、AIアシスタントのGeminiで現実を理解し、ARCore Geospatial APIで現実の特定の位置にオブジェクトを表示ということができそうです。

まだAndroid XRのデバイスは出ていないため実際の操作感等がわからないため、早くデバイスが出てほしいです。

最後に今回はAndroid XR向けUIデザインのガイドを参考にまとめてみました。
Android XRがどんなことができるかを知りたい時に、皆さんの助けになればと思います。