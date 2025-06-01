---
title: "Firebase AI Logic for Unity発表！XR体験にGeminiを"
emoji: "🥽"
type: "tech"
topics: ["firebase", "unity", "ai", "xr", "gemini"]
published: true
publication_name: "hololab"
---

## はじめに

2025年5月20日、Googleが**Firebase AI Logic for Unity**のプレビュー版を発表しました！これにより、UnityでのAndroid XRアプリケーション開発において、Geminiモデルを活用した強力な生成AI機能を直接統合できるようになります。

![Firebase AI Logic](/images/articles/firebase-ai-logic-unity-androidxr/ailogic-logo_1600x800.png)
*出典: [Firebase Blog - Introducing Unity support in Firebase AI Logic](https://firebase.blog/posts/2025/05/ai-logic-unity-androidxr)*

## Firebase AI Logic for Unityとは？

これまで、UnityでGeminiのようなAIモデルを使用するには、複雑なバックエンドセットアップが必要でした。Firebase AI Logic for Unityは、この課題を解決し、Unityランタイム内でGeminiの機能に直接かつ安全にアクセスできるクライアント側SDKです。

### 主な特徴

- **Unityとの直接統合**: 複雑なバックエンド不要
- **マルチモーダル対応**: テキスト、画像、音声の入出力をサポート
- **リアルタイムストリーミング**: 双方向音声会話が可能
- **Firebase連携**: Authentication、App Check、Cloud Storageとの統合
- **Unity 2021 LTS以降対応**: AndroidとiOSの両方をサポート

## 2つのAPI選択肢

![AI Logic Flow](/images/articles/firebase-ai-logic-unity-androidxr/ailogic-flow_1600x800.png)
*出典: [Firebase Blog - Introducing Unity support in Firebase AI Logic](https://firebase.blog/posts/2025/05/ai-logic-unity-androidxr)*

### Gemini Developer API（プレビュー版）
- **無料枠あり**: プロトタイピングに最適
- **従量課金制**: 本番アプリにも対応
- 開発開始時におすすめ

### Vertex AI Gemini API（GA版）
- **エンタープライズグレード**: 可用性とガバナンスを重視
- **本番環境向け**: 重要なアプリケーションに最適

無料のGemini Developer APIから始めて、必要に応じてVertex AI Gemini APIに移行することが可能です。

## XR体験での活用例

### 1. インタラクティブな音声インターフェース
音声会話インターフェースを提供し、ユーザーがコンテンツやメタデータについて質問できる新しいインタラクション層を追加します。Geminiの双方向ストリーミング機能を活用し、ユーザーはテキストをアップロードし、音声でやり取りし、リアルタイムで音声応答を受け取ることができます。

### 2. 動的な環境に適応した画像生成
ユーザーが自然言語で説明した要素をAIが2Dグラフィックとして生成し、よりイマーシブで動的な環境を作成します。Geminiの画像生成機能を活用して、ユーザーの体験に合わせてカスタマイズされたコンテンツを提供します。

### 3. インテリジェントなNPC
NPCにユーザーのコンテキストを認識させ、ユーザーと環境を説明するテキストや画像を提供することで、より動的な会話と体験を実現します。

## 実装例：NPCとの会話

```csharp
var model = FirebaseAI.DefaultInstance.GetGenerativeModel(
  modelName: "gemini-2.0-flash",
  systemInstruction: ModelContent.Text(
    "You are a virtual guide in an XR environment. Help users navigate and understand the space around them."
  )
);

var chat = model.StartChat(/* Optional chat history */);
var response = await chat.SendMessageAsync("What can you tell me about this area?");
Debug.Log(response.Text);
```

## Android XR Developer Preview 2対応

Firebase AI Logic for UnityはUnity 6でのAndroid XRプロジェクトと互換性があります！

### マルチモーダル入力を使用したテキスト生成

```csharp
var model = FirebaseAI.DefaultInstance.GetGenerativeModel(
  modelName: "gemini-2.0-flash"
);

var multimodalPrompt = new [] {
  ModelContent.Text(textPrompt),
  ModelContent.InlineData("image/png",
    UnityEngine.ImageConversion.EncodeToPNG(unityTexture2D))
};

var response = await model.GenerateContentAsync(multimodalPrompt);
Debug.Log(response.Text);
```

### 抽象アート作品の生成

```csharp
var generationConfig = new GenerationConfig(
  responseModalities: new ResponseModality[] {
    ResponseModality.Text, ResponseModality.Image }
);

var model = FirebaseAI.DefaultInstance.GetGenerativeModel(
  modelName: "gemini-2.0-flash-preview-image-generation",
  generationConfig: generationConfig
);

var response = await model.GenerateContentAsync(
  "Generate abstract artwork for a room that complements a color palette: " + roomColors
);

// 画像をローカルファイルに保存
var imagePart = response.Candidates.First().Content.Parts
  .OfType<ModelContent.InlineDataPart>()
  .First(part => part.MimeType.Contains("image"));
var imageFile = File.Create("output/path/to/image");
imageFile.Write(imagePart.Data.ToArray());
imageFile.Close();
```

## 今後の展望

Firebase AI Logic for Unityのローンチは、没入感のあるXR体験において最先端の生成AIを手軽に利用できるようにする重要な一歩です。このSDKにより、次世代のAI搭載XR体験を構築できるようになりそうです。

## 始め方

[公式ガイド](https://firebase.google.com/docs/ai-logic/get-started)でFirebase AI Logic SDK for Unityの使用方法を学ぶことができます。

## まとめ

- Firebase AI Logic for Unityがプレビューとして発表
- Geminiモデルを直接Unity内で利用可能
- XR体験に新しい可能性をもたらす
- Android XR Developer Preview 2に対応
- 無料枠から始められ、エンタープライズ向けオプションも提供

この新しいSDKにより、より簡単にAI機能をXRアプリケーションに統合できるようになり、次世代のインタラクティブ体験の創造が可能になります。
