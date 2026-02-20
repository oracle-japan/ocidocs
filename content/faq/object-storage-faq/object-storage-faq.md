---
lastmod: 2021-08-11
title: "オブジェクト・ストレージ・サービス 技術FAQ"
description: "Oracle Cloud Infrastructure オブジェクト・ストレージ・サービスに関して、利用時のよくある質問に対する回答や、技術的な問題に対する対処策をまとめています。"
layout: faq
categories:
  - FAQ
  - Storage
tags:
  - FAQ
tagline: "Oracle Cloud Infrastructure オブジェクト・ストレージ・サービスに関する技術FAQです<br>サービス仕様に関する⼀般的な質問や、既知の問題については下記の外部リンクもご参照ください" 
header:
  actions:
    - label: "Object Storageに関するよくある質問"
      url: "https://www.oracle.com/jp/cloud/storage/object-storage-faq.html"
    - label: "既知の問題 - オブジェクト・ストレージ"
      url: "https://docs.oracle.com/ja-jp/iaas/Content/knownissues.htm#objectstorage"
aliases: "/faq/storage/object-storage-faq/"
---

# オブジェクトへのアクセス拒否エラーが発生した場合のトラブルシューティング方法を教えてください

## 質問

オブジェクト・ストレージ・バケット内のオブジェクトへのアクセスを試みたところ、アクセス拒否エラーが発生しました。このような場合のトラブルシューティング方法を教えてください。

## 回答

オブジェクト・ストレージ・バケットへのアクセス方法は下記の２つの方法があります。

1. コンソール、SDK、CLI、その他のツールを使用したREST APIからのアクセス
2. 事前認証済みリクエストを使用したアクセス

それぞれのアクセス方法でアクセスエラーが発生した際のトラブルシューティング方法は下記の通りです：

