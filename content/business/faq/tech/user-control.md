---
type: business
layout: tech-faq-article
title: ユーザーが使える機能を制限したい
weight: 5
---

## 一部のキーボードショートカットを無効化したい {#disable-keyboard-shortcuts}

キーワード：機能制限、導入時初期設定、集中管理

現在のバージョンの Firefox はキーボードショートカットを管理する機能を含んでいません。ポリシー設定で機能を無効化することによって、その機能に紐付くキーボードショートカットを無効化することはできます。

### 設定方法

[グループポリシー](../setting-management/#group-policy) または [ポリシー定義ファイル](../setting-management/#policies-json) を用いて、対応する機能を無効化してください。例えば以下の要領です。

* ファイル＞新しいプライベートウィンドウを開く (Ctrl-Shift-P): [`DisablePrivateBrowsing`](https://github.com/mozilla/policy-templates/blob/master/README.md#disableprivatebrowsing) を `true` に設定
* ツール＞ウェブ開発 (開発ツールボタン)  およびその配下の機能: [`DisableDeveloperTools`](https://github.com/mozilla/policy-templates/blob/master/README.md#disabledevelopertools) を `true` に設定

## 一部のメニュー項目やツールバーボタンなどの UI 要素を非表示にしたい {#hide-ui-elements}

キーワード：機能制限、導入時初期設定、集中管理

現在のバージョンの Firefox は特定の UI 要素の表示・非表示を管理する機能を原則として含んでいません。ポリシー設定で機能を無効化することによって、その機能に紐付く UI を無効化することはできます。

### 設定方法

[グループポリシー](../setting-management/#group-policy) または [ポリシー定義ファイル](../setting-management/#policies-json) を用いて、対応する機能を無効化してください。例えば以下の要領です。

* ファイル＞新しいプライベートウィンドウを開く (Ctrl-Shift-P): [`DisablePrivateBrowsing`](https://github.com/mozilla/policy-templates/blob/master/README.md#disableprivatebrowsing) を `true` に設定
* ツール＞ウェブ開発 (開発ツールボタン)  およびその配下の機能: [`DisableDeveloperTools`](https://github.com/mozilla/policy-templates/blob/master/README.md#disabledevelopertools) を `true` に設定
* ヘルプ＞トラブルシューティング情報: [`BlockAboutSupport`](https://github.com/mozilla/policy-templates/blob/master/README.md#blockaboutsupport) を `true` に設定
* ヘルプ＞フィードバックを送信: [`DisableFeedbackCommands`](https://github.com/mozilla/policy-templates/blob/master/README.md#disablefeedbackcommands) を `true` に設定
* ヘルプ＞アドオンを無効にして再起動: [`DisableSafeMode`](https://github.com/mozilla/policy-templates/blob/master/README.md#disablesafemode) を `true` に設定
* コンテンツ領域のコンテキストメニュー＞デスクトップの背景に設定: [`DisableSetDesktopBackground`](https://github.com/mozilla/policy-templates/blob/master/README.md#disablesetdesktopbackground) を `true` に設定
* Firefox Sync: [`DisableFirefoxAccounts`](https://github.com/mozilla/policy-templates/blob/master/README.md#disablefirefoxaccounts) を `true` に設定
* Pocket: [`DisablePocket`](https://github.com/mozilla/policy-templates/blob/master/README.md#disablepocket) を `true` に設定

## プライベートブラウジング機能を使わせたくない

キーワード：機能制限、情報漏洩対策

プライベートブラウジング機能へのアクセス経路を無効化することで、ユーザーのプライベートブラウジング機能の利用を禁止できます。

### 設定方法

[グループポリシー](../setting-management/#group-policy) または [ポリシー定義ファイル](../setting-management/#policies-json) を用いて、[`DisablePrivateBrowsing`](https://github.com/mozilla/policy-templates/blob/master/README.md#disableprivatebrowsing) を `true` に設定してください。例えば以下の要領です。

    {
      "policies": {
        "DisablePrivateBrowsing": true
      }
    }

## Firefox Sync を使わせたくない

キーワード：機能制限、導入時初期設定、集中管理

無用なトラブルや情報の流出を避けるため、ユーザーが任意に Firefox Sync をセットアップできないよう設定することができます。

### 設定方法

[グループポリシー](../setting-management/#group-policy) または [ポリシー定義ファイル](../setting-management/#policies-json) を用いて、[`DisableFirefoxAccounts`](https://github.com/mozilla/policy-templates/blob/master/README.md#disablefirefoxaccounts) を `true` に設定してください。例えば以下の要領です。

    {
      "policies": {
        "DisableFirefoxAccounts": true
      }
    }
