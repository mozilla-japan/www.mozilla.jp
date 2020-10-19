---
type: business
layout: tech-faq-article
title: ユーザーを惑わすメッセージを表示させたくない
weight: 7
---

## 初回起動時の設定移行ウィザードを表示させたくない

キーワード：導入時初期設定

Firefox や Thunderbird の初回起動時に表示される `設定移行ウィザード` (他のアプリケーションの設定を引き継ぐためのウィザード) は無効化することができます。

### 設定方法

`override.ini` という名前で以下の内容のテキストファイルを作成し、Firefox であればインストール先ディレクトリ内の `browser` ディレクトリ内 (Windows であれば、`C:\Program Files\Mozilla Firefox\browser\override.ini` など) 、Thunderbird であればインストール先ディレクトリ直下 (Windows であれば、`C:\Program Files\Mozilla Thunderbird\override.ini` など) に置きます。

    [XRE]
    EnableProfileMigrator=0

## アップデート後の「お使いの Firefox は最新版に更新されました」タブを表示させたくない

キーワード：導入時初期設定

Firefoxを 更新した後の初回起動時に表示される「お使いの Firefox は最新版に更新されました」タブは、設定で無効化することができます。

### 設定方法

[グループポリシー](../setting-management/#group-policy) または [ポリシー定義ファイル](../setting-management/#policies-json) を用いて、[`OverridePostUpdatePage`](https://github.com/mozilla/policy-templates/blob/master/README.md#overridepostupdatepage) を空文字に設定してください。例えば以下の要領です。

    {
      "policies": {
        "OverridePostUpdatePage": ""
      }
    }

## アップデート後の「Thunderbird へようこそ」 (新着情報) タブを表示させたくない

キーワード：導入時初期設定

Thunderbird を更新した後の初回起動時に表示される「Thunderbird へようこそ」タブは、設定で無効化することができます。

### 設定方法

[MCD (AutoConfig)](../setting-management/#mcd) を用いて以下の通り設定してください。

    clearPref("app.update.postupdate");

上記の設定は、設定値の内容に関わらず、ユーザー設定値が保存されていると「Thunderbird へようこそ」タブが開かれるという仕様になっています。そのため、明示的に `false` を指定する代わりにユーザー設定値を消去する必要があります。

## パフォーマンス情報の自動送信に関するメッセージを表示させたくない

キーワード：導入時初期設定

Firefox や Thunderbird の初回起動時などに表示される「Mozilla Firefox (Thunderbird) の改善のため、メモリ消費量、パフォーマンス、応答速度を自動的に Mozilla に送信しますか？」のメッセージは、設定で無効化することができます。

### 設定方法

[MCD (AutoConfig)](../setting-management/#mcd) を用いて以下の通り設定してください。設定名は Firefox と Thunderbird で共通です。

    if (typeof getPref("toolkit.telemetry.prompted") == "boolean")
      clearPref("toolkit.telemetry.prompted");
    lockPref("toolkit.telemetry.prompted", 2);

上記のメッセージが表示された際に「いいえ」を選択した状態にしたい場合 (パフォーマンス情報の自動送信を禁止したい場合) は、以下も併せて指定します。

    lockPref("toolkit.telemetry.enabled", false);
    lockPref("toolkit.telemetry.rejected", true);

<!--

## ダウンロード完了の通知を表示させたくない (未稿)

  autoconfig
旧ダウンロードマネージャが廃止されたので、これはこのままでは書けない気がする。
何のために通知を表示させたくないのか、ということを汲み取って、新しいUIでその目的を達成するためのカスタマイズを考える必要がある。
-->

## タブを閉じようとしたときの警告を表示させたくない

キーワード：導入時初期設定

Firefox でウィンドウや複数のタブを一度に閉じようとした時に表示される、本当に閉じてもよいかどうかの確認ダイアログは、設定で無効化することができます。

### 設定方法

[MCD (AutoConfig)](../setting-management/#mcd) を用いて以下の通り設定してください。

    // 複数のタブを開いた状態でウィンドウを閉じようとした時の確認を表示しない
    lockPref("browser.tabs.warnOnClose", false);
    // 「他のタブを閉じる」で複数のタブを一度に閉じようとした時の確認を表示しない
    lockPref("browser.tabs.warnOnCloseOtherTabs", false);
