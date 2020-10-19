---
type: business
layout: tech-faq-article
title: 情報漏洩対策
weight: 4
---

## 社外サイトへアクセスする機能を全て無効化したい

キーワード：機能制限、導入時初期設定、集中管理、情報漏洩対策

Firefox にはネットワーク上のサーバーと連携する機能が多数含まれています。情報漏洩対策その他の理由から外部ネットワークへの意図しない通信を行わないようにしたい場合には、各機能を無効化することができます。

ただし、以下の設定は行動収集スクリプト (トラッキングスクリプト) のブロックのためのブロックリストを取得する動作も無効化します。現在の Web では行動収集スクリプトに起因する通信量が増大しており、Mozilla が提供するブロックリストを使用して通信を遮断した方が、結果的にバックグラウンドでの通信量が減る (ブロックリストの取得のための通信を無効化すると、通信量が却って増大する) という可能性も考えられます。Firefox 自体が行うバックグラウンドでの通信をどこまで無効化するかについては慎重に判断することが推奨されます。

### 設定方法

全ての外部向け通信を無効化するためには、[グループポリシー](../setting-management/#group-policy) または [ポリシー定義ファイル](../setting-management/#policies-json)と、[MCD (AutoConfig)](../setting-management/#mcd) の両方を組み合わせる必要があります。
ポリシー定義ファイルの設定例は以下の通りです。

    {
      "policies": {
        "DisableAppUpdate": true,
        "ExtensionUpdate": false,
        "BlockAboutAddons": true,
        "InstallAddonsPermission": {
          "Default": false
        },
        "CaptivePortal": false,
        "SearchSuggestEnabled": false,
        "DisableTelemetry": true,
        "DisableFirefoxAccounts": true,
        "EnableTrackingProtection": {
          "Value": false,
          "Locked": true
        },
        "OverrideFirstRunPage": ""
      }
    }

MCD 用設定ファイルの設定例は以下の通りです。

    // 攻撃サイトに対する警告の可否: しない
    lockPref("browser.safebrowsing.malware.enabled", false);
    lockPref("browser.safebrowsing.downloads.remote.url", "");
    lockPref("browser.safebrowsing.provider.google.advisoryURL", "");
    lockPref("browser.safebrowsing.provider.google.gethashURL", "");
    lockPref("browser.safebrowsing.provider.google.reportMalwareMistakeURL", "");
    lockPref("browser.safebrowsing.provider.google.reportPhishMistakeURL", "");
    lockPref("browser.safebrowsing.provider.google.reportURL", "");
    lockPref("browser.safebrowsing.provider.google.updateURL", "");
    lockPref("browser.safebrowsing.provider.google4.advisoryURL", "");
    lockPref("browser.safebrowsing.provider.google4.gethashURL", "");
    lockPref("browser.safebrowsing.provider.google4.reportMalwareMistakeURL", "");
    lockPref("browser.safebrowsing.provider.google4.reportPhishMistakeURL", "");
    lockPref("browser.safebrowsing.provider.google4.reportURL", "");
    lockPref("browser.safebrowsing.provider.google4.updateURL", "");
    lockPref("browser.safebrowsing.provider.mozilla.gethashURL", "");
    lockPref("browser.safebrowsing.provider.mozilla.updateURL", "");
    lockPref("browser.safebrowsing.reportMalwareMistakeURL", "");
    lockPref("browser.safebrowsing.reportPhishMistakeURL", "");
    lockPref("browser.safebrowsing.reportPhishURL", "");
    // ツールバーカスタマイズにおける、「その他のテーマを入手」の遷移の可否: 禁止する
    lockPref("lightweightThemes.getMoreURL", "");
    // ツールバーカスタマイズにおける、テーマの「おすすめ」の取得の可否: 禁止する
    lockPref("lightweightThemes.recommendedThemes", "");
    // 危険なアドオンとプラグインのブロックの可否: ブロックしない
    lockPref("extensions.blocklist.enabled", false);
    lockPref("extensions.blocklist.detailsURL", "");
    lockPref("extensions.blocklist.itemURL", "");
    lockPref("extensions.blocklist.url", "");
    // プロトコルごとの外部 Web アプリケーションへの連携: 使用しない
    // Web フィード用のサービス
    lockPref("browser.contentHandlers.types.0.name", "");
    lockPref("browser.contentHandlers.types.0.uri", "");
    lockPref("browser.contentHandlers.types.1.name", "");
    lockPref("browser.contentHandlers.types.1.uri", "");
    pref("browser.contentHandlers.types.2.name", "");
    pref("browser.contentHandlers.types.2.uri", "");
    pref("browser.contentHandlers.types.3.name", "");
    pref("browser.contentHandlers.types.3.uri", "");
    pref("browser.contentHandlers.types.4.name", "");
    pref("browser.contentHandlers.types.4.uri", "");
    pref("browser.contentHandlers.types.5.name", "");
    pref("browser.contentHandlers.types.5.uri", "");
    // IRC
    lockPref("gecko.handlerService.schemes.irc.0.name", "");
    lockPref("gecko.handlerService.schemes.irc.0.uriTemplate", "");
    pref("gecko.handlerService.schemes.irc.1.name", "");
    pref("gecko.handlerService.schemes.irc.1.uriTemplate", "");
    pref("gecko.handlerService.schemes.irc.2.name", "");
    pref("gecko.handlerService.schemes.irc.2.uriTemplate", "");
    pref("gecko.handlerService.schemes.irc.3.name", "");
    pref("gecko.handlerService.schemes.irc.3.uriTemplate", "");
    lockPref("gecko.handlerService.schemes.ircs.0.name", "");
    lockPref("gecko.handlerService.schemes.ircs.0.uriTemplate", "");
    pref("gecko.handlerService.schemes.ircs.1.name", "");
    pref("gecko.handlerService.schemes.ircs.1.uriTemplate", "");
    pref("gecko.handlerService.schemes.ircs.2.name", "");
    pref("gecko.handlerService.schemes.ircs.2.uriTemplate", "");
    pref("gecko.handlerService.schemes.ircs.3.name", "");
    pref("gecko.handlerService.schemes.ircs.3.uriTemplate", "");
    // メール
    lockPref("gecko.handlerService.schemes.mailto.0.name", "");
    lockPref("gecko.handlerService.schemes.mailto.0.uriTemplate", "");
    lockPref("gecko.handlerService.schemes.mailto.1.name", "");
    lockPref("gecko.handlerService.schemes.mailto.1.uriTemplate", "");
    pref("gecko.handlerService.schemes.mailto.2.name", "");
    pref("gecko.handlerService.schemes.mailto.2.uriTemplate", "");
    pref("gecko.handlerService.schemes.mailto.3.name", "");
    pref("gecko.handlerService.schemes.mailto.3.uriTemplate", "");
    // カレンダー
    lockPref("gecko.handlerService.schemes.webcal.0.name", "");
    lockPref("gecko.handlerService.schemes.webcal.0.uriTemplate", "");
    pref("gecko.handlerService.schemes.webcal.1.name", "");
    pref("gecko.handlerService.schemes.webcal.1.uriTemplate", "");
    pref("gecko.handlerService.schemes.webcal.2.name", "");
    pref("gecko.handlerService.schemes.webcal.2.uriTemplate", "");
    pref("gecko.handlerService.schemes.webcal.3.name", "");
    pref("gecko.handlerService.schemes.webcal.3.uriTemplate", "");
    // ファイルのダウンロード保護により、危険なソフトウェアのインストールをブロックする: ブロックしない
    lockPref("browser.safebrowsing.downloads.enabled", false);
    // ファイルのダウンロード保護により、不要なソフトウェアのインストールの可能性がある場面で警告する: 警告しない
    lockPref("browser.safebrowsing.downloads.remote.block_potentially_unwanted", false);
    lockPref("browser.safebrowsing.downloads.remote.block_uncommon", false);
    // 危険ででしゃばりな Flash コンテンツをブロック: ブロックしない
    lockPref("plugins.flashBlock.enabled", false);
    // 過去にデータ流出事故を起こした Web サービスでの警告の表示: 警告を表示しない
    lockPref("extensions.fxmonitor.enabled", false);
    // リンク先の先読みの可否: 禁止する
    lockPref("network.prefetch-next", false);
    // 検索結果のローカライズ用地域コードの位置情報に基づく推定の可否: 禁止する
    lockPref("browser.search.geoip.url", "");
    // Gecko Media Plugin の利用の可否: 禁止する
    lockPref("media.eme.enabled",false);
    lockPref("media.gmp-eme-adobe.enabled",false);
    lockPref("media.gmp-eme-adobe.autoupdate",false);
    lockPref("media.gmp-gmpopenh264.enabled",false);
    lockPref("media.gmp-gmpopenh264.autoupdate",false);
    lockPref("media.gmp-widevinecdm.enabled",false);
    lockPref("media.gmp-widevinecdm.autoupdate",false);
    lockPref("media.gmp-manager.url", "about:blank");
    lockPref("media.gmp-provider.enabled",false);
    // プラグインのブロック時などの詳細説明の URL
    lockPref("app.support.baseURL", "");
    // Webサイトの互換性情報の URL
    lockPref("breakpad.reportURL", "");
    // about:home に表示するアドバイス情報の取得元 URL
    lockPref("browser.aboutHomeSnippets.updateUrl", "");
    // オートコレクト用辞書の取得先 URL
    lockPref("browser.dictionaries.download.url", "");
    // 位置情報サービスの説明用 URL
    lockPref("browser.geolocation.warning.infoURL", "");
    // 地域ごとのデフォルトの検索サービス切り替え
    lockPref("browser.search.geoSpecificDefaults", false);
    lockPref("browser.search.geoSpecificDefaults.url", "");
    // 検索プロバイダ (検索エンジン) の取得元 URL
    lockPref("browser.search.searchEnginesURL", "");
    // 接続の状態 (接続が制限されているかどうかなど) を判定するためのアクセス先
    lockPref("captivedetect.canonicalURL", "");
    // Developer Edition の説明
    lockPref("devtools.devedition.promo.url", "");
    // 開発ツールで使用するデバイス
    lockPref("devtools.devices.url", "");
    // 開発者ツールからの外部サイト参照の無効化
    lockPref("devtools.gcli.imgurUploadURL", "");
    lockPref("devtools.gcli.jquerySrc", "");
    lockPref("devtools.gcli.lodashSrc", "");
    lockPref("devtools.gcli.underscoreSrc", "");
    lockPref("devtools.remote.adb.extensionURL", "");
    lockPref("devtools.webide.templatesURL", "");
    // 実験的機能の案内の無効化
    lockPref("experiments.manifest.uri", "");
    // パッチ、組み込みのアドオンの更新
    lockPref("extensions.systemAddon.update.url", "");
    // パッチ、組み込みのアドオンの更新
    lockPref("extensions.webservice.discoverURL", "");
    // 位置情報を Wi-Fi アクセスポイントから取得するための URL
    lockPref("geo.wifi.uri", "");
    // Firefox Accounts
    lockPref("identity.fxaccounts.remote.webchannel.uri", "");
    // Firefox Sync 向けモバイルアプリの宣伝リンク
    lockPref("identity.mobilepromo.android", "");
    lockPref("identity.mobilepromo.ios", "");
    // トラッキング防止の案内
    lockPref("privacy.trackingprotection.introURL", "");
    // クラッシュレポーターの関連情報
    lockPref("toolkit.crashreporter.infoURL", "");
    // アドオンの署名義務化に関するメッセージ
    lockPref("xpinstall.signatures.devInfoURL", "");
    // IPv4, IPv6 の疎通確認
    lockPref("network.connectivity-service.enabled", false);
    lockPref("network.connectivity-service.IPv4.url", "");
    lockPref("network.connectivity-service.IPv6.url", "");
    // DNS の疎通確認
    lockPref("network.connectivity-service.DNSv4.domain", "");
    lockPref("network.connectivity-service.DNSv6.domain", "");
    // ブロックリスト等の取得
    lockPref("services.settings.server", "localhost");

## クラッシュ時の情報を送信させたくない

キーワード：導入時初期設定、情報漏洩対策

Firefox や Thunderbird がクラッシュすると、通常はクラッシュレポーターが起動し、開発者達が問題を解決するための手がかりとしてクラッシュ時の詳しい情報をサーバーに送信します。また、送信された情報は公開され、誰でも見ることができます。この仕組みによって機密情報が不用意に流出してしまわないように、クラッシュレポーター自体を無効化することができます。

### 送信される情報の内容

どのような情報がクラッシュレポートに付随して送信されるかは、[プライバシーポリシー](http://www.mozilla.jp/legal/privacy/firefox/) を参照してください。送信される内容には、例えば以下のような情報が含まれます。

* Firefox のバージョン、使用言語、使用テーマ、インストールされた日時、起動時間など
* OS の種類、バージョン、メモリのサイズ、メモリの空き状況など
* インストールされているアドオンの ID とバージョンの一覧
* クラッシュ時の例外とスタックトレース

端的にいうと、場合によっては個人や組織の特定に繋がりうる情報が含まれることがあります。例えば組織内専用に開発したアドオンを使用している場合、そのアドオンの ID は送信される情報に含まれることとなります。

<!--
書きかけで断念した。一言説明を書け次第復活する必要がある。

まず、クラッシュレポーターが送信する情報とはどのような物なのかを説明します。例として、[あるユーザーが Firefox 26 で遭遇したクラッシュについての情報](https://crash-stats.mozilla.com/report/index/a09f9beb-39e7-4706-8e5d-c0be92131222) を見ていきます。

リンク先のページはクラッシュ情報の詳細です。ここには以下の情報が記されています。

 * Signature：クラッシュの種類を識別する情報
 * UUID：このクラッシュ情報に付けられた一意な ID
 * Date Processed：クラッシュ情報が送られた日時
 * Uptime：起動時間
 * Last Crash：前回のクラッシュからの経過時間
 * Install Age：Firefox がインストールされてからの経過時間
 * Install Time：Firefox がインストールされた日時
 * Product：クラッシュした製品 (Firefox)
 * Version：Firefoxのバージョン
 * Build ID：Firefox の詳細なバージョン
 * Release Channel：Firefox の自動アップデート対象 (リリース版、ベータ版など)
 * OS：OSの種類
 * OS Version：OS のバージョン
 * Build Architecture：実行ファイルのビルド対象アーキテクチャ
 * Build Architecture Info：実行ファイルのビルド対象アーキテクチャの詳細
 * Crash Reason：クラッシュの理由となった内部的な例外の名前
 * Crash Address：クラッシュしたメモリ上のアドレス
 * User Comments：クラッシュレポーター上でユーザーが入力したコメント
 * App Notes：ビデオドライバのバージョン、Direct 3D などのハードウェアアクセラレーションの対応情報など
 * Processor Notes：クラッシュレポートの作成に使われたモジュールの情報
 * EMCheckCompatibility：アドオンマネージャでアドオンの互換性確認機能が有効になっているかどうか
 * Winsock LSP：Windowsの通信レイヤの詳細な情報
 * Adapter Vendor ID：ビデオカードのベンダを識別する ID
 * Adapter Device ID：ビデオカードの種類を識別する ID
 * Total Virtual Memory：仮想メモリの総サイズ
 * Available Virtual Memory：仮想メモリの利用可能サイズ
 * System Memory Use Percentage：システムのメモリの使用状況 (パーセント)
 * Available Page File：利用可能なスワップファイルのサイズ
 * Available Physical Memory；利用可能な実メモリのサイズ
 * Add-ons：インストールされているアドオンの ID とバージョンの一覧
-->

### 設定方法

クラッシュレポーターの無効化は、通常の設定手順では行えず、環境変数の設定を必要とします。環境変数 `MOZ_CRASHREPORTER_DISABLE` を `1` に設定した状態では、Firefox がクラッシュしてもクラッシュレポータは起動しません。

## 利用時の統計情報を送信させたくない

キーワード：導入時初期設定、情報漏洩対策

Firefox には、利用時におけるメモリの使用状況などの性能に関する統計情報を収集してサーバーに送信する機能が含まれています。この仕組みは初期状態で無効化されており、ユーザーの確認の上で有効化されますが、最初から無効の状態に固定しておくことができます。

### 送信される情報の内容

どのような情報が統計情報として送信されるかは、[プライバシーポリシー](http://www.mozilla.jp/legal/privacy/firefox/#telemetry) を参照してください。個人や組織の特定に繋がりうる情報としては、統計情報に付随して IP アドレスが送信されます。

### 設定方法

[グループポリシー](../setting-management/#group-policy) または [ポリシー定義ファイル](../setting-management/#policies-json) を用いて、[`DisableTelemetry`](https://github.com/mozilla/policy-templates/blob/master/README.md#disabletelemetry) を `true` に設定してください。例えば以下の要領です。

    {
      "policies": {
        "DisableTelemetry": true
      }
    }

## フォームのオートコンプリート機能を無効化したい

キーワード：機能制限、導入時初期設定、集中管理、情報漏洩対策

Firefox のオートコンプリート機能 (テキストボックスに入力した内容を保存しておき、次回以降の入力を省略する機能) は無効化することができます。

### 設定方法

[グループポリシー](../setting-management/#group-policy) または [ポリシー定義ファイル](../setting-management/#policies-json) を用いて、[`DisableFormHistory`](https://github.com/mozilla/policy-templates/blob/master/README.md#disableformhistory) を `true` に設定してください。例えば以下の要領です。

    {
      "policies": {
        "DisableTelemetry": true
      }
    }

なお、この設定を反映しても、既に保存されている入力履歴の削除までは行われません。

## スマートロケーションバーを無効化したい

キーワード：機能制限、導入時初期設定、集中管理、情報漏洩対策

Firefox のスマートロケーションバー機能 (ロケーションバーから過去の閲覧履歴等を検索する機能) は無効化することができます。

### 設定方法

[MCD (AutoConfig)](../setting-management/#mcd) を用いて以下の通り設定してください。

    // スマートロケーションバーのオートコンプリート機能の無効化
    lockPref("browser.urlbar.autocomplete.enabled", false);
    lockPref("browser.urlbar.maxRichResults", -1);
    lockPref("browser.urlbar.suggest.history", false);
    lockPref("browser.urlbar.suggest.bookmark", false);
    lockPref("browser.urlbar.suggest.openpage", false);
    lockPref("browser.urlbar.suggest.searches", false);

なお、この設定を反映しても、既に保存されている入力履歴や閲覧履歴の削除までは行われません (単に表示されなくなります)。

## パスワードを保存させたくない (パスワードマネージャを無効化したい)

キーワード：機能制限、導入時初期設定、集中管理、情報漏洩対策

Firefox および Thunderbird のパスワードマネージャ機能は無効化することができます。

### 設定方法

[グループポリシー](../setting-management/#group-policy) または [ポリシー定義ファイル](../setting-management/#policies-json) を用いて、[`OfferToSaveLogins`](https://github.com/mozilla/policy-templates/blob/master/README.md#offertosavelogins) を `false` に設定してください。例えば以下の要領です。

    {
      "policies": {
        "OfferToSaveLogins": false
      }
    }

なお、この設定を反映しても、既に保存されてしまっているパスワードの削除までは行われません。

## セッション機能を無効化したい

キーワード：機能制限、導入時初期設定、集中管理、情報漏洩対策

Firefox のセッション関連機能はある程度まで無効化することができます。

### 設定方法

[MCD (AutoConfig)](../setting-management/#mcd) を用いて以下の通り設定してください。

    lockPref("privacy.sanitize.sanitizeOnShutdown", true);
    lockPref("privacy.clearOnShutdown.sessions", true);
    lockPref("browser.sessionstore.max_tabs_undo", 0);
    lockPref("browser.sessionstore.max_windows_undo", 0);
    lockPref("browser.sessionstore.max_resumed_crashes", -1);
    lockPref("browser.sessionstore.max_serialize_back", 0);
    lockPref("browser.sessionstore.max_serialize_forward", 0);
    lockPref("browser.sessionstore.resume_from_crash", false);
    lockPref("browser.sessionstore.resume_session_once", false);
    lockPref("browser.sessionstore.privacy_level", 2);
    lockPref("browser.sessionstore.privacy_level_deferred", 2);
    // 3 にすると前回セッションの復元となるので、それ以外を選択する。
    // 0: 空白ページ、1: ホームページ
    lockPref("browser.startup.page", 0);

この設定により、ディスク上に保存されるセッション情報は最小限の物となります。

### 注意事項

現在のバージョンの Firefox では、セッション管理機構自体を無効化することはできません。上記の設定を行っても、タブで現在開いているページの情報だけはセッション情報に必ず保存されます。また、閉じたウィンドウを開き直すためのデータもセッション中 1 つは必ず保持されます。

## 検索エンジン (Google など) の候補の推測表示を無効化したい

キーワード：機能制限、導入時初期設定、集中管理、情報漏洩対策

Firefox の Web 検索バーは Google などの検索における検索語句の候補の表示に対応していますが、この機能は無効化することができます。

### 設定方法

[グループポリシー](../setting-management/#group-policy) または [ポリシー定義ファイル](../setting-management/#policies-json) を用いて、[`SearchSuggestEnabled`](https://github.com/mozilla/policy-templates/blob/master/README.md#searchsuggestenabled) を `false` に設定してください。例えば以下の要領です。

    {
      "policies": {
        "SearchSuggestEnabled": false
      }
    }

## 位置情報取得 API (Geolocation) を無効化したい

キーワード：機能制限、導入時初期設定、集中管理、情報漏洩対策

Firefox は地図などの Web サービスに対して現在位置の情報を通知する機能を含んでいますが、この機能は無効化することができます。

### 設定方法

[MCD (AutoConfig)](../setting-management/#mcd) を用いて以下の通り設定してください。

    lockPref("geo.enabled", false);
