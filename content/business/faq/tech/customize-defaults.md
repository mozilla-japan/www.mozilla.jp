---
type: business
layout: tech-faq-article
title: 初期設定の変更
weight: 8
---

## 既定のホームページを変更したい

キーワード：導入時初期設定

Firefox を起動した時に表示される最初のページはユーザーが自由に変更できますが、変更するまでの間は初期設定が使われ、また、`初期設定に戻す` で最初の状態に戻すことができます。この時の初期設定として使われるページは変更することができます。

### 設定方法

[グループポリシー](../setting-management/#group-policy) または [ポリシー定義ファイル](../setting-management/#policies-json) を用いて、[`Homepage.URL`](https://github.com/mozilla/policy-templates/blob/master/README.md#homepage) を設定してください。例えば以下の要領です。

    {
      "policies": {
        "Homepage": {
          "URL": "http://example.com",
          "Locked": false
        }
      }
    }

ユーザーによる設定の変更を禁止する場合は以下のようにしてください。

    {
      "policies": {
        "Homepage": {
          "URL": "http://example.com",
          "Locked": true
        }
      }
    }

また、単一のタブではなく複数のタブをホームページとして開く場合は以下のようにしてください。

    {
      "policies": {
        "Homepage": {
          "URL": "http://example.com",
          "Locked": true,
          "Additional": [
            "https://example.org:8080",
            "https://example.jp:8080"
          ]
        }
      }
    }

## 初期状態のブックマークの内容を変更したい

キーワード：導入時初期設定、ブックマーク

Firefox の初期状態のブックマークの内容は、変更することができます。

### 設定方法

[グループポリシー](../setting-management/#group-policy) または [ポリシー定義ファイル](../setting-management/#policies-json) を用いて、[`Bookmarks`](https://github.com/mozilla/policy-templates/blob/master/README.md#bookmarks) を設定してください。例えば以下の要領です。

    {
      "policies": {
        "Bookmarks": [
          {
            "Title": "ブックマークツールバー上に作成する項目",
            "URL": "https://example.com/toolbar",
            "Favicon": "https://example.com/favicon.ico",
            "Placement": "toolbar",
            "Folder": "フォルダー名"
          },
          {
            "Title": "ブックマークメニューに作成する項目",
            "URL": "https://example.com/menu",
            "Favicon": "https://example.com/favicon.ico",
            "Placement": "menu",
            "Folder": "フォルダー名"
          }
        ]
      }
    }

## ブックマークを初期状態で空にしたい

キーワード：導入時初期設定、ブックマーク

Firefox の初期状態で含まれているブックマーク項目は、空にすることができます。

### 設定方法

[グループポリシー](../setting-management/#group-policy) または [ポリシー定義ファイル](../setting-management/#policies-json) を用いて、[`NoDefaultBookmarks`](https://github.com/mozilla/policy-templates/blob/master/README.md#nodefaultbookmarks) を `true` に設定してください。例えば以下の要領です。

    {
      "policies": {
        "NoDefaultBookmarks": true
      }
    }

## ブックマークツールバーを初期状態で表示したい

キーワード：導入時初期設定、ブックマーク

Firefox の初期状態ではブックマークツールバーは非表示ですが、表示状態にしておくことができます。

### 設定方法

[グループポリシー](../setting-management/#group-policy) または [ポリシー定義ファイル](../setting-management/#policies-json) を用いて、[`DisplayBookmarksToolbar`](https://github.com/mozilla/policy-templates/blob/master/README.md#displaybookmarkstoolbar) を `true` に設定してください。例えば以下の要領です。

    {
      "policies": {
        "DisplayBookmarksToolbar": true
      }
    }

## プロキシの設定を固定・強制したい

キーワード：導入時初期設定、機能制限

Firefox のネットワーク設定において、プロキシの使用を強制することができます。

### 設定方法

[グループポリシー](../setting-management/#group-policy) または [ポリシー定義ファイル](../setting-management/#policies-json) を用いて、[`Proxy`](https://github.com/mozilla/policy-templates/blob/master/README.md#proxy) 配下の設定を適切に設定してください。

特定の HTTP プロキシの使用を強制する場合は以下のように設定します。

    {
      "policies": {
        "Proxy": {
          "Mode": "manual",
          "Locked": true,
          "HTTPProxy": "proxy.hostname:8080",
          "UseHTTPProxyForAllProtocols": true
        }
      }
    }

自動設定スクリプト (PAC ファイル) の使用を強制する場合は以下のように設定します。

    {
      "policies": {
        "Proxy": {
          "Mode": "autoConfig",
          "Locked": true,
          "AutoConfigURL": "http://internal-server/proxy.pac"
        }
      }
    }

## プロキシを使用しない例外サイト (ドメイン) を指定したい

キーワード：導入時初期設定

Firefox は、プロキシを使用しない例外サイトを設定する機能を持っています。

### 設定方法

[MCD (AutoConfig)](../setting-management/#mcd) を用いて以下の通り設定してください。対象ホスト名はカンマ区切りで複数指定できます。

    lockPref("network.proxy.no_proxies_on", "localhost, .example.org");

## 履歴を保存させたくない

キーワード：導入時初期設定、機能制限

Firefox での Web ページの閲覧履歴について、一切の履歴を保存しないように設定することができます。

### 設定方法

[グループポリシー](../setting-management/#group-policy) または [ポリシー定義ファイル](../setting-management/#policies-json) を用いて、[`SanitizeOnShutdown`](https://github.com/mozilla/policy-templates/blob/master/README.md#sanitizeonshutdown-selective) を `true` に設定してください。例えば以下の要領です。

    {
      "policies": {
        "SanitizeOnShutdown": true
      }
    }

この設定を行うと、Cookie やフォームの入力、サイトごとの設定なども全て消去されます。各項目を消去するかどうかを個別に変更したい場合、以下のように消去した物のみ `true` と設定してください。

    {
      "policies": {
        "SanitizeOnShutdown": {
          "Cache": true,
          "Cookies": false,
          "Downloads": true,
          "FormData": false,
          "History": true,
          "Sessions": true,
          "SiteSettings": false,
          "OfflineApps": true
        }
      }
    }

## 一定以上の日数が経過した履歴を自動的に消去したい

キーワード：導入時初期設定、機能制限

現在のバージョンの Firefox は、一定以上の日数が経過した履歴を自動的に消去する機能を持っていません。アドオンを使用することで、古い履歴を自動的に消去させることができます。

### 設定方法

日数ベースでの履歴の管理機能を Firefox に導入する方法としては、アドオン [Expire history by days](https://addons.mozilla.org/firefox/addon/expire-history-by-days/) の利用が挙げられます。

ただし、このアドオンは管理者が設定を変更する機能を持ちません。履歴の削除日数を管理者側で制御するためには、ポリシー設定に対応した派生版である [Flexible Expire History by Days](https://github.com/clear-code/flexible-expire-history-by-days/releases) を使用する必要があります。例えば、履歴を 30 日まで保持する設定であれば以下の要領です。

 1. [管理者によるアドオンのインストール手順](../addons/#install-addons-by-administrator) に従い、Flexible Expire History by Days を導入します。
 2. [グループポリシー](../setting-management/#group-policy) または [ポリシー定義ファイル](../setting-management/#policies-json) の [`3rdparty.Extensions`](https://github.com/mozilla/policy-templates/blob/master/README.md#sanitizeonshutdown-selective)、または [managed storage manifest](https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/Native_manifests#Managed_storage_manifests) を用いて設定を固定します。以下はポリシー定義ファイルでの設定例です。

    {
      "policies": {
        "3rdparty": {
          "Extensions": {
            "flexible-expire-history-by-days@clear-code.com": {
              "frequency": 0,
              "days":      30
            }
          }
        }
      }
    }

    以下は managed storage manifest での設定例です。

    {
      "name": "flexible-expire-history-by-days@clear-code.com",
      "description": "Configs for Flexible Expire History by Days",
      "type": "storage",
      "data": {
        "frequency": 0,
        "days":      30
      }
    }

## サイトごとの機能の許可をあらかじめ設定しておきたい (位置情報の取得の可否、アドオンのインストールの可否など)

キーワード：導入時初期設定

Firefox には、Cookie や位置情報などの Web ページから利用できる様々な機能について、機能の許可を Web サイトごとに管理する仕組みが備わっています。既に保存されている設定については、設定画面の「プライバシーとセキュリティ」配下の「許可設定」で設定の変更や消去が可能です。

### 設定方法

[グループポリシー](../setting-management/#group-policy) または [ポリシー定義ファイル](../setting-management/#policies-json) を用いて、[`Permissions`](https://github.com/mozilla/policy-templates/blob/master/README.md#permissions) 配下に設定を記述してください。

例えば、カメラ、マイク、位置情報、デスクトップ通知のそれぞれについて、既定ではアクセスを拒否しつつ `https://example.org` に対してのみ許可する場合は以下のようになります。

    {
      "policies": {
        "Permissions": {
          "Camera": {
            "Allow": ["https://example.org"],
            "BlockNewRequests": true,
            "Locked": true
          },
          "Microphone": {
            "Allow": ["https://example.org"],
            "BlockNewRequests": true,
            "Locked": true
          },
          "Location": {
            "Allow": ["https://example.org"],
            "BlockNewRequests": true,
            "Locked": true
          },
          "Notifications": {
            "Allow": ["https://example.org"],
            "BlockNewRequests": true,
            "Locked": true
          }
        }
      }
    }

逆に、既定ではアクセスを許可しつつ `https://example.org` に対してのみ禁止する場合は以下のようになります。

    {
      "policies": {
        "Permissions": {
          "Camera": {
            "Block": ["https://example.org"],
            "BlockNewRequests": false,
            "Locked": true
          },
          "Microphone": {
            "Block": ["https://example.org"],
            "BlockNewRequests": false,
            "Locked": true
          },
          "Location": {
            "Block": ["https://example.org"],
            "BlockNewRequests": false,
            "Locked": true
          },
          "Notifications": {
            "Block": ["https://example.org"],
            "BlockNewRequests": false,
            "Locked": true
          }
        }
      }
    }

## ロケーションバーで常に「`http://`」を表示させたい

キーワード：導入時初期設定

Firefox のロケーションバーでは通常、URL 文字列の先頭の「`http://`」は省略して表示されますが、これを常に表示するように設定することができます。

### 設定方法

[MCD (AutoConfig)](../setting-management/#mcd) を用いて以下の通り設定してください。

    lockPref("browser.urlbar.trimURLs", false);

## 独自ルート証明書をあらかじめ登録済みの状態にしたい

キーワード：導入時初期設定

Firefox にあらかじめ登録されている物以外の証明局によって署名された証明書を使った Web サイトに SSL で接続すると、Firefox は不明な証明書として警告を表示します。それらの証明書の発行元である独自認証局の証明書を別途安全な手段で提供できるのであれば、証明書を Firefox にあらかじめ登録しておくことで、警告画面を見ずに Web サイトを利用することができます。

### エンタープライズの証明書の自動インポート

Firefoxは Active Directory のグループポリシーによって Windows に配布されたエンタープライズの証明書を自動的にインポートして認識します。Active Directory を運用している場合、この方法での配布が最も手軽です。

### PEM 形式の証明書の自動インポート

Active Directory を運用していない環境では、[ポリシー定義ファイル](../setting-management/#policies-json) を用いて証明書を自動インポートできます。手順は以下の通りです。

1. PEM形式の証明書ファイルをローカルディスク上または UNC パスで表されるファイルサーバー上に設置します。
2. ポリシー定義ファイルの [`Certificates.Install`](https://github.com/mozilla/policy-templates/blob/master/README.md#certificates--install) に、インポート対象の証明書ファイルのパスを指定します。例えば以下の要領です。

   {

     "policies": {
       "Certificates": {
         "Install": [
           "C:\\Users\\All Users\\cert1.pem",
           "\\\\192.168.0.10\\shared\\cert2.pem"
         ]
       }
     }

   }

#### SSL のセキュリティ例外の自動登録

Firefox は、SSL のセキュリティ例外について、特定のホストを対象に設定する機能を持ちません。
