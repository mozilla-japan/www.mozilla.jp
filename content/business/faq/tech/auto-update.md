---
type: business
layout: tech-faq-article
title: 自動アップデート
weight: 6
---

## Firefox・Thunderbird の自動アップデートを禁止したい

キーワード：機能制限、集中管理、自動アップデート

無用なトラブルを避けるため、ユーザーが使用中に Firefox や Thunderbird が自動アップデートを行わないよう設定することができます。

### 設定方法

[グループポリシー](../setting-management/#group-policy) または [ポリシー定義ファイル](../setting-management/#policies-json) を用いて、[`DisableAppUpdate`](https://github.com/mozilla/policy-templates/blob/master/README.md#disableappupdate) を `true` に設定してください。例えば以下の要領です。

    {
      "policies": {
        "DisableAppUpdate": true
      }
    }

## Firefox・Thunderbird の自動アップデートについて、メジャーアップデートは禁止し、マイナーアップデートのみ自動で適用したい

キーワード：機能制限、集中管理、自動アップデート

Firefox や Thunderbird の ESR 版は通常、あるメジャーバージョンのサポートが終了すると、自動アップデート経由で次のメジャーバージョンにアップデートされます。例えば Firefox ESR 68 は、順次 Firefox ESR 78 へアップデートされます。

このようなメジャーバージョンの変更を伴う自動アップデートの適用を禁止し、マイナーバージョンの変更のみを適用するには、組織内で提供する更新情報を参照するようにする必要があります。

### 設定方法

[グループポリシー](../setting-management/#group-policy) または [ポリシー定義ファイル](../setting-management/#policies-json) を用いて、[`AppUpdateURL`](https://github.com/mozilla/policy-templates/blob/master/README.md#AppUpdateURL) を社内ホスト上の URL に設定してください。例えば以下の要領です。

    {
      "policies": {
        "AppUpdateURL": "http://192.168.0.10/update.xml"
      }
    }

上記 URL で提供する更新情報の内容の記述方法は次項を参照してください。

## Firefox・Thunderbird の自動アップデートの提供タイミングを組織内で制御したい

キーワード：機能制限、集中管理、自動アップデート

通常、Firefox や Thunderbird は Mozilla が公式に提供しているアップデート情報に基づいて自動アップデートを行いますが、設定変更により、組織内のサーバーなどをアップデート情報の提供元にすることができます。これにより、自動アップデートの適用タイミングを制御できます。

### 設定方法

<!--

    app.update.url に update.xml の URL を指定する場合、ユーザー設定では反映されず、AutoConfig を使い defaultPref/lockPref で認識させる必要がある。
        URL には https: のみ利用できる。http:, ftp:, file: などは利用できない。
        http://mxr.mozilla.org/mozilla-central/source/toolkit/mozapps/update/nsUpdateService.js#3150
        ( http://mxr.mozilla.org/mozilla-esr17/source/toolkit/mozapps/update/nsUpdateService.js#2806 )
        http://mxr.mozilla.org/mozilla-central/source/toolkit/mozapps/shared/CertUtils.jsm#142
        ( http://mxr.mozilla.org/mozilla-esr17/source/toolkit/mozapps/shared/CertUtils.jsm#142 )
        ビルトインのルート証明書で検証できる証明書か、もしくは、独自の証明書が必要。独自の証明書を使う場合は app.update.cert.requireBuiltIn=false にする。
        それ以外は拒否されてしまう。
        update.xml から指定するアップデート用ファイルの URL は、http または https でなければならない。
        http://mxr.mozilla.org/mozilla-esr17/source/netwerk/base/src/nsIncrementalDownload.cpp#503
        ローカルファイル、FTP などは使えない。
    この方法は説明が煩雑なので紹介しない。

-->

Firefox ESR 60.7.0 (64 bit 版) が導入済みのクライアントを Firefox ESR 68.0 (64 bit 版) に更新するための情報およびファイルを静的なファイルとして提供する場合を例として、手順を説明します。

 1. アップデート用のアーカイブファイルを Mozilla のリリースサーバーから入手します。

    - リリースサーバー上には各バージョンのアップデート用差分ファイル、完全アップデート用アーカイブファイルが保存されており、以下のような URL でダウンロードすることができます。

      [https://releases.mozilla.org/pub/firefox/releases/68.0esr/update/win64/ja/](https://releases.mozilla.org/pub/firefox/releases/68.0esr/update/win64/ja/)

    - ファイル名に `partial` と付いている物は差分アップデート用ファイル、`complete` と付いている物は完全アップデート用ファイルです。差分アップデート用ファイルはファイル名で示されている更新前バージョンに対してのみ適用できます。

 2. 1 でダウンロードしたファイルを、自組織内からアクセスできる HTTP サーバー上に設置します。Samba サーバー上のファイルにファイルとしてアクセスする形態や、ローカルのファイルシステムにマウントしてファイルとしてアクセスする形態では利用できず、あくまで HTTP 経由でダウンロードできる状態にしておく必要があります。
 3. 以下のような内容で、自動アップデート情報提供用のXMLファイル `update.xml` を用意します。

        <?xml version="1.0"?>
        <updates>
          <update type="minor"
                  displayVersion="更新後バージョン番号の表示名"
                  appVersion="更新後バージョン番号"
                  platformVersion="更新後バージョン番号"
                  buildID="更新後バージョンのビルド ID"
                  actions="silent">
            <patch type="complete"
                   URL="marファイルのダウンロード用 URL"
                   hashFunction="ハッシュ関数の種類"
                   hashValue="mar ファイルのハッシュ"
                   size="mar ファイルのサイズ（バイト数）"/>
          </update>
        </updates>

    例えば Firefox ESR 68.0 への更新で、ハッシュを SHA-512 で用意するのあれば、以下のようになります。

        <?xml version="1.0"?>
        <updates>
          <update type="minor"
                  displayVersion="68.0esr"
                  appVersion="68.0"
                  platformVersion="68.0"
                  buildID="about:supportで確認できるビルド ID"
                  actions="silent">
            <patch type="complete"
                   URL="marファイルのダウンロード用 URL"
                   hashFunction="SHA512"
                   hashValue="mar ファイルの SHA-512 ハッシュ"
                   size="mar ファイルのサイズ（バイト数）"/>
          </update>
        </updates>

 4. 3 で用意したファイルをクライアント上のローカルファイル、ファイル共有サーバー上のファイル、HTTP サーバー上のファイルのいずれかの形で設置し、クライアントから取得できるようにします。
 5. [グループポリシー](../setting-management/#group-policy) または [ポリシー定義ファイル](../setting-management/#policies-json) を用いて、[`AppUpdateURL`](https://github.com/mozilla/policy-templates/blob/master/README.md#AppUpdateURL) を 4 で設置したファイルの URL 文字列に設定してください。例えば以下の要領です。

    {
      "policies": {
        "AppUpdateURL": "http://192.168.0.10/update.xml"
      }
    }

以上で更新情報の提供準備ならびにクライアントの設定は完了です。以後は、サーバー上に設置した `update.xml` ならびにアップデート用のアーカイブファイルを適宜更新するようにしてください。

詳細な情報は [更新サーバーの設定 - Mozilla | MDN](https://developer.mozilla.org/ja/docs/Mozilla/Setting_up_an_update_server) を参照してください。

### 確認方法

以下の通り設定を変更すると、自動アップデートの処理が 10 秒ごとに行われるようになります。この状態で `エラーコンソール` もしくは `ブラウザコンソール` を表示すると、自動アップデート処理の詳細なログが表示されます。更新情報の取得に成功しているかどうか、取得した更新情報の読み込みに成功しているかどうかなどを確認するのに利用できます。

* `app.update.timerMinimumDelay` (整数) ： `10`
* `app.update.promptWaitTime` (整数) ： `1`
* `app.update.interval` (整数) ： `10`
* `app.update.log` (真偽) ： `true`
* `app.update.log.all` (真偽) ： `true`

### 注意事項

上記手順での設定後は、SSL を使用しない限り、更新情報の提供元自体が正しいかどうか (中間者攻撃を受けていないかどうか) の検証は行われない状態となります。信頼できないネットワークを経由する場合は、SSL を使って安全に更新情報を取得できるようにしてください。

## 自動アップデート機能を使わずに Firefox/Thunderbird を差分更新したい

キーワード：自動アップデート

Firefox や Thunderbird の自動アップデート機能は、通常のインストーラよりも遙かに小さい差分ファイルをダウンロードしてアプリケーションを更新するようになっています。この差分更新用のファイルを使った差分更新処理は、自動アップデート機能を使わずとも、任意のタイミングで実行することができます。これにより、自動アップデート機能自体は無効にしておきつつ、必要に応じてシステムのログオンスクリプトを使って任意のタイミングでの差分更新を適用する、という形での運用が可能です。

### 差分ファイルの入手

差分アップデートの適用時には、アップデート用差分ファイルを公式の FTP サイトから入手する必要があります。URL の凡例は以下の通りです。

    Windows 用 64 bit 版：
    https://releases.mozilla.org/pub/mozilla.org/[製品名]/releases/[アップデート先バージョン]/update/win64/ja/[製品名]-[アップデート元バージョン]-[アップデート先バージョン].partial.mar
    Windows 用 32 bit 版：
    https://releases.mozilla.org/pub/mozilla.org/[製品名]/releases/[アップデート先バージョン]/update/win32/ja/[製品名]-[アップデート元バージョン]-[アップデート先バージョン].partial.mar

例えば Firefox ESR 68.0 (64 bit 版) から ESR 68.1.0 (64 bit 版) へアップデートする場合に必要な差分ファイルは以下の場所から入手できます。

    https://releases.mozilla.org/pub/firefox/releases/68.1.0esr/update/win64/ja/firefox-68.0esr-68.1.0esr.partial.mar

差分ファイルによるアップデートを行うには、現在インストールされている Firefox のバージョンに対応した差分ファイルが必要となります。差分ファイルが想定する「アップデート前のバージョン」が現在インストールされている Firefox のバージョンに一致しない場合、差分アップデートは行えません。

通常、公式の FTP サイトでは特定バージョンの Firefox に対して、それ以前のいくつかのバージョンからの差分アップデート用のファイルのみが配布されています。差分ファイルが用意されていないパターン、例えば Firefox ESR 60.0 から ESR 68.0 へアップデートするというような、間のバージョンを多数飛ばしたアップデートは原則として行えないものとご理解ください。

### 差分更新の適用手順の凡例

Firefox の差分更新用ファイルを用いて際の手順は以下の通りです。

 1. 管理者権限でコマンドプロンプトを起動する。Windows XP の場合、Administrator 権限のあるユーザーでコマンドプロンプトを起動する。Windows Vista 以降の場合、スタートメニューの「すべてのプログラム」→「アクセサリ」→「コマンド プロンプト」を右クリックして「管理者として実行」を選択する。
 2. 先の方法で入手した差分アップデート用のファイル ( `firefox-*-*.partial.mar` ) を作業ディレクトリに「`update.mar`」というファイル名で配置する。

        > copy firefox-*.partial.mar "<作業ディレクトリのパス>\update.mar"

 3. Firefox のインストール先フォルダにある `updater.exe` を作業ディレクトリにコピーする。

        > copy "<Firefoxのインストール先フォルダのパス>\updater.exe"
            "<作業ディレクトリのパス>\updater.exe"

 4. 作業ディレクトリに配置した `updater.exe` を、差分アップデート用のファイルがあるディレクトリ (ここでは作業ディレクトリと同じ) のフルパスを第 1 引数、Firefox のインストール先フォルダのフルパスを第 2 引数、`updater.exe` が動作する際の作業フォルダのパス (＝Firefox のインストール先フォルダ) のフルパスを第 3 引数して渡して起動する。

        > cd "<作業ディレクトリのパス>"
        > "<作業ディレクトリのパス>\updater.exe" "<差分アップデート用のファイルがあるディレクトリのフルパス>" "<Firefoxのインストール先フォルダのフルパス>" "<Firefoxのインストール先フォルダのフルパス>"

 5. アップデートの適用結果を確認する。作業ディレクトリに出力された `update.status` の内容が「succeeded」であれば、アップデートに成功している。そうでない場合は、アップデートの適用に失敗している。

 6. アンインストール情報を更新する。 `update.log` を Firefox のインストール先フォルダの「`uninstall`」フォルダ内に、「`uninstall.update`」というファイル名でコピーする。

        > copy /Y update.log "<Firefoxのインストール先フォルダのパス>\uninstall\uinstall.update"

 7. アップデートの後処理を実行する。Firefox のインストール先フォルダの「`uninstall`」フォルダにある `helper.exe` を、「`/PostUpdate`」オプションを指定して実行する。

        > "<Firefoxのインストール先フォルダのパス>\uninstall\helper.exe" /PostUpdate

    これにより、レジストリ内の Firefox のバージョンなどの情報が更新される。

以上で、差分アップデートの適用は完了です。

### 差分更新の適用例

本項では、例として以下のバージョンにおける差分更新の適用時の具体的な手順を示します。

* 現在 Firefox ESR 68.0 がインストールされている。
* Firefox ESR 68.1.0 へアップデートする。
* 作業ディレクトリは `C:\temp` とする。
* Firefox のインストール先は `C:\Program Files\Mozilla Firefox` とする。

 1. 管理者権限でコマンドプロンプトを起動する。
 2. 差分アップデート用のファイルを作業ディレクトリに `update.mar` というファイル名で配置する。

        > copy firefox-68.0esr-68.1.0esr.partial.mar "C:\temp\update.mar"

 3. Firefox のインストール先フォルダにある `updater.exe` を作業ディレクトリにコピーする。

        > copy "C:\Program Files\Mozilla Firefox\updater.exe"
          "C:\temp\updater.exe"

 4. 作業ディレクトリに配置した `updater.exe` を、作業ディレクトリのフルパスを第 1 引数、Firefox のインストール先フォルダのフルパスを第 2 引数と第 3 引数として渡して起動する。

        > cd c:\temp
        > updater.exe "C:\temp" "C:\Program Files\Mozilla Firefox" "C:\Program Files\Mozilla Firefox"

 5. アップデートの適用結果を確認する。

        > type update.status

以上で、差分アップデートの適用は完了です。

## アドオンの自動アップデートの提供タイミングを組織内で制御したい

キーワード：機能制限、集中管理、自動アップデート、アドオン

通常、Firefox や Thunderbird は Mozilla が公式に提供しているアドオンのアップデート情報に基づいてアドオンの自動アップデートを行いますが、設定変更により、組織内のサーバーなどをアップデート情報の提供元にすることができます。これにより、アドオンの自動アップデートの適用タイミングを制御できます。

### 設定方法

 1. 以下のような内容で、自動アップデート情報提供用の JSON ファイル `update.json` を用意します。

        {
          "addons": {
            "アドオンの ID": {
              "updates": [
                {
                  "version": "アドオンのバージョン",
                  "update_link": "XPI ファイルのダウンロード用 URL",
                  "update_hash": "ハッシュ関数名: XPI ファイルのハッシュ値"
                  "browser_specific_settings": {
                    "gecko": { "strict_min_version": "対応する Firefox の最小バージョン" }
                  }
                }
              ]
            }
          }
        }

    例えば [Duplicate Tabs Closer](https://addons.mozilla.org/firefox/addon/duplicate-tabs-closer/) の更新情報を提供するのであれば以下のようになります。

        {
          "addons": {
            "jid0-RvYT2rGWfM8q5yWxIxAHYAeo5Qg@jetpack": {
              "updates": [
                {
                  "version": "3.4.1",
                  "update_link": "http://192.168.0.10/duplicate_tabs_closer-3.4.1-fx.xpi",
                  "update_hash": "sha256:a952bbcef93fbd0d5e2278265824fc270c356bbabe91c79ef3245f7419d9f02c"
                  "browser_specific_settings": {
                    "gecko": { "strict_min_version": "55.0" }
                  }
                }
              ]
            }
          }
        }

 2. 1 で用意したファイルをクライアント上のローカルファイル、ファイル共有サーバー上のファイル、HTTP サーバー上のファイルのいずれかの形で設置し、クライアントから取得できるようにします。
 3. [MCD (AutoConfig)](../setting-management/#mcd) を使って、文字列型の設定 `extensions.update.url` の値に、2 で設置したファイルの URL 文字列を指定します。

以上で更新情報の提供準備ならびにクライアントの設定は完了です。以後は、サーバー上に設置した `update.json` ならびに各アドオンのXPIファイルを適宜更新するようにしてください。

詳細な情報は [Updating your extension | Extension Workshop](https://extensionworkshop.com/documentation/manage/updating-your-extension/) を参照してください。

### 確認方法

以下の通り設定を変更すると、アドオンの自動アップデートの処理が 10 秒ごとに行われるようになります。この状態で `エラーコンソール` もしくは `ブラウザコンソール` を表示すると、自動アップデート処理の詳細なログが表示されます。更新情報の取得に成功しているかどうか、取得した更新情報の読み込みに成功しているかどうかなどを確認するのに利用できます。

* `app.update.timerMinimumDelay` (整数) ： `10`
* `extensions.update.interval` (整数) ： `10`
* `extensions.logging.enabled` (真偽) ： `true`

### 注意事項

上記手順での設定後は、SSL を使用しない限り、更新情報の提供元自体が正しいかどうか (中間者攻撃を受けていないかどうか) の検証は行われない状態となります。信頼できないネットワークを経由する場合は、SSL を使って安全に更新情報を取得できるようにしてください。
