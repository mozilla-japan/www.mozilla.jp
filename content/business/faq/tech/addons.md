---
type: business
layout: tech-faq-article
title: アドオン、プラグイン
weight: 2
---

## アドオンの利用を禁止したい (ユーザーが任意にアドオンをインストールできないようにしたい)

キーワード：機能制限、導入時初期設定、集中管理、アドオン

無用なトラブルを避けるため、ユーザーが任意にアドオンをインストールできないよう設定することができます。

### 設定方法

[グループポリシー](../setting-management/#group-policy) または [ポリシー定義ファイル](../setting-management/#policies-json) を用いて、[`BlockAboutAddons`](https://github.com/mozilla/policy-templates/blob/master/README.md#blockaboutaddons) を `true` に、[`InstallAddonsPermission.Default`](https://github.com/mozilla/policy-templates/blob/master/README.md#installaddonspermission) を `false` に設定してください。例えば以下の要領です。

    {
      "policies": {
        "BlockAboutAddons": true,
        "InstallAddonsPermission": {
          "Default": false
        }
      }
    }

これにより、アドオンマネージャの読み込みがブロックされ、設定を変更できない状態になります。また、アドオンのインストール操作も禁止されるようになります。

### 注意事項

この設定は、既にインストール済みの他のアドオンの状態を変更しません。既にインストール済みのアドオンをシステム管理者の判断で強制的に無効化する方法は、[特定のアドオンを常に無効化したい](#disable-addons-by-administrator) を参照してください。

## 特定のアドオンを常に無効化したい {#disable-addons-by-administrator}

キーワード：機能制限、導入時初期設定、集中管理、アドオン、プラグイン

既に導入されているアドオンを、システム管理者の判断で強制的に無効化 (アンインストール) することができます。

### 設定方法

[グループポリシー](../setting-management/#group-policy) または [ポリシー定義ファイル](../setting-management/#policies-json) を用いて、[`Extensions.Uninstall`](https://github.com/mozilla/policy-templates/blob/master/README.md#extensions) に無効化したいアドオンの ID を列挙してください。例えば、[Duplicate Tabs Closer](https://addons.mozilla.org/firefox/addon/duplicate-tabs-closer/) であれば以下の要領です。

    {
      "policies": {
        "Extensions": {
          "Uninstall": ["jid0-RvYT2rGWfM8q5yWxIxAHYAeo5Qg@jetpack"]
        }
      }
    }

これにより、ID が一致するアドオンが、次回以降の起動時に自動的にアンインストールされます。
 (アドオンをインストールしたまま無効化状態とすることはできません。)

## 管理者によるアドオンのインストール手順 {#install-addons-by-administrator}

キーワード：導入時初期設定、アドオン

Firefox や Thunderbird は通常、ユーザーが任意のアドオンをインストールして使用します。以下の手順に則ると、管理者が、そのクライアント上のすべてのユーザーを対象としてアドオンをインストールすることができます。

以下、[Duplicate Tabs Closer](https://addons.mozilla.org/firefox/addon/duplicate-tabs-closer/)をインストールする場合を例にとって解説します。

### パターン 1：起動時に自動的にインストールされるアドオンとして登録する

[グループポリシー](../setting-management/#group-policy) または [ポリシー定義ファイル](../setting-management/#policies-json) を用いて、[`Extensions.Install`](https://github.com/mozilla/policy-templates/blob/master/README.md#extensions) にアドオンの XPI ファイルの URL またはパスを列挙してください。例えば、`192.168.0.10` のホストをファイル配信に使用するのであれば以下の要領です。

    {
      "policies": {
        "Extensions": {
          "Install": [
            "https://192.168.0.10/duplicate_tabs_closer-3.4.1-fx.xpi"
          ]
        }
      }
    }

`192.168.0.10` が Windows のファイルサーバーまたは Samba サーバーである場合、UNC パスを用いた以下の指定の仕方も可能です。

    {
      "policies": {
        "Extensions": {
          "Install": [
            "file:////192.168.0.10/shared/duplicate_tabs_closer-3.4.1-fx.xpi",
            "\\\\\\\\\\192.168.0.10\\shared\\duplicate_tabs_closer-3.4.1-fx.xpi"
          ]
        }
      }
    }

これにより、次回以降の起動時に、各アドオンが自動的にユーザープロファイル配下へアンインストールされます。

この手順でインストールしたアドオンは以下の特徴を持ちます。

* *既に存在しているユーザープロファイルで Firefox を起動した場合も、当該アドオンはインストールされます*。
* 当該アドオンは、ユーザーが自分でインストールしたのと同じ扱いになります。
  * 当該アドオンはアドオンマネージャ上に表示されます。
  * *ユーザーは当該アドオンを削除できます。*
  * *ユーザーは当該アドオンを無効化できます。*
* *当該アドオンは自動更新されます。*

自動更新を利用できない場面において、この方法でインストールしたアドオンを更新するためには、同じアドオンをバージョン番号を付与した別の URL またはバージョン番号を付与した別のパスで `Extensions.Install` に列挙し直して再インストールする必要があります。

### パターン 2：初回起動時 (新規プロファイル作成時) に自動的にインストールされるアドオンとして登録する

この場合のインストール手順は以下の通りです。

 1. Firefox の実行ファイルと同じ位置に `distribution` という名前でフォルダを作成します。

    Firefox が `C:\Program Files\Mozilla Firefox` にインストールされている場合、作成するフォルダのパスは `C:\Program Files\Mozilla Firefox\distribution` となります。

 2. 1.で作成したフォルダの中に `extensions` という名前でフォルダを作成します。
 3. 2.で作成したフォルダの中に、インストールしたいアドオンのインストールパッケージ (XPI ファイル) を設置します。ファイル名はアドオンの内部的な ID に合わせて変更します。

    Duplicate Tabs Closer であれば、ファイル名は `jid0-RvYT2rGWfM8q5yWxIxAHYAeo5Qg@jetpack.xpi` で、最終的なファイルのパスは `C:\Program Files\Mozilla Firefox\distribution\extensions\jid0-RvYT2rGWfM8q5yWxIxAHYAeo5Qg@jetpack.xpi` となります。

 4. ユーザー権限で Firefox を起動します。それが初回起動であれば、アドオンが自動的にインストールされます。

この手順でインストールしたアドオンは以下の特徴を持ちます。

* *既に存在しているユーザープロファイルで Firefox を起動した場合、当該アドオンはインストールされません*。当該アドオンが自動的にインストールされるのは、あくまで、新規の導入時などで新しいプロファイルが作成された場合のみに限られます。
* 当該アドオンは、ユーザーが自分でインストールしたのと同じ扱いになります。
  * 当該アドオンはアドオンマネージャ上に表示されます。
  * *ユーザーは当該アドオンを削除できます。*
  * *ユーザーは当該アドオンを無効化できます。*
* *当該アドオンは自動自動更新されます。*
