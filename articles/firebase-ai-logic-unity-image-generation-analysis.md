---
title: "Firebase AI Logic for Unity：画像生成・画像解析の実装と検証"
emoji: "🖼️"
type: "tech"
topics: ["firebase", "unity", "ai", "gemini"]
published: true
publication_name: "hololab"
---

## はじめに

Firebase AI Logic for Unityシリーズ第3弾！今回は**画像生成**と**画像解析**機能にフォーカスして、実装方法から実際の動作検証まで詳しく解説します。

前回までの記事：
- [第1弾：Firebase AI Logic for Unity発表！GeminiでXR体験を革新する](https://zenn.dev/hololab/articles/firebase-ai-logic-unity-androidxr)
- [第2弾：環境構築からサンプル実行まで](https://zenn.dev/hololab/articles/firebase-ai-logic-unity-sample-tutorial)

今回は、Geminiモデルを使った視覚的なAI機能をUnityアプリケーションに統合する方法を確認しました。

## 本記事で扱う内容

### 画像解析機能
- **画像を読み込んでAIに質問**: 写真や図表について自然言語で問い合わせ
- **リアルタイムストリーミング分析**: 段階的に分析結果を受信
- **Texture2Dからの画像分析**: Unity内のTexture2Dデータを直接分析可能

### 画像生成機能
- **テキストプロンプトから画像を生成**: 自然言語で画像を作成
- **混在コンテンツ生成**: テキストと画像を同時に生成
- **ストリーミング生成**: リアルタイムで結果を取得

## 検証環境

- **Unity**: 6000.0.44f1
- **Firebase Unity SDK**: 12.9.0
- **対象モデル**: 
  - `gemini-2.0-flash` (画像解析)
  - `gemini-2.0-flash-preview-image-generation` (画像生成)
- **プラットフォーム**: Android

## 1. 画像解析機能の実装

### 基本的な実装パターン

Firebase AI Logic for Unityでの画像解析は、`ModelContent.InlineData()`を使用して画像データをGeminiモデルに送信します：

```csharp
// Firebase AI インスタンスの取得
var ai = FirebaseAI.GetInstance(FirebaseAI.Backend.GoogleAI());
var model = ai.GetGenerativeModel(modelName: "gemini-2.0-flash");

// Texture2Dから画像データを準備
var imageData = ModelContent.InlineData("image/png",
    UnityEngine.ImageConversion.EncodeToPNG(texture));

// プロンプトの準備
var prompt = ModelContent.Text("この画像について詳しく説明してください");

// 画像解析の実行
var response = await model.GenerateContentAsync(new [] { imageData, prompt });
UnityEngine.Debug.Log(response.Text);
```

### 技術的な制限事項

画像解析を実装する際に注意すべき制限事項：

- **リクエストサイズ**: 合計20MBまで
- **対応画像形式**: PNG、JPEG、WebP
- **最大画像数**: 1回のリクエストで3000枚まで  
- **画像解像度**: 自動的に最大3072x3072にスケーリング

### ストリーミング画像解析

リアルタイムで解析結果を受信することも可能です：

```csharp
// ストリーミングレスポンスで段階的に結果を取得
var responseStream = model.GenerateContentStreamAsync(new [] { imageData, prompt });
await foreach (var response in responseStream) {
    UnityEngine.Debug.Log(response.Text);
}
```

### 実用的な活用例

画像解析機能の代表的な用途：

- **画像キャプション生成**: 画像の内容を自然言語で説明
- **オブジェクト検出**: 画像内の物体や人物の識別
- **画像比較**: 複数の画像間の類似点や相違点の分析
- **感情分析**: 画像から読み取れる感情や雰囲気の解析


## 2. 画像生成機能の実装

### 基本的な実装パターン

Firebase AI Logic for Unityでの画像生成は、専用モデル`gemini-2.0-flash-preview-image-generation`と`ResponseModality`の設定を使用します：

```csharp
// 画像生成専用モデルの設定
var model = FirebaseAI.GetInstance(FirebaseAI.Backend.GoogleAI()).GetGenerativeModel(
    modelName: "gemini-2.0-flash-preview-image-generation",
    generationConfig: new GenerationConfig(
        responseModalities: new[] { ResponseModality.Text, ResponseModality.Image })
);

// テキストプロンプトから画像を生成
var response = await model.GenerateContentAsync("Generate an image of a sunset");

// 生成された画像データを取得
var imageParts = response.Candidates.First().Content.Parts
                   .OfType<ModelContent.InlineDataPart>()
                   .Where(part => part.MimeType == "image/png");
```

### 技術的な制限事項

画像生成機能には以下の制限があります：

- **最大画像サイズ**: 1024ピクセル
- **対応言語**: 英語、スペイン語、日本語、中国語、ヒンディー語で最高のパフォーマンス
- **生成の一貫性**: プロンプトによっては期待通りの画像が生成されない場合がある

### ベストプラクティス

効果的な画像生成のためのガイドライン：

- **明示的な画像リクエスト**: プロンプトで画像の生成を明確に要求する
- **プロンプトの試行錯誤**: 期待通りの結果が得られない場合は異なるプロンプトを試す
- **段階的なアプローチ**: 先にテキストを生成してから、関連する画像を要求する

### 画像生成の種類

Firebase AI Logic for Unityでサポートされている画像生成機能：

- **テキストから画像**: 自然言語の説明から画像を生成
- **画像編集**: 既存の画像を修正・変更
- **マルチターン画像生成**: 会話の流れの中で継続的に画像を生成

## 3. 実装時の重要なポイント

### バックエンドの選択

画像生成・解析機能は両方のバックエンドで利用可能です：

```csharp
private GenerativeModel GetModel() {
    var backend = backendSelection == 0
        ? FirebaseAI.Backend.GoogleAI()      // Gemini Developer API
        : FirebaseAI.Backend.VertexAI();     // Vertex AI Gemini API

    return FirebaseAI.GetInstance(backend).GetGenerativeModel(modelName, ...);
}
```

## 4. 検証結果と制限事項

### 画像解析

Androidデバイスでの画像解析が動くことを確認しました。
下記はストリーミング分析機能の結果です。

https://x.com/fit51/status/1931198848569139235

### 画像生成

Androidデバイスでの画像生成を確認しましたが、現在(2025/6/9)は指定のモデルが使えないとのエラーメッセーが出てしまい動作確認はできませんでした。
Firebase.AI.GenerativeModel()を実行時に下記エラーメッセージが返ってきます。

```
System.Net.Http.HttpRequestException: HTTP request failed with status code: 400 (Bad Request).
Error Content: {
  "error": {
    "code": 400,
    "message": "Developer instruction is not enabled for models/gemini-2.0-flash-preview-image-generation",
    "status": "INVALID_ARGUMENT"
  }
}
```

### Imagen 3の対応状況

現在(2025/6/9)はUnity非対応です。

## 5. 実用的な活用例

### XRアプリケーションでの画像解析

```csharp
// AR カメラで撮影した画像を分析
public async Task AnalyzeCapturedImage(Texture2D cameraTexture) {
    var imageBytes = cameraTexture.EncodeToJPG();
    
    var content = new ModelContent[] {
        ModelContent.Text("この物体の名前と特徴を教えてください"),
        ModelContent.InlineData("image/jpeg", imageBytes)
    };
    
    var model = FirebaseAI.GetInstance(backend).GetGenerativeModel("gemini-2.0-flash");
    var response = await model.GenerateContentAsync(content);
    
    // AR UI に解析結果を表示
    ShowAnalysisResult(response.Text);
}
```

### 教育アプリでの画像解説

```csharp
// 教材画像の詳細解説
public async Task ExplainEducationalImage(Texture2D educationImage, string subject) {
    var content = new ModelContent[] {
        ModelContent.Text($"{subject}の観点から、この画像について詳しく解説してください"),
        ModelContent.InlineData("image/png", educationImage.EncodeToPNG())
    };
    
    var response = await model.GenerateContentAsync(content);
    DisplayEducationalExplanation(response.Text);
}
```

## まとめ

画像解析機能は即座に実用的なアプリケーションで利用することができそうです。

画像生成機能については現在(2025/6/9)制限がありますが、Firebase AI Logic for Unityの継続的な開発により、将来的な対応に期待したいと思います。

## 参考リンク

- [Firebase AI Logic 画像解析ガイド](https://firebase.google.com/docs/ai-logic/analyze-images)
- [Firebase AI Logic 画像生成ガイド](https://firebase.google.com/docs/ai-logic/generate-images-gemini)
- [Firebase Unity SDK サンプル](https://github.com/firebase/quickstart-unity/tree/master/firebaseai/testapp)