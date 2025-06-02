---
title: "Firebase AI Logic for Unity実践ガイド：環境構築からサンプル実行まで"
emoji: "🚀"
type: "tech"
topics: ["firebase", "unity", "ai", "xr", "gemini"]
published: true
publication_name: "hololab"
---

## はじめに

前回の記事「[Firebase AI Logic for Unity発表！XR体験にGeminiを](https://zenn.dev/hololab/articles/firebase-ai-logic-unity-androidxr)」では、Firebase AI Logic for Unityの概要と可能性についてお伝えしました。

今回は実際に手を動かして、環境構築からサンプルアプリケーションの実行まで、一歩ずつ進めていきます。公式ドキュメントとGitHubのサンプルコードを参考にお伝えします。

## サンプルアプリケーションで体験できること

まず、Firebase AI Logic for Unityのサンプルアプリケーションで何ができるのかを紹介します。

### 2つのAI バックエンドの比較体験

サンプルでは、同じUnityアプリケーション内で2つの異なるAIバックエンドを切り替えて使用できます：

- **Google AI Backend（Gemini Developer API）**
  - 無料枠：1日1,500リクエスト、月100万トークン
  - プロトタイピングや個人開発に最適
  - APIキー認証で簡単にスタート

- **Vertex AI Backend（Vertex AI Gemini API）**
  - エンタープライズグレードの信頼性とスケーラビリティ
  - 詳細な使用量分析とガバナンス機能
  - 本格的なプロダクション環境向け

### 2つの会話モード

#### 1. 単発メッセージモード
```
入力: "この建物の情報を教えてください"
↓
Gemini: "こちらは東京駅の丸の内駅舎です。1914年に建設され..."
```

- 会話履歴を保持しない一回限りのやり取り
- XR空間での情報案内や解説に適している
- レスポンスが早く、観光案内や建物解説などに最適

#### 2. チャットセッションモード  
```
ユーザー: "バーチャル美術館を案内してください"
Gemini: "ようこそバーチャル美術館へ！まずは印象派の展示室から..."

ユーザー: "モネの作品について詳しく聞かせてください"
Gemini: "先ほどご案内した印象派のコーナーにあるモネですが..."
```

- 会話履歴を自動的に保持
- 文脈を理解した自然な対話が可能
- XRガイドやバーチャルアシスタントとの継続的な会話に最適

## 検証環境

- **Unity**: Unity 6000.0.44f1
- **OS**: macOS Sequoia 15.5
- **Firebase Unity SDK**: 12.9.0（Firebase AI Logic SDK含む）
- **対象プラットフォーム**: Android（エディター内テストも含む）

## 1. 事前準備

### 必要なツール

- **Unity 2021.3 LTS以降**（今回は6000.0.44f1を使用）
- **Firebaseプロジェクト**
- **Android SDK**（デバイス向けビルド時）

### 作業の全体的な流れ

1. **Firebase プロジェクトの設定**（Firebase Console）
2. **サンプルプロジェクトの取得**（GitHub）
3. **Firebase SDK のインポート**（Unity）
4. **設定ファイルの配置**（google-services.json）
5. **サンプル実行と動作確認**（Unity Editor + Android）

それでは順番に進めていきます。

## 2. Firebase プロジェクトの設定

まず、[Firebase Console](https://console.firebase.google.com/)でプロジェクトを作成します。

1. **Firebase プロジェクトの作成**
   - Firebase Consoleで新規プロジェクトを作成
   - プロジェクト名を設定（例：`firebase-ai-unity-test`）

2. **Firebase AI Logic の有効化**
   - 左サイドバーから「AI Logic」を選択
   - 「使ってみる」をクリックして設定ワークフローを開始
   - **Gemini Developer API**を選択（無料枠で試用可能）

3. **Androidアプリの登録**
   - プロジェクト設定でAndroidアプリを追加
   - パッケージ名：`com.google.firebase.unity.firebaseai.testapp`
       - サンプルのパッケージ名
   - `google-services.json`をダウンロード

## 3. Unityプロジェクトの準備

### サンプルプロジェクトの取得

Firebase Unity SDKの公式サンプルを使用します：

```bash
git clone https://github.com/firebase/quickstart-unity.git
cd quickstart-unity/firebaseai/testapp
```

### Unityでプロジェクトを開く

1. Unity Hubで「プロジェクトを開く」
2. `quickstart-unity/firebaseai/testapp`フォルダを選択
3. Unityのバージョン変換が求められた場合は「確認」をクリック

### Firebase SDK のインポート

1. [Firebase Unity SDK](https://firebase.google.com/download/unity)をダウンロード
2. Unity Editor で **Assets > Import Package > Custom Package**
3. ダウンロードした`FirebaseAI.unitypackage`を選択
4. Import Dialogで「Import」をクリック

### 設定ファイルの配置

ダウンロードした`google-services.json`を配置します：

1. Projectウィンドウで`Assets/Firebase/Sample/FirebaseAI`フォルダに移動
2. `google-services.json`をドラッグ&ドロップ

## 4. サンプルアプリケーションの概要

### UIHandler.cs の構成

サンプルの核となる`UIHandler.cs`を見てみます：

```csharp
namespace Firebase.Sample.FirebaseAI {
  using Firebase;
  using Firebase.AI;
  using Firebase.Extensions;
  using System.Threading.Tasks;
  using UnityEngine;

  public class UIHandler : MonoBehaviour {
    // Geminiモデルの設定
    public string ModelName = "gemini-2.0-flash";
    
    // バックエンド選択（Google AI / Vertex AI）
    private int backendSelection = 0;
    private string[] backendChoices = new string[] { 
      "Google AI Backend", 
      "Vertex AI Backend" 
    };
```

### 重要なメソッド解説

#### 1. モデル取得メソッド

```csharp
private GenerativeModel GetModel() {
  var backend = backendSelection == 0
      ? FirebaseAI.Backend.GoogleAI()      // Gemini Developer API
      : FirebaseAI.Backend.VertexAI();     // Vertex AI Gemini API

  return FirebaseAI.GetInstance(backend).GetGenerativeModel(ModelName);
}
```

このメソッドでは、ユーザーの選択に応じて2つのAPIバックエンドを切り替えられます。

#### 2. 単発メッセージ送信

```csharp
async Task SendSingleMessage(string message) {
  DebugLog("Sending message to model: " + message);
  var response = await GetModel().GenerateContentAsync(message);
  DebugLog("Response: " + response.Text);
}
```

会話履歴を保持しない、一回限りのやり取りです。

#### 3. チャットセッション管理

```csharp
private Chat chatSession = null;

void StartChatSession() {
  chatSession = GetModel().StartChat();
}

async Task SendChatMessage(string message) {
  if (chatSession == null) {
    DebugLog("Missing Chat Session");
    return;
  }

  DebugLog("Sending chat message: " + message);
  var response = await chatSession.SendMessageAsync(message);
  DebugLog("Chat response: " + response.Text);
}
```

チャットセッションを使用することで、会話の文脈を保持できます。

## 5. 実際の実行手順

### エディター内での実行

1. **MainSceneを開く**
   - `Assets/Firebase/Sample/FirebaseAI/MainScene`をダブルクリック

2. **実行テスト**
   - Playボタンを押してゲームを開始
   - UIが表示され、以下の操作が可能：
     - バックエンド選択（Google AI / Vertex AI）
     - テキスト入力
     - 単発メッセージ送信
     - チャットセッション開始/終了

### 実際の動作確認

https://x.com/fit51/status/1928629488394404202

#### 単発メッセージモードの検証

1. "Google AI Backend"を選択
2. テキストフィールドに "上田城について教えて" と入力
3. "Send Single Message"をクリック
4. ログエリアにGeminiからの応答が表示

```
Sending message to model: 上田城について教えて
Response: 上田城についてですね！かしこまりました。上田城は、長野県上田市にあるお城で、...
```

#### チャットセッションモードの検証

1. "Start Chat Session"をクリック
2. "バーチャルガイドとして案内してください"と入力
3. "Send Chat Message"をクリック
4. 続けて"上田城について教えて"を送信
5. 前の会話を踏まえた返答を確認

## 6. 実用的なカスタマイズ

### システム指示の追加

XRガイドやバーチャルアシスタントのような用途であれば、システム指示を設定できます：

```csharp
var model = FirebaseAI.GetInstance(backend).GetGenerativeModel(
  modelName: "gemini-2.0-flash",
  systemInstruction: ModelContent.Text(
    "You are a knowledgeable virtual museum guide in an XR environment. " +
    "Provide detailed explanations about exhibits, answer questions about art history, " +
    "and help visitors navigate the virtual space."
  )
);
```

## まとめ

Firebase AI Logic for Unityを使用することで、複雑なバックエンド構築なしに、Unityアプリケーション内でGeminiの高度なAI機能を活用できることを確認できました。

特に、テキストベースの会話機能であれば、わずか数行のコードで実装できる手軽さは大きな魅力です。

## 参考リンク

- [Firebase AI Logic公式ドキュメント](https://firebase.google.com/docs/ai-logic/get-started)
- [Firebase Unity SDK GitHub](https://github.com/firebase/quickstart-unity/tree/master/firebaseai/testapp)