1. **コンソール、SDK、CLI、その他のツールを使用したREST APIからのアクセスの場合**

   - 自分のしたいアクセスタイプに必要な、IAMポリシーが付与されているかを確認してください。

     **【アクセスタイプ別のポリシー・ステートメント例】**

     1. **オブジェクト・ストレージ管理者によるバケットおよびオブジェクトの管理**：

        すべてのコンパートメントのオブジェクト・ストレージ・バケットおよびオブジェクトのすべての操作を実行する機能

     ```			
     Allow group ObjectAdmins to manage buckets in tenancy
     Allow group ObjectAdmins to manage objects in tenancy 
     ```

     2. **ユーザーによるオブジェクトのオブジェクト・ストレージ・バケットへの書込み**：

        テナンシおよびコンパートメント内の、任意のオブジェクト・ストレージ・バケットにオブジェクトを書き込む機能

     ```
     Allow group ObjectWriters to read buckets in [任意のテナンシおよびコンパートメント]
     Allow group ObjectWriters to manage objects in [任意のテナンシおよびコンパートメント] where any request.permission='OBJECT_CREATE', request.permission='OBJECT_INSPECT'}
     ```

     ​			※特定のコンパートメント内の特定のバケットへのアクセスを制限するには、条件where target.bucket.name='<bucket_name>'を追加してください。

     3. **ユーザーによるオブジェクトのオブジェクト・ストレージ・バケットからのダウンロード**：

     　テナンシおよびコンパートメント内の、任意のオブジェクト・ストレージ・バケットからオブジェクトをダウンロードする機能

     ```
     Allow group ObjectReaders to read buckets in [任意のテナンシおよびコンパートメント]
     Allow group ObjectReaders to read objects in [任意のテナンシおよびコンパートメント]
     ```

     ​			※特定のコンパートメント内の特定のバケットへのアクセスを制限するには、条件where target.bucket.name='<bucket_name>'を追加してください。

   - 詳細は[Oracle Cloud Infrastructure ドキュメント：オブジェクト・ストレージ管理者によるバケットおよびオブジェクトの管理](https://docs.oracle.com/ja-jp/iaas/Content/Identity/Concepts/commonpolicies.htm#) をご覧ください。

   - なお、適切なIAMポリシーが設定されているにもかかわらず、アクションを実行しようとした際に、「権限がない」または「認可されていない」というメッセージが表示された場合は、IAMポリシーが正しく設定されていない可能性があります。必要なアクセスのタイプと作業するコンパートメントを管理者に確認してください。	

   - 【参考】[Oracle Cloud Infrastructure ドキュメント「バケットの管理」](https://docs.oracle.com/ja-jp/iaas/Content/Object/Tasks/managingbuckets.htm#Managing_Buckets)

1. **事前認証済みリクエストを使用したアクセスの場合**

   - 事前認証済みリクエストとは？
     - 事前認証済リクエストを作成すると、一意のURLが生成されます。このURLを提供するすべての人は、curlやwgetなどの標準HTTPツールを使用して、特定のオブジェクト・ストレージ・リソースにアクセスできるようになります。事前認証済リクエストは、OCIの資格証明を持たない人にも、バケットにアクセスを可能にするアクセス方法です。
   - 事前認証済みリクエストが使用できないときには、下記項目を確認してください。
     1. 事前認証済リクエスト作成者の権限が変更されていないか
     2. 事前認証済リクエストを作成したユーザーが削除されていないか
     3. 事前認証済リクエストを作成したフェデレーテッド・ユーザーが、そのリクエストを作成したときのユーザー機能を失っていないか
     4. 事前認証済リクエストが期限切れになっていないか
        - 事前認証済リクエストには、URIの有効期限があり、期限を過ぎたURLは無効になります。
   - 【参考】[Oracle Cloud Infrastructure ドキュメント「事前認証済リクエストの使用」](https://docs.oracle.com/ja-jp/iaas/Content/Object/Tasks/usingpreauthenticatedrequests.htm#Using_PreAuthenticated_Requests)

**関連情報 :**  

- [Oracle Cloud Infrastructure ドキュメント：オブジェクト・ストレージ管理者によるバケットおよびオブジェクトの管理](https://docs.oracle.com/ja-jp/iaas/Content/Identity/Concepts/commonpolicies.htm#) 
- [Oracle Cloud Infrastructure ドキュメント：バケットの管理](https://docs.oracle.com/ja-jp/iaas/Content/Object/Tasks/managingbuckets.htm#Managing_Buckets)
- [Oracle Cloud Infrastructure ドキュメント：事前認証済リクエストの使用](https://docs.oracle.com/ja-jp/iaas/Content/Object/Tasks/usingpreauthenticatedrequests.htm#Using_PreAuthenticated_Requests)

<br />

# バケット間でオブジェクトのデータをコピーしたい場合のセットアップ方法を教えてください。

## 質問

オブジェクト・ストレージ・バケット間で、オブジェクトのデータをコピーしたい場合のセットアップ方法を教えてください。

## 回答

オブジェクト・ストレージでは、オブジェクトを同一リージョン内の他のバケット、および他のリージョン内のバケットにコピーすることができます。

バケット間のコピーを実施する上で、下記項目が設定されていることを、事前にご確認ください。

1. 【ユーザ権限】適切なアクセス権が、IAMポリシーで定義されていること
   - オブジェクト・コピーを実行する場合、ソース・バケットおよび宛先バケットの両方に対して、必要なアクセス権を持っている必要があります。ソース・バケットおよび宛先バケット内のオブジェクトを管理する権限も必要です。
2. 【サービス権限】オブジェクト・ストレージはリージョナル・サービスであるため、代理でコピー操作を実行する各リージョンのオブジェクト・ストレージ・サービスを認可する必要があります。
   - たとえば、オブジェクト・ストレージ・サービスをJapan East(Tokyo)リージョンで認可して、代理でオブジェクトを管理できます。オブジェクト・ストレージ・サービスを認可すると、Japan East(Tokyo)リージョンのバケットに格納されているオブジェクトを、別のリージョンのバケットにコピーできます。

オブジェクトのデータを手動でコピーしたい場合の手順詳細はこちらをご参照ください。
[Oracle Cloud Infrastructure ドキュメント：コンソールを使用したオブジェクトのコピー](https://docs.oracle.com/ja-jp/iaas/Content/Object/Tasks/copyingobjects.htm#usingconsole)

**関連情報 :**  

- [Oracle Cloud Infrastructure ドキュメント：オブジェクトのコピー → コンソールを使用したオブジェクトのコピー](https://docs.oracle.com/ja-jp/iaas/Content/Object/Tasks/copyingobjects.htm#usingconsole)

<br />

# ライフサイクルポリシーを使用して、バケット内のオブジェクトをすべて削除するにはどうすればよいですか？

## 質問

ライフサイクルポリシーを使用して、バケット内のオブジェクトをすべて削除するにはどうすればよいですか？

## 回答

下記項目が設定されていることを、事前にご確認ください：

1. 【ユーザ権限】適切なアクセス権が、IAMポリシーで定義されていること

   - Oracle Cloud Infrastructureを使用するには、管理者が記述するポリシー で、コンソールまたはSDK、CLIまたはその他のツールを使用したREST APIのどれを使用しているかにかかわらず、必要なアクセスのタイプを付与されている必要があります。
   - なお、適切なIAMポリシーが設定されているにもかかわらず、アクションを実行しようとした際に、「権限がない」または「認可されていない」というメッセージが表示された場合は、IAMポリシーが正しく設定されていない可能性があります。必要なアクセスのタイプと作業するコンパートメントを管理者に確認してください。

2. 【サービス権限】オブジェクト・ライフサイクル・ポリシーを実行するには、代理でオブジェクトをアーカイブおよび削除する権限をサービスに付与する必要があります。これを行うには、テナンシのルート・コンパートメントに次のポリシーを作成します:

   ```
   Allow service objectstorage-<region_identifier> to manage object-family in compartment <compartment_name>
   ```

   オブジェクト・ストレージはリージョナル・サービスであるため、ライフサイクル・ポリシーを使用する各リージョンでオブジェクト・ストレージ・サービスを認可する必要があります。オブジェクト・ストレージにより、データが権限のないリージョンから読み取られないことが保証されます。

ライフサイクルポリシーを作成する際に、「ライフサイクル・アクション」にてポリシーを適用したバケット内のオブジェクトに対するアクションを設定します。

バケット内のオブジェクトを削除したい場合には、このライフサイクル・アクションを「削除」に設定します。

ライフサイクルポリシーの設定方法は下記のドキュメントをご覧ください。

- コンソールを使用して作成する場合
  - [Oracle Cloud Infrastructure ドキュメント：オブジェクト・ライフサイクルの使用 → コンソールの使用](https://docs.oracle.com/ja-jp/iaas/Content/Object/Tasks/usinglifecyclepolicies.htm#usingconsole)
- CLIを使用して作成する場合
  - [Oracle Cloud Infrastructure ドキュメント：オブジェクト・ライフサイクルの使用 → CLIの使用](https://docs.oracle.com/ja-jp/iaas/Content/Object/Tasks/usinglifecyclepolicies.htm#cli)

**関連情報 :**  

- [Oracle Cloud Infrastructure ドキュメント：オブジェクト・ライフサイクルの使用](https://docs.oracle.com/ja-jp/iaas/Content/Object/Tasks/usinglifecyclepolicies.htm)

<br />

# オブジェクト・ストレージ・バケット別のコストを一覧するにはどうしたらよいですか?

## 質問

オブジェクト・ストレージ・バケット別のコストを一覧するにはどうしたらよいですか?

## 回答

バケット別のコストを一覧する方法は大きく2つの方法があります：

1. コスト分析で一覧する
2. コストおよび使用状況レポートを閲覧する

以下、それぞれの方法を解説します。

1. コスト分析で一覧する
   - コスト分析では、テナンシ内のリソースのコスト・使用量を可視化する機能です。
     コストを可視化したいリソースの、期間・特定のタグ・コンパートメント・サービスまたはフィルタでフィルタ処理し、グループ化して表示することができます。
   - バケット別のコストを閲覧したい場合には、タグを使用したフィルタリングが有効です。
     設定方法は下記の通り：
     1. コスト分析を行いたいバケットにタグを追加する。
        - この際に、タグはタグ・ネームスペースを持ったタグである必要があります。コスト分析では、タグ・ネームスペースを持たないフリーフォームタグを使用してフィルタリングすることはできませんので、ご注意ください。
        - タグについてはドキュメントをご参照ください。
          -  [「タグおよびタグ・ネームスペースの管理」](https://docs.oracle.com/ja-jp/iaas/Content/Tagging/Tasks/managingtagsandtagnamespaces.htm)
          - [「フリーフォームタグの理解」]( https://docs.oracle.com/ja-jp/iaas/Content/Tagging/Concepts/understandingfreeformtags.htm)
     2. 「グループ化ディメンション」で「タグ」を選択し、１で設定したタグを選択。
        - 手順：[「コスト分析の概要」](https://docs.oracle.com/ja-jp/iaas/Content/Billing/Concepts/costanalysisoverview.htm)
   
2. コストおよび使用状況レポートを閲覧する

   - コストおよび使用状況レポートは、毎日生成されてオブジェクト・ストレージ・バケットに格納されるカンマ区切り値(CSV)ファイルです。このレポートはダウンロードして閲覧することが可能です

   - コストおよび使用状況レポートへアクセスする際には、下記事項が適切に設定されていることをご確認ください。

     1. 【ユーザ権限】適切なアクセス権が、IAMポリシーで定義されていること

     2. コストおよび使用状況レポートを使用するには、管理者が記述するポリシー で、コンソールまたはSDK、CLIまたはその他のツールを使用したREST APIのどれを使用しているかにかかわらず、次のポリシー・ステートメントが定義されている必要があります。

        ```
        define tenancy usage-report as ocid1.tenancy.oc1..aaaaaaaaned4fkpkisbwjlr56u7cj63lf3wffbilvqknstgtvzub7vhqkggq
        
        endorse group <group> to read objects in tenancy usage-report
        ```

        なお、適切なIAMポリシーが設定されているにもかかわらず、アクションを実行しようとした際に、「権限がない」または「認可されていない」というメッセージが表示された場合は、IAMポリシーが正しく設定されていない可能性があります。必要なアクセスのタイプと作業するコンパートメントを管理者に確認してください。

   - コンソールおよびAPIを使用したアクセスが可能です。詳細は下記のURLをご確認ください。

     - [「コストおよび使用状況レポートへのアクセス」 → コンソールの使用](https://docs.oracle.com/ja-jp/iaas/Content/Billing/Tasks/accessingusagereports.htm#console)
     - [「コストおよび使用状況レポートへのアクセス」→ APIの使用](https://docs.oracle.com/ja-jp/iaas/Content/Billing/Tasks/accessingusagereports.htm#api)

   - コストおよび使用状況レポートのcsvファイルでは、作成されたリソースを一覧することができます。コストを比較したいバケットの詳細情報が記載された行を探し、コスト欄を確認することが可能です。

**関連情報 :**  

- [Oracle Cloud Infrastructureドキュメント：コスト分析の概要](https://docs.oracle.com/ja-jp/iaas/Content/Billing/Concepts/costanalysisoverview.htm)
- [Oracle Cloud Infrastructureドキュメント：コストおよび使用状況レポートへのアクセス ](https://docs.oracle.com/ja-jp/iaas/Content/Billing/Tasks/accessingusagereports.htm)

<br />



# アーカイブ・ストレージ層から一括でリストアしたい場合の、効率的なリストア方法を教えてください。

## 質問

アーカイブ・ストレージ層にある大規模なデータを一括でリストアしたい場合の、効率的なリストア方法を教えてください。

## 回答

アーカイブ・ストレージ層にあるデータをリストアする方法は、コンソールを使用した方法と、CLIを使用した方法の２つがあります。

また、アーカイブ・ストレージのオブジェクトをリストアするには、OBJECT_RESTORE権限が必要です。事前にご確認ください。

１．コンソールを使用したリストア方法

バケット詳細からリストアすることができます。複数のオブジェクトを一括でリストアしたい場合には、すべてのオブジェクトがフォルダを使用することなくフラットに保存されている必要があります。（実際にはフラットに保存されていますが、仮想的に階層構造で管理することができます。）

２．CLIを使用したリストア方法

下記のコマンドを実行するとリストアが始まります。

```
oci os object restore --namespace <object_storage_namespace> --bucket-name <archive_bucket_name> --name <archived_object_name> [--hours <#_of_hours>]
```

詳細はOCI CLIのコマンドレファレンス[【OCI CLI command reference】](https://docs.oracle.com/en-us/iaas/tools/oci-cli/2.17.0/oci_cli_docs/cmdref/os/object/restore.html)をご確認ください。



**関連情報 :**  

- [Oracle Cloud Infrastructure ドキュメント：コンソールの使用 → アーカイブ・ストレージからオブジェクトをリストアするには](https://docs.oracle.com/ja-jp/iaas/Content/Object/Tasks/managingobjects.htm#usingconsole)

<br />

# オブジェクト・ストレージにイベント通知を設定しました。イベントがトリガーされたかを確認する方法を教えてください。

## 質問

オブジェクト・ストレージ・リソースにイベントを設定し、アクションとして通知を設定しました。イベントがトリガーされたかどうかを確認する方法を教えてください。

## 回答

イベント・サービスのイベントメトリックの「発行済みメトリック」では、コンパートメント内のリソースによって発行されたイベントの合計数が分かります。
時間の範囲を絞り、指定した範囲でトリガーされたイベントに対する結果をグラフ表示することができます。

詳細はドキュメント[「イベント・メトリック」](https://docs.oracle.com/ja-jp/iaas/Content/Events/Reference/eventsmetrics.htm)をご確認ください。

**関連情報 :**  

- [Oracle Cloud Infrastructure ドキュメント：イベント・メトリック](https://docs.oracle.com/ja-jp/iaas/Content/Events/Reference/eventsmetrics.htm)

<br />

# バケット間にレプリケーションをセットアップしたのですが、オブジェクトがレプリケートされません。どうすれば解決できますか?

## 質問

オブジェクト・ストレージ・バケット間にクロスリージョンレプリケーションまたは同一リージョンレプリケーションをセットアップしたのですが、オブジェクトがレプリケート先バケットにレプリケートされません。どうすれば解決できますか?

## 回答

レプリケートできないときに確認すべき事項は下記の通りです：

1. 【ユーザ権限】レプリケーションを構成するときは、ソース・バケットと宛先バケットの両方に対して必要なアクセス権を持っている必要があります。ソース・バケットおよび宛先バケット内のオブジェクトを管理する権限も必要です。

   ＜ポリシー・ステートメント例＞
   - 管理者の場合：テナンシのオブジェクト・ストレージ・サービス・リソースに対してすべての操作を実行できるようにするポリシー・ステートメント例
   ```
    Allow group [IAMグループ名] to manage object-family in tenancy
   ```
   - テナンシの特定のコンパートメント内のバケットとオブジェクトを管理できるようにするポリシー・ステートメント例:
   ```
    Allow group [IAMグループ] to manage buckets in [コンパートメント名]
    Allow group [IAMグループ] to manage objects in [コンパートメント名]
   ```

2. 【サービス権限】オブジェクト・ストレージはリージョナル・サービスであるため、代理でレプリケーションを実行する各リージョンのオブジェクト・ストレージ・サービスを認可する必要があります。

   ＜ポリシー・ステートメント例＞
   - オブジェクト・ストレージ・サービスが米国西部(フェニックス)リージョンのテナンシ内のリソースに対して、すべての操作を実行できるようにするポリシー・ステートメント例：

   ```
    Allow service objectstorage-us-phoenix-1 to manage object-family in     tenancy
   ```
   - オブジェクト・ストレージ・サービスが、米国西部(フェニックス)リージョン内の特定のコンパートメント内のリソースに対して、すべての操作を実行できるようにするポリシー・ステートメント例：
   ```
    Allow service objectstorage-us-phoenix-1 to manage object-family in compartment [コンパートメント名]		
   ```
3. 宛先バケットが作成されていることを確認する。
   - レプリケーション・ポリシーの作成では、宛先バケットは自動的に作成されません。ソース・バケットにレプリケーション・ポリシーを作成する前に、宛先バケットを作成します。

4. ソースバケットに指定しようとしているバケットが、別バケットのレプリケート先バケットでないことを確認する。
   - 宛先バケットはレプリケーション・ソースになることはできません。連鎖レプリケーションはサポートされていません。

**関連情報 :**  

- [Oracle Cloud Infrastructure ドキュメント：レプリケーションの使用](https://docs.oracle.com/ja-jp/iaas/Content/Object/Tasks/usingreplication.htm)

<br />

# オブジェクトストレージに大容量のファイルをアップロードするときにパフォーマンスを最適化する方法を教えてください。

## 質問

オブジェクトストレージに大容量のファイル(1 GB 以上)をアップロードするときにパフォーマンスを最適化する方法を教えてください。

## 回答

大容量のファイルを一括アップロードしたいときには、CLIのbulk-upload コマンドを使用します。
詳細はドキュメントの「[オブジェクトバケットを一括アップロードするには](https://docs.oracle.com/ja-jp/iaas/Content/Object/Tasks/managingobjects.htm#cli) 」の項目をご確認ください。

コマンドの構成は次の通りです：

```
$ oci os object bulk-upload -ns [名前空間] -bn [アップロード先バケット名] --src-dir [対象ファイルが格納されているディレクトリパス] --no-multipart
```

パラメータの詳細や具体的な使用方法については、コマンドリファレンスをご確認ください。

- [コマンドリファレンス](https://docs.oracle.com/en-us/iaas/tools/oci-cli/2.24.5/oci_cli_docs/cmdref/os/object/bulk-upload.html#description)
- [【ドキュメント】オブジェクトの管理](https://docs.oracle.com/ja-jp/iaas/Content/Object/Tasks/managingobjects.htm#)

**関連情報 :**  

- [Oracle Cloud Infrastructure ドキュメント：オブジェクトのコピー>>コンソールを使用したオブジェクトのコピー](https://docs.oracle.com/ja-jp/iaas/Content/Object/Tasks/copyingobjects.htm#usingconsole)

<br />

