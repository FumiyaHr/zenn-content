---
title: "UI Toolkit インスペクター拡張 ライブリロード"
emoji: "🫥"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["unity", "UIToolkit"]
published: true
---

## 1. UI Toolkitとは

> UI Toolkitは、ユーザーインターフェース（UI）を開発するための機能、リソース、ツールのコレクションです。UI
> Toolkitを使用して、Unity Editor用のカスタムUIや拡張機能、ランタイムデバッグツール、ゲームやアプリケーションのランタイムUIを開発できます。
> 
> UI Toolkitは、標準的なWeb技術に触発されています。Webページやアプリケーションを開発した経験があれば、知識は移行可能であり、コアコンセプトはなじみがあります。

[Unity - Manual: UI Toolkit](https://docs.unity3d.com/2022.3/Documentation/Manual/UIElements.html) より

## 2. できるもの
UI Toolkit でインスペクター拡張を作成するとき、標準では UI Builder で UXML を変更してもインスペクターに反映されない。
ライブリロードをオンにすることによって、UI Builder での変更がリアルタイムにインスペクターに反映されるようになる。

## 3. 環境
- Unity 2022.3.5f1

## 4.方法

Inspector の設定から `UI Toolkit Live Reload`
![](/images/articles/uitoolkit-live-reload/1.png =300x)