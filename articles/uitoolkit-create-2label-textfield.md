---
title: "UIToolkit Labelが2つのLabelFieldテンプレート"
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

IMGUIのLabelFiledでLabelを2つ指定した時と似た表示のコントロール

![](/images/articles/uitoolkit-create-2label-textfield/1.png)

## 3. 環境
- Unity 2022.3.5f1

## 4. 方法

### 4.1. UXML
IMGUIのLabelが2つのLabelFieldと似たコントロールのテンプレート

```uxml
<ui:UXML xmlns:ui="UnityEngine.UIElements" xmlns:uie="UnityEditor.UIElements" xsi="http://www.w3.org/2001/XMLSchema-instance" engine="UnityEngine.UIElements" editor="UnityEditor.UIElements" noNamespaceSchemaLocation="../../UIElementsSchema/UIElements.xsd" editor-extension-mode="True">
    <ui:VisualElement name="VisualElement" style="flex-direction: row;">
        <ui:Label tabindex="-1" text="Label" display-tooltip-when-elided="true" name="label" style="flex-grow: 1; max-width: 127px; min-width: 127px; padding-left: 4px;" />
        <ui:Label tabindex="-1" text="Label" display-tooltip-when-elided="true" name="label2" style="flex-grow: 1;" />
    </ui:VisualElement>
</ui:UXML>
```

### 4.2. IMGUIとの比較
IMGUIのLabelが2つのLabelFiledと比較します。
また標準のTextFieldも比較として並べます。

#### UXMLで作成したLabelField
![](/images/articles/uitoolkit-create-2label-textfield/1.png)

#### IMGUIのLabelField
![](/images/articles/uitoolkit-create-2label-textfield/2.png)

※ソースコード `EditorGUILayout.LabelField("label", "label2");`