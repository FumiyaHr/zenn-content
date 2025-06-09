---
title: "Firebase AI Logic for Unity：Live API実装ガイド　リアルタイム音声対話を実現する"
emoji: "🎤"
type: "tech"
topics: ["firebase", "unity", "ai", "gemini", "live-api"]
published: false
publication_name: "hololab"
---

## はじめに

Firebase AI Logic for Unityシリーズ第4弾！今回は**Live API**にフォーカスして、リアルタイム音声対話機能の実装方法から継続会話の実現まで詳しく解説します。

前回までの記事：
- [第1弾：Firebase AI Logic for Unity発表！GeminiでXR体験を革新する](https://zenn.dev/hololab/articles/firebase-ai-logic-unity-androidxr)
- [第2弾：環境構築からサンプル実行まで](https://zenn.dev/hololab/articles/firebase-ai-logic-unity-sample-tutorial)
- [第3弾：画像生成・画像解析の実装と検証](https://zenn.dev/hololab/articles/firebase-ai-logic-unity-image-generation-analysis)

Live APIを使用することで、従来のリクエスト・レスポンス形式ではなく、**双方向リアルタイム通信**によるより自然な音声対話体験をUnityアプリケーションに統合できます。

## 本記事で扱う内容

### Live API機能
- **リアルタイム音声対話**: 低レイテンシーでの双方向音声通信
- **マルチモーダル対応**: テキスト・音声の同時入出力
- **ストリーミング処理**: WebSocketベースの継続的データ交換

### 実装上の課題と解決方法
- **継続会話の実現**: セッション切断対策と自動再接続
- **安定した音声対話システムの構築**

## Live APIの特徴と制限事項

### 対応プラットフォーム
現在のLive APIサポート状況：
- **サポート済み**: Android、Flutter、Unity
- **近日提供予定**: Apple プラットフォーム、Web アプリ

### APIバックエンド要件
**重要**: Live APIは**Vertex AI Gemini API**のみサポートされています。
- **対応**: Vertex AI Backend
- **近日提供予定**: Gemini Developer API

### マルチモーダル対応状況
- **現在対応**: テキスト、音声
- **近日提供予定**: 動画入力（期待していた機能ですが、まだ利用できません）

### 技術仕様
- **音声入力形式**: Raw 16 bit PCM audio at 16kHz little-endian
- **音声出力形式**: Raw 16 bit PCM audio at 24kHz little-endian
- **通信プロトコル**: WebSocket
- **推奨モデル**: `gemini-2.0-flash-live-preview-04-09`

### 利用制限
- **同時セッション数**: Firebaseプロジェクトあたり10セッション
- **トークン制限**: 毎分4Mトークン
- **デフォルトセッション長**: 30分
- **プレビュー版**: 後方互換性のない変更の可能性があります

## 検証環境

- **Unity**: 6000.0.44f1
- **Firebase Unity SDK**: 12.9.0
- **対象モデル**: `gemini-2.0-flash-live-preview-04-09`
- **プラットフォーム**: Android
- **音声設定**: Prebuilt Voice "Puck"

## 1. Live APIの基本実装

### LiveGenerativeModelの作成

Live APIを使用するための基本的なセットアップ：

```csharp
// Live APIはVertex AI Backendのみサポート
var backend = FirebaseAI.Backend.VertexAI();

// 音声専用レスポンス設定
var config = new LiveGenerationConfig(
  responseModalities: new[] { ResponseModality.Audio },
  speechConfig: SpeechConfig.UsePrebuiltVoice("Puck")
);

var model = FirebaseAI.GetInstance(backend).GetLiveModel(
  modelName: "gemini-2.0-flash-live-preview-04-09",
  liveGenerationConfig: config
);
```

### セッションの開始と音声ストリーミング

Live APIセッションの確立と双方向音声通信：

```csharp
// セッション開始
var session = await model.ConnectAsync();

// 音声データ送信（例：マイクロフォンから）
await session.SendAudioAsync(audioData);

// 音声レスポンス受信
await foreach (var response in session.ReceiveAsync())
{
  if (response.AudioAsFloat != null)
  {
    // 受信した音声データをスピーカーで再生
    foreach (var audioChunk in response.AudioAsFloat)
    {
      // audioChunkをAudioSourceで再生
      PlayAudioChunk(audioChunk);
    }
  }
  
  if (response.Text != null)
  {
    Debug.Log("AI Response: " + response.Text);
  }
}
```

### 完全なUnity実装例

```csharp
using Firebase.AI;
using System.Collections;
using UnityEngine;

public class LiveAudioChat : MonoBehaviour
{
  private LiveSession session;
  private AudioSource audioSource;
  private AudioClip microphoneClip;
  private bool isRecording = false;
  
  async void Start()
  {
    // Live APIモデル設定
    var backend = FirebaseAI.Backend.VertexAI();
    var config = new LiveGenerationConfig(
      responseModalities: new[] { ResponseModality.Audio },
      speechConfig: SpeechConfig.UsePrebuiltVoice("Puck")
    );
    
    var model = FirebaseAI.GetInstance(backend).GetLiveModel(
      modelName: "gemini-2.0-flash-live-preview-04-09",
      liveGenerationConfig: config
    );
    
    // セッション開始
    session = await model.ConnectAsync();
    
    // 音声レスポンス受信開始
    _ = ReceiveAudioAsync();
    
    // マイクロフォン録音開始
    StartRecording();
  }
  
  void StartRecording()
  {
    if (Microphone.devices.Length > 0)
    {
      microphoneClip = Microphone.Start(null, true, 60, 16000);
      isRecording = true;
      StartCoroutine(SendMicrophoneData());
    }
  }
  
  IEnumerator SendMicrophoneData()
  {
    int lastPosition = 0;
    float[] buffer = new float[1024];
    
    while (isRecording)
    {
      int currentPosition = Microphone.GetPosition(null);
      
      if (currentPosition != lastPosition)
      {
        int samplesToRead = currentPosition > lastPosition 
          ? currentPosition - lastPosition
          : microphoneClip.samples - lastPosition + currentPosition;
          
        if (samplesToRead > 0)
        {
          microphoneClip.GetData(buffer, lastPosition);
          
          // 音声データをLive APIに送信
          float[] audioData = new float[samplesToRead];
          System.Array.Copy(buffer, 0, audioData, 0, samplesToRead);
          _ = session.SendAudioAsync(audioData);
          
          lastPosition = currentPosition;
        }
      }
      
      yield return new WaitForSeconds(0.1f);
    }
  }
  
  async Task ReceiveAudioAsync()
  {
    await foreach (var response in session.ReceiveAsync())
    {
      if (response.AudioAsFloat != null)
      {
        foreach (var audioChunk in response.AudioAsFloat)
        {
          // 受信した音声をスピーカーで再生
          PlayAudioChunk(audioChunk);
        }
      }
    }
  }
  
  void PlayAudioChunk(float[] audioData)
  {
    // AudioSourceで音声データを再生する実装
    var clip = AudioClip.Create("Response", audioData.Length, 1, 24000, false);
    clip.SetData(audioData, 0);
    audioSource.clip = clip;
    audioSource.Play();
  }
}
```

## 2. 継続会話の課題と解決方法

### 発見した問題

Live APIの実装において、重要な課題を発見しました：

**問題**: 初回の音声対話は正常に動作するが、2回目以降の発話に対してAIが応答しない

**原因**: `liveSession.ReceiveAsync()`のストリームが、1回の応答後に予期せず終了している

### 解決方法：自動再接続機能

この問題を解決するため、基本実装に**セッション自動再接続機能**を追加します：

```csharp
public class LiveAudioChat : MonoBehaviour
{
  private LiveSession session;
  private AudioSource audioSource;
  private AudioClip microphoneClip;
  private bool isRecording = false;
  private bool isSessionActive = false;
  
  async Task ReceiveAudioAsync()
  {
    try
    {
      await foreach (var response in session.ReceiveAsync())
      {
        if (response.AudioAsFloat != null)
        {
          foreach (var audioChunk in response.AudioAsFloat)
          {
            PlayAudioChunk(audioChunk);
          }
        }
      }
      
      // ReceiveAsyncループが予期せず終了した場合の処理
      Debug.Log("ReceiveAsync loop ended - attempting to reconnect");
      
      if (isSessionActive)
      {
        await ReconnectSession();
      }
    }
    catch (System.Exception ex)
    {
      Debug.LogError("Error in ReceiveAudioAsync: " + ex.Message);
      
      // エラー時も再接続を試行
      if (isSessionActive)
      {
        await ReconnectSession();
      }
    }
  }
  
  async Task ReconnectSession()
  {
    try
    {
      Debug.Log("Reconnecting Live API session...");
      
      // 新しいモデルインスタンスで再接続
      var backend = FirebaseAI.Backend.VertexAI();
      var config = new LiveGenerationConfig(
        responseModalities: new[] { ResponseModality.Audio },
        speechConfig: SpeechConfig.UsePrebuiltVoice("Puck")
      );
      
      var model = FirebaseAI.GetInstance(backend).GetLiveModel(
        modelName: "gemini-2.0-flash-live-preview-04-09",
        liveGenerationConfig: config
      );
      
      session = await model.ConnectAsync();
      Debug.Log("Live session reconnected successfully");
      
      // 音声レスポンス受信を再開
      _ = ReceiveAudioAsync();
    }
    catch (System.Exception ex)
    {
      Debug.LogError("Failed to reconnect Live API session: " + ex.Message);
      isSessionActive = false;
      isRecording = false;
    }
  }
}
```

### 実装のポイント

1. **ReceiveAsyncループの監視**: 正常終了を検出して自動再接続
2. **例外処理での再接続**: エラー発生時も再接続を試行
3. **セッション状態の確認**: `isSessionActive`で不要な再接続を防止
4. **失敗時の安全停止**: 再接続に失敗した場合は完全にセッションを停止

### 完全な実装例（再接続機能付き）

基本実装に再接続機能を統合した完全版：

```csharp
using Firebase.AI;
using System.Collections;
using UnityEngine;

public class LiveAudioChat : MonoBehaviour
{
  private LiveSession session;
  private AudioSource audioSource;
  private AudioClip microphoneClip;
  private bool isRecording = false;
  private bool isSessionActive = false;
  
  async void Start()
  {
    await InitializeSession();
  }
  
  async Task InitializeSession()
  {
    // Live APIモデル設定
    var backend = FirebaseAI.Backend.VertexAI();
    var config = new LiveGenerationConfig(
      responseModalities: new[] { ResponseModality.Audio },
      speechConfig: SpeechConfig.UsePrebuiltVoice("Puck")
    );
    
    var model = FirebaseAI.GetInstance(backend).GetLiveModel(
      modelName: "gemini-2.0-flash-live-preview-04-09",
      liveGenerationConfig: config
    );
    
    // セッション開始
    session = await model.ConnectAsync();
    isSessionActive = true;
    
    // 音声レスポンス受信開始（再接続機能付き）
    _ = ReceiveAudioAsync();
    
    // マイクロフォン録音開始
    StartRecording();
  }
  
  // 再接続機能付きの音声受信
  async Task ReceiveAudioAsync()
  {
    try
    {
      await foreach (var response in session.ReceiveAsync())
      {
        if (response.AudioAsFloat != null)
        {
          foreach (var audioChunk in response.AudioAsFloat)
          {
            PlayAudioChunk(audioChunk);
          }
        }
      }
      
      // ReceiveAsyncループが終了した場合の自動再接続
      if (isSessionActive)
      {
        await ReconnectSession();
      }
    }
    catch (System.Exception ex)
    {
      Debug.LogError("Error in ReceiveAudioAsync: " + ex.Message);
      
      if (isSessionActive)
      {
        await ReconnectSession();
      }
    }
  }
  
  async Task ReconnectSession()
  {
    try
    {
      Debug.Log("Reconnecting Live API session...");
      
      var backend = FirebaseAI.Backend.VertexAI();
      var config = new LiveGenerationConfig(
        responseModalities: new[] { ResponseModality.Audio },
        speechConfig: SpeechConfig.UsePrebuiltVoice("Puck")
      );
      
      var model = FirebaseAI.GetInstance(backend).GetLiveModel(
        modelName: "gemini-2.0-flash-live-preview-04-09",
        liveGenerationConfig: config
      );
      
      session = await model.ConnectAsync();
      Debug.Log("Live session reconnected successfully");
      
      // 音声レスポンス受信を再開
      _ = ReceiveAudioAsync();
    }
    catch (System.Exception ex)
    {
      Debug.LogError("Failed to reconnect: " + ex.Message);
      isSessionActive = false;
      isRecording = false;
    }
  }
  
  void StartRecording()
  {
    if (Microphone.devices.Length > 0)
    {
      microphoneClip = Microphone.Start(null, true, 60, 16000);
      isRecording = true;
      StartCoroutine(SendMicrophoneData());
    }
  }
  
  IEnumerator SendMicrophoneData()
  {
    int lastPosition = 0;
    float[] buffer = new float[1024];
    
    while (isRecording && isSessionActive)
    {
      int currentPosition = Microphone.GetPosition(null);
      
      if (currentPosition != lastPosition)
      {
        int samplesToRead = currentPosition > lastPosition 
          ? currentPosition - lastPosition
          : microphoneClip.samples - lastPosition + currentPosition;
          
        if (samplesToRead > 0)
        {
          microphoneClip.GetData(buffer, lastPosition);
          
          float[] audioData = new float[samplesToRead];
          System.Array.Copy(buffer, 0, audioData, 0, samplesToRead);
          _ = session.SendAudioAsync(audioData);
          
          lastPosition = currentPosition;
        }
      }
      
      yield return new WaitForSeconds(0.1f);
    }
  }
  
  void PlayAudioChunk(float[] audioData)
  {
    var clip = AudioClip.Create("Response", audioData.Length, 1, 24000, false);
    clip.SetData(audioData, 0);
    audioSource.clip = clip;
    audioSource.Play();
  }
}
```

## 3. 実用的な応用例

### XR環境での音声ガイド

```csharp
var systemInstruction = ModelContent.Text(
  "You are a virtual museum guide in a VR environment. " +
  "Provide detailed explanations about artworks and exhibits. " +
  "Respond in Japanese for Japanese speakers."
);
```

### インタラクティブなNPC

```csharp
var systemInstruction = ModelContent.Text(
  "You are a medieval innkeeper in a fantasy game. " +
  "Welcome travelers and provide information about the inn and local area. " +
  "Keep responses concise but immersive."
);
```

### 音声アシスタント

```csharp
var systemInstruction = ModelContent.Text(
  "You are a helpful voice assistant for Unity developers. " +
  "Provide technical guidance and coding advice. " +
  "Always respond with audio output."
);
```

## まとめ

Firebase AI Logic for UnityのLive APIにより、リアルタイム音声対話機能をUnityアプリケーションに統合できます。しかし、実装には以下の重要なポイントがあります：

### 成功のキーポイント

1. **自動再接続機能**: セッション切断に対する堅牢な対策
2. **Vertex AI Backend必須**: 現時点ではVertex AI以外は非対応

### 今後の展望

- **Gemini Developer API対応**: より手軽な開発環境の提供予定
- **動画入力サポート**: マルチモーダル体験のさらなる拡充
- **Apple・Web対応**: プラットフォーム対応の拡大

Live APIは現在プレビュー版ですが、XRやゲーム開発において音声によるナチュラルなインタラクションを実現する強力なツールです。今後の展望に期待します。

---

**参考リンク**
- [Firebase AI Logic Live API 公式ドキュメント](https://firebase.google.com/docs/ai-logic/live-api)
- [Firebase Unity SDK リファレンス](https://firebase.google.com/docs/reference/unity/)
- [本プロジェクトのGitHubリポジトリ](https://github.com/Fumiya-Hori-HL/FirebaseAILogic-Unity)