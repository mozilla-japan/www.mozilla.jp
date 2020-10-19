---
type: business
layout: tech-faq-article
title: 設定の管理
weight: 1
---

## 設定を管理者が管理したい {#control-configurations-by-administrator}

キーワード：機能制限、導入時初期設定、集中管理

Firefox や Thunderbird には、設定を管理者が管理し、ユーザーが自由に変更できないようにするための機能が備わっています。

Firefox ESR 60 以降のバージョンでは、Active Directory のグループポリシーまはた JSON 形式のポリシー定義ファイルを用いて設定を集中管理できます。また、従来からの設定の集中管理の仕組みである「Mission Control Desktop (MCD、あるいは AutoConfig)」も使用できます。

### グループポリシーでの実現 {#group-policy}

[ポリシーテンプレート](https://github.com/mozilla/policy-templates) を使用してグループポリシー経由で設定の集中管理を行えます。[最新のリリース版ポリシーテンプレート](https://github.com/mozilla/policy-templates/releases) をダウンロードしてドメインコントローラに読み込ませ、各種の設定を Active Directory 上で行うと、ドメインに参加した Windows PC 上で Firefox を起動する度に、グループポリシーで変更された設定が読み込まれ、反映されるようになります。

#### 注意点

* 上記ページからダウンロードできる管理用テンプレートファイルの内容は日本語化されていません。日本語で設定を管理したい場合は、管理用テンプレートファイルを自分で翻訳する必要があります。
* 管理できる設定項目は、管理用テンプレートファイルに記述されている物のみとなります。それ以外の設定を管理したい場合は、[Mozilla の Bugzilla](https://bugzilla.mozilla.org/) 経由で設定項目の追加を提案する必要があります。

### ポリシー定義ファイルでの実現 {#policies-json}

Active Directory を運用していない場合や、Windows 以外のプラットフォームでは、JSON 形式のポリシー定義ファイルを設置することによって、グループポリシーと同様の設定の集中管理を行えます。

#### 設定方法

以下のような内容のプレーンテキストファイル `policies.json` を用意します。

    {
      "policies": {
        "DisableAppUpdate": true
      }
    }

この例ではアプリケーションの自動更新を停止する設定のみを記述しています。記述可能な設定項目については、[ポリシーテンプレートのリポジトリ内にある説明 (英語)](https://github.com/mozilla/policy-templates/blob/master/README.md) や [株式会社クリアコードのブログ内の解説](https://www.clear-code.com/blog/2018/5/12.html) などを参照してください。

次に、作成した `policies.json` を、Firefox のインストール先の `distribution/` ディレクトリに置きます (Windows であれば、`C:\Program Files\Mozilla Firefox\distribution\policies.json` など)。

以上で設定は完了です。

#### 注意点

* 管理できる設定項目は、グループポリシーで設定可能な項目のみとなります。それ以外の設定を管理したい場合は、[Mozilla の Bugzilla](https://bugzilla.mozilla.org/) 経由で設定項目の追加を提案する必要があります。

### MCD用設定ファイルでの実現 {#mcd}

以下では、Firefox の自動アップデートを禁止するという場合を例にとって設定の手順を説明します。

#### 設定方法

以下の内容のプレーンテキストファイル `autoconfig.js` を用意します。

    pref("general.config.filename", "autoconfig.cfg");
    pref("general.config.vendor", "autoconfig");
    pref("general.config.obscure_value", 0);

    // 「globalChrome.css 読み込み用スクリプト」を使用する場合は以下の行も必要です。
    pref("general.config.sandbox_enabled", false);

作成した `autoconfig.js` を、Firefox のインストール先の `defaults/pref/` ディレクトリに置きます (Windows であれば、`C:\Program Files\Mozilla Firefox\defaults\pref\autoconfig.js` など)。

以下の内容のプレーンテキストファイル `autoconfig.cfg` を用意します。

    // 1 行目は必ずコメントとしてください。
    lockPref("app.update.enabled", false);

作成した `autoconfig.cfg` を、Firefox または Thunderbird のインストール先ディレクトリに置きます (Windows であれば、`C:\Program Files\Mozilla Firefox\autoconfig.cfg` など)。

以上で設定は完了です。

#### 確認方法

Firefox を起動してオプション (設定画面) を開き、`詳細` → `更新` と辿って、自動更新に関する設定が `更新の確認は行わない` で固定されグレイアウトされていることを確認してください。

#### 詳細情報

`autoconfig.cfg` では以下の 3 つのディレクティブで Firefox・Thunderbird の設定を管理することができます。

* `defaultPref("設定名", 値)` ：設定を指定した値に変更します。ユーザーは設定を自由に変更でき、変更後の値は Firefox・Thunderbird の終了後も保存されます。
* `pref("設定名", 値)` ：設定を指定した値に変更します。ユーザーは設定を一時的に変更できますが、変更後の値は Firefox・Thunderbird を終了すると失われます。 (次回起動時には、`autoconfig.cfg` で指定した値に戻ります。)
* `lockPref("設定名", 値)` ：設定を指定した値に固定します。ユーザーは設定を変更することはできません。

また、`autoconfig.cfg` では JavaScript の制御構文や環境変数の参照、LDAP サーバーからの情報の取得 (※Thunderbird のみ) も利用できます。
詳しくは以下の情報を参照してください。

* [Mozilla 製品の集中管理 - 基本編 - MCD | MDN](https://developer.mozilla.org/ja/docs/MCD/Getting_Started)
* [MCD, Mission Control Desktop AKA AutoConfig | MDN](https://developer.mozilla.org/ja/docs/MCD,_Mission_Control_Desktop_AKA_AutoConfig)

設定を変更する場合は、新しい `autoconfig.cfg` で古い `autoconfig.cfg` を上書きしてください。

`autoconfig.cfg` で管理できる設定項目は、`about:config` (設定エディタ) の一覧に表示される物、もしくは一覧に現れていない隠し設定のみに限られます。アドオンの有効・無効の状態、Web サイトごとの機能の利用許可、メニュー項目の表示・非表示などは、`autoconfig.cfg` では管理できません。

<!--
defaultPref() だけを使うのであれば、distribution/distribution.ini で以下のようにするという手もある。

    [Preferences]
    mozilla.partner.id="testpartner"
    app.distributor="testpartner"
    app.distributor.channel=
    browser.search.distributionID="com.testpartner"

    [LocalizablePreferences]
    browser.startup.homepage="http://sandmill.org/%LOCALE%/%LOCALE%/"
    browser.startup.homepage_reset="http://sandmill.org/%LOCALE%/"

 * [Customizing Firefox – distribution.ini | Mike's Musings](http://mike.kaply.com/2012/03/26/customizing-firefox-distribution-ini/ "Customizing Firefox – distribution.ini | Mike's Musings")
 * [Creating a Customized Firefox Distribution | Mike's Musings](http://mike.kaply.com/2010/08/05/creating-a-customized-firefox-distribution/ "Creating a Customized Firefox Distribution | Mike's Musings")

が、話がややこしくなるので、ここでは触れないことにする。
-->

## すべてのクライアントの設定を管理者が一括して変更したい

キーワード：機能制限、導入時初期設定、集中管理

Active Directory ドメインに参加している Windows PC では、[グループポリシー](../setting-management/#group-policy) によって、管理者が全クライアントの設定を一括して管理・変更することができます。

グループポリシーを使用しない場合でも、Firefox や Thunderbird の独自の設定管理機能である [MCD (AutoConfig)](../setting-management/#mcd) では、各クライアントのローカルディスク上に設置した設定ファイルだけでなく、サーバー上に設置した設定ファイルを読み込ませることができます。これにより、管理者が 1 つの設定ファイルを管理するだけで全クライアントの設定を一括して管理・変更するという運用が可能です。

以下では、設定ファイルを `http://internalserver/autoconfig.jsc` として提供して Firefox の自動アップデートを禁止するという場合を例にとって MCD での設定の手順を説明します。

### 設定方法

以下の内容のプレーンテキストファイル `autoconfig.js` を用意します。

    pref("general.config.filename", "autoconfig.cfg");
    pref("general.config.vendor", "autoconfig");
    pref("general.config.obscure_value", 0);

作成した `autoconfig.js` を、Firefox のインストール先の `defaults/pref/` ディレクトリに置きます (Windows であれば、`C:\Program Files\Mozilla Firefox\defaults\pref\autoconfig.js` など)。

以下の内容のプレーンテキストファイル `autoconfig.cfg` を用意します。

    // 1 行目は必ずコメントとしてください。
    lockPref("autoadmin.global_config_url", "http://internalserver/autoconfig.jsc");

作成した `autoconfig.cfg` を、Firefox または Thunderbird のインストール先ディレクトリに置きます (Windows であれば、`C:\Program Files\Mozilla Firefox\autoconfig.cfg` など)。

以下の内容のプレーンテキストファイル `autoconfig.jsc` を用意します。

    // 1 行目は必ずコメントとしてください。
    lockPref("app.update.enabled", false);

次に、上記の URL にてファイルをダウンロード可能なように、設定ファイルの提供用サーバーにファイルを設置します。また、ファイルは以下の MIME Type を伴って送信されるように設定します。

    application/x-javascript-config

以上で設定は完了です。

### 確認方法

Firefox を起動してオプション (設定画面) を開き、`詳細` → `更新` と辿って、自動更新に関する設定が `更新の確認は行わない` で固定されグレイアウトされていることを確認してください。

### 詳細情報

`autoconfig.jsc` の書式と設定可能な設定項目の種類は、autoconfig.cfg と同一です。詳細は[設定を管理者が管理したい](#control-configurations-by-administrator)を参照してください。

なお、`autoconfig.jsc` は Web サーバーでの提供以外に、ローカルファイル、ファイル共有サーバー上のファイルなどの形でも提供できます。以下はすべて有効な指定です。

    // ローカルファイルとして提供する場合 (ネットワークドライブをマウントする場合など)
    lockPref("autoadmin.global_config_url", "file:///Z:/firefox/autoconfig.jsc");

    // Samba サーバー、NAS などの上にファイルを設置する場合
    lockPref("autoadmin.global_config_url", "file://///file-server/shared/firefox/autoconfig.jsc");

## Thunderbird のアカウント設定を非表示にしたい (管理者が設定を集中管理するので、アカウント設定の画面にアクセスさせたくない)

キーワード：機能制限、導入時初期設定、集中管理

[MCD (AutoConfig)](../setting-management/#mcd) や [グループポリシー](../setting-management/#group-policy) などの方法でアカウント設定を管理者が管理する際に、ユーザーがアカウント設定の画面にアクセスできないようにすることができます。

### 設定方法

アカウント設定画面へのアクセス経路を UI 上に表示しないようにするためには、[`globalChrome.css` 読み込み用スクリプト](https://github.com/clear-code/globalchromecss/blob/master/autoconfig-globalchromecss.js) を使ってメニュー項目を隠す必要があります。 `globalChrome.css` を使う場合の手順は以下の通りです。

 1. 「メモ帳」などのテキストエディタを開き、開発ツールで調べた ID を使って項目を非表示にするスタイル指定を記述します。

        @-moz-document url-prefix(chrome://messenger/content/) {
          #menu_accountmgr,
          #appmenu_accountmgr,
          #appmenu_newAccountPopupMenuSeparator,
          #appmenu_newCreateEmailAccountMenuItem,
          #appmenu_newMailAccountMenuItem,
          #appmenu_newIMAccountMenuItem,
          #appmenu_newFeedAccountMenuItem,
          #appmenu_newAccountMenuItem {
            display: none !important;
          }
        }
        @-moz-document url-prefix(chrome://messenger/content/msgAccountCentral.xul) {
          #AccountsHeader,
          #AccountSettings.spacer,
          #AccountSettings,
          #CreateAccount.spacer,
          #CreateAccount,
          #CreateAccounts,
          #AccountsSection.spacer {
            display: none !important;
          }
        }

     (`@-moz-document` は、特定のウィンドウに対してのみスタイル指定を反映させるための記述です。詳細は [`@-moz-document` について参考](http://www.akatsukinishisu.net/wiki.cgi?%40-moz-document) を参照してください。)

 2. 1 で作成した内容を `globalChrome.css` という名前のプレーンテキストファイルに保存します。
 3. 2 で作成したファイルを Thunderbird のインストール先の `chrome` フォルダに設置します。

     (Windows Vista 以降の場合のファイルの設置場所は `C:\Program Files\Mozilla Thunderbird\chrome\globalChrome.css` となる。)

 4. [MCD用設定ファイル](#mcd) を作成し、`autoconfig.cfg` に [`globalChrome.css` 読み込み用スクリプト](https://github.com/clear-code/globalchromecss/blob/master/autoconfig-globalchromecss.js) の内容を張り付けて設置します。

## `about:config` (設定エディタ) の利用を禁止したい

キーワード：機能制限、導入時初期設定、集中管理

無用なトラブルを避けるため、ユーザーが `about:config` (設定エディタ) の画面にアクセスできないようにすることができます。

### 設定方法

[グループポリシー](../setting-management/#group-policy) または [ポリシー定義ファイル](../setting-management/#policies-json) を用いて、[`BlockAboutConfig`](https://github.com/mozilla/policy-templates/blob/master/README.md#blockaboutconfig) を `true` に設定してください。例えば以下の要領です。

    {
      "policies": {
        "BlockAboutConfig": true
      }
    }

これにより、`about:config` の読み込みがブロックされ、設定を変更できない状態になります。
