---
lastmod: 2021-07-07
title: "コンピュート・サービス 技術FAQ"
description: "Oracle Cloud Infrastructure コンピュート・サービスに関して、利用時のよくある質問に対する回答や、技術的な問題に対する対処策をまとめています。"
layout: faq
categories:
  - FAQ
  - Services
  - Compute
tags:
  - FAQ
tagline: "Oracle Cloud Infrastructure コンピュート・サービスに関する技術FAQです<br>サービス仕様に関する⼀般的な質問や、既知の問題については下記の外部リンクもご参照ください" 
header:
  actions:
    - label: "コンピュートに関するよくある質問"
      url: "https://www.oracle.com/jp/cloud/compute/faq.html"
    - label: "既知の問題 - コンピュート"
      url: "https://docs.oracle.com/ja-jp/iaas/Content/knownissues.htm#compute"
aliases: "/faq/services/compute/compute-faq/"
---

# コンソールにログインできない

## 質問

ブラウザからコンソールのURLにアクセスしたのですがページが表示されません。

## 回答

まずはクラウド・アカウント名、ユーザー名、パスワードが正しいかを確認してください。ログイン方法は [Oracle Cloud Infrastructureドキュメント : コンソールのサインイン](https://docs.oracle.com/ja-jp/iaas/Content/GSG/Tasks/signingin.htm) を参考にしてください。

問題の切り分けや対処として、以下の対応が考えられます。

1. [Oracle Cloud Infrastructureドキュメント : サポートされているブラウザ](https://docs.oracle.com/ja-jp/iaas/Content/GSG/Tasks/signingin.htm#Supporte) を確認し、サポートされているブラウザをご利用ください。

1. サポートされている他のブラウザでも同様の問題が発生するかどうかを確認します。

    どのブラウザでも問題が発生する場合
    - クライアントが利用しているネットワークのファイアーウォール設定やPCのセキュリティ設定でコンソールURLにアクセス可能かどうかを確認してください。
    - クライアントPCの時刻設定が正しいか確認してください。PCの時刻がずれている場合は正しく設定して下さい。コンソールからはデータ取得のためにOCIに対しAPIリクエストが発行されますが、クライアントとOCIの時刻とのずれが大きいとAPIが正しく実行できずエラーになります。

    特定のブラウザのみで問題が発生する場合
    - プライベートモード(シークレットモード)でログインする
    - キャッシュのクリア

上記を実施しても解決しない場合はサポートにSRを起票してください。

<br />

# コンピュート・インスタンスにsshできない

## 質問

Linuxのコンピュート・インスタンスを作成しましたが、クライアントからsshでのログインができません。

## 回答

問題の切り分けや対処として、以下の対応が考えられます。

- クライアント側でお使いのネットワークのファイアーウォール設定でsshの通信が許可されているかを確認し、必要に応じてsshの通信を許可してください。  
    不明な場合はネットワーク管理者の方にご確認ください。

- クライアント側ネットワークでプロキシサーバーを経由する必要があるか確認してください。  
    プロキシサーバーの経由が必要な場合は、sshクライアント・ソフトウェアに適切にプロキシ設定を行ってください。不明な場合はネットワーク管理者の方にご確認ください。  
     (例) Tera Term の場合は、メニュー 設定 → プロキシ から設定

- OCI上でコンピュート・インスタンスを配置しているサブネットの設定を確認します。  
    サブネットに設定されているセキリティ・リスト、またはインスタンスに関連づけられているネットワーク・セキュリティ・グループのルールに以下のイングレス・ルールが存在するかを確認してください。ない場合はルールを追加してください。  
    - ステートレス：いいえ
    - ソース：0.0.0.0/0 (またはアクセス元のクライアントのパブリックIPアドレス範囲)
    - IPプロトコル：TCP
    - ソース・ポート範囲：All
    - 宛先ポート範囲：22

- 切り分けのために、インスタンスのIPアドレスに対してpingが応答するかを確認します、**デフォルトではインスタンスへのpingは通らない**設定になっていますので、セキュリティ・リストまたはネットワーク・セキュリティ・グループのルールの以下のイングレス・ルールを以下のように編集します。
    - ステートレス：いいえ
    - ソース：0.0.0.0/0 (またはアクセス元のクライアントのパブリックIPアドレス範囲)
    - IPプロトコル：ICMP
    - タイプとコード：3, 4 (デフォルト) → All に変更

    上記の変更を適用後、クライアント側からインスタンスのIPアドレスに対してpingを実行します。正しく応答が返ってくる場合は、ネットワークは到達できていますが、sshでのログインに問題があることがわかります。sshに問題がある場合は、インスタンスのsshの設定や、sshクライアントの設定を見直してください。

- カスタム・イメージをご利用の場合はOSの設定でsshが許可されているかを確認してください。  
    オラクル提供イメージから作成したLinuxインスタンスの場合はデフォルトでsshは許可されていますが、カスタム・イメージの場合は、イメージの元となったインスタンス側でsshを許可しないように設定されている場合があります。

上記を実施しても解決しない場合はサポートにSRを起票してください。


<br />

# インスタンスに関する計画メンテナンス通知を受け取った場合に理解しておくことは何ですか?

## 質問

コンピュート・インスタンスの計画メンテナンスのお知らせメールが届きました。または、コンソール上で計画メンテナンスの通知が表示されています。どのようなことを理解しておけばいいでしょうか?

## 回答

稼働中のインスタンスに影響するサーバーなどのインフラコンポーネントの計画メンテナンスをする必要がある場合、事前に通知がおこなわれ、コンソール上のお知らせページでの表示と管理者にメールが送信されます。  
該当のインスタンスの詳細ページを確認すると、メンテナンス実施に関する項目が表示されており、通常14~16日後にメンテナンス作業の予定実施日時が記載されています。  

もし、通知に記載された予定日時までの間に、ユーザーが何のアクションも起こさなかった場合は、その日時にインスタンスが新しいホストに移行します。

もし、インスタンスがライブ・マイグレーションをサポートしているシェイプ[^1]の場合は、移行はライブ・マイグレーションによって実施され、インスタンスが起動中ステータスのまま新しいホスト移行が完了します。  

{{< hint type=note title="[^1]" >}}
ライブ・マイグレーション対象のシェイプは、[Oracle Cloud Infrastructureドキュメント : Live Migration](https://docs.oracle.com/en-us/iaas/Content/Compute/Tasks/movinganinstance.htm#live-migration) をご確認ください。
{{< /hint >}}

もし、インスタンスがライブ・マイグレーションをサポートしていないシェイプの場合は、ホストの移行はリブート・マイグレーション(再起動移行)によって実施され、短いダウンタイムを伴います。  
リブート・マイグレーションの実施に当たっては、事前に注意しておくべき内容が [Oracle Cloud Infrastructureドキュメント : Prerequisites for Reboot Migration](https://docs.oracle.com/en-us/iaas/Content/Compute/Tasks/movinganinstance.htm#prerequisites-reboot) にありますので、こちらもご確認ください。

また、ユーザーは、通知に記載された予定日時よりも前の任意のタイミングで新しいホストへのインスタンスの移行を実施したり、スケジュールを行うことができます。

ベアメタル・インスタンスやローカルNVMeを内蔵するDenseIO、HPCなどのシェイプは、ライブ・マイグレーションやリブート・マイグレーションによって別のホストにインスタンスを移動して稼働を継続させることができず、手動で内蔵データの移行作業を行う必要がある場合があります。詳細は、[Oracle Cloud Infrastructureドキュメント : 手動移行によるインスタンスの移動](https://docs.oracle.com/ja-jp/iaas/Content/Compute/Tasks/movinganinstance.htm#manual) をご参照ください。

<br />

# インスタンスの状態が変化した時にメール通知を受け取りたい

## 質問

コンピュート・インスタンスの状態が変化(起動、終了、シェイプ変更などのインスタンスの更新など)した際にメール通知を受け取りたいです。どのように設定すればいいでしょうか?

## 回答

[イベント(Event)サービス](https://www.oracle.com/jp/cloud-native/events-service/) でインスタンスの状態の変化を捉え、 [通知(Notification)サービス](https://www.oracle.com/jp/devops/notifications/) に連携してユーザーに通知します。
コンピュート・インスタンスにおいて状態変化に応じて生成できるイベントのタイプは [Oracle Cloud Infrastructureドキュメント : イベントを生成するサービス](https://docs.oracle.com/ja-jp/iaas/Content/Events/Reference/eventsproducers.htm) のコンピュートの項を参照してください。

**手順概要 :**

1. [Oracle Cloud Infrastructureドキュメント : イベントの開始](https://docs.oracle.com/ja-jp/iaas/Content/Events/Reference/eventsproducers.htm) に従って、通知先となる トピックとサブスクリプション を作成します
1. 続いて以下の内容のイベントのルールを作成します。
    - ルール条件
        - サービス名：Compute
        - イベント・タイプ：通知したいイベントを選択  
            インスタンスの起動や停止を通知したい場合は、Instance - Action Begin、Instance - Action End を選択します。
    - アクション
        - アクション・タイプ：通知
        - 通知コンパートメント：手順1で作成したトピックのあるコンパートメントを指定
        - トピック：手順1で作成したトピックを指定

<br />

# インスタンスに永続的なパブリックIPを付与する方法

## 質問
コンピュート・インスタンスの終了後、同一のパブリックIPアドレスを別のコンピュート・インスタンスに関連付けたいと考えています。永続的なパブリックIPアドレスをコンピュート・インスタンスに関連付けるにはどうしたら良いですか。

## 回答
OCI のパブリック IP アドレスには、エフェメラル・パブリック IP アドレスと、予約済みパブリック IP アドレスの２種類が存在します。永続的なパブリック IP アドレスを利用したい場合、予約済みパブリック IP アドレスを取得します。

- **エフェメラル・パブリック IP アドレス**  
    インスタンスの存続期間中のみ保持される、一時的なパブリック IP アドレスです。インスタンスの起動・停止で変更されることはありません。

- **予約済みパブリック IP アドレス**  
    割り当てられているインスタンスの存続期間を超えて永続的に保持可能なパブリック IP アドレスです。割り当てを解除し、いつでも好きなときに別のインスタンスに再割り当てすることができます。

予約済みパブリック IP アドレスを取得し、コンピュート・インスタンスに割り当てるには、以下の手順を実行します。

1. [Oracle Cloud Infrastructureドキュメント : プールに新規予約済パブリックIPを作成するには](https://docs.oracle.com/ja-jp/iaas/Content/Network/Tasks/managingpublicIPs.htm#console-reserved) を参考に、予約済みパブリック IP アドレスを取得します。

2. [Oracle Cloud Infrastructureドキュメント : プライベートIPに予約済パブリックIPを割り当てるには](https://docs.oracle.com/ja-jp/iaas/Content/Network/Tasks/managingpublicIPs.htm#console-reserved) を参考に、コンピュート・インスタンスのアタッチされた VNIC に、取得した予約済みパブリック IP アドレスを割り当てます。

既にコンピュート・インスタンスに エフェメラル・パブリック IP アドレス、または、予約済みパブリック IP アドレスが割り当てられている場合は、事前に対象のパブリック IP アドレスを削除する必要があります。詳細は [Oracle Cloud Infrastructureドキュメント : インスタンスからエフェメラル・パブリックIPを削除するには](https://docs.oracle.com/ja-jp/iaas/Content/Network/Tasks/managingpublicIPs.htm#console-ephemeral) または  [Oracle Cloud Infrastructureドキュメント : 予約済パブリックIPを割当て解除してプールに戻すには](https://docs.oracle.com/ja-jp/iaas/Content/Network/Tasks/managingpublicIPs.htm#console-reserved) をご参照ください。

<br />

# インスタンスに仮想NIC(VNIC)を追加する方法

## 質問

コンピュート・インスタンスを複数の VCN 内のサブネットに接続させたいと考えています。セカンダリ VNIC はどのように追加できますか?

## 回答

セカンダリ VNIC は、コンピュート・インスタンスの起動後にのみ追加可能です。  
インスタンスにアタッチできる VNIC の数には制限があり、シェイプによって異なります。これらの制限については、[Oracle Cloud Infrastructureドキュメント : コンピュート・シェイプ](https://docs.oracle.com/ja-jp/iaas/Content/Compute/References/computeshapes.htm) を参照してください。

セカンダリ VNIC を追加するには、以下の手順を実行します。
1. コンソール画面から、対象のコンピュート・インスタンスにセカンダリVNICを作成します。
    - [Oracle Cloud Infrastructureドキュメント : セカンダリVNICを作成してアタッチするには](https://docs.oracle.com/ja-jp/iaas/Content/Network/Tasks/managingVNICs.htm#console)
    
1. セカンダリ VNIC を作成後、コンピュート・インスタンスの種類によっては、OS へ認識させるために構成する必要があります。  
Linux VM インスタンスでは OS によって自動的にセカンダリ VNIC が認識されます。  
その他の場合は、下記の手順を参考に手動でセカンダリVNICをOSに構成します。
    - [Oracle Cloud Infrastructureドキュメント : Linux BM インスタンスでセカンダリ VNIC を構成する](https://docs.oracle.com/ja-jp/iaas/Content/Network/Tasks/managingVNICs.htm#Linux)
    - [Oracle Cloud Infrastructureドキュメント : Windows インスタンスでセカンダリ VNIC を構成する](https://docs.oracle.com/ja-jp/iaas/Content/Network/Tasks/managingVNICs.htm#Windows)

<br />

# インスタンスに追加のプライベートIPアドレスを設定する方法

## 質問

コンピュート・インスタンスに、セカンダリプライベートIPアドレスを設定したいと考えています。どうすればよいですか?

## 回答

セカンダリ・プライベートIPアドレスは、コンピュート・インスタンスのプライマリVNIC、またはセカンダリVNICに追加することが可能です。  
その際のIPアドレスは、VNICのプライマリ・プライベートIPアドレスが存在しているのと同じサブネットのCIDRから取得されます。

インスタンスのVNICのセカンダリ・プライベートIPを、同じサブネットに属している別のインスタンスのVNICに移動することもできます。

セカンダリプライベート IP アドレスを取得し、コンピュート・インスタンスに割り当てるには、以下の手順を実行します。
1. コンピュート・インスタンスにアタッチされた VNIC にセカンダリ・プライベート IP アドレスを割り当てます。
    - [Oracle Cloud Infrastructureドキュメント : VNICに新規セカンダリ・プライベートIPを割り当てるには](https://docs.oracle.com/ja-jp/iaas/Content/Network/Tasks/managingIPaddresses.htm#console)

2. セカンダリ・プライベート IP を VNIC に割り当てた後、OS がその IP アドレスを使用するよう構成する必要があります。
    - [Oracle Cloud Infrastructureドキュメント : Linux: セカンダリIPアドレスの詳細](https://docs.oracle.com/ja-jp/iaas/Content/Network/Tasks/managingIPaddresses.htm#Linux)
    - [Oracle Cloud Infrastructureドキュメント : Windows: セカンダリIPアドレスの詳細](https://docs.oracle.com/ja-jp/iaas/Content/Network/Tasks/managingIPaddresses.htm#Windows)

{{< hint type=tip >}}
セカンダリIPの付与については、OCIチュートリアル[インスタンスにセカンダリIPを付与する](https://oracle-japan.github.io/ocitutorials/intermediates/attaching-secondary-ips/) でも手順を紹介しています
{{< /hint >}}

<br />

# インスタンスのバックアップにはどのような方法がありますか? またバックアップを自動化できますか?

## 質問

コンピュート・インスタンスのバックアップを作成したいと考えています。バックアップにはどうような方法がありますか? また、自動化はどのような方法がありますか?

## 回答

コンピュート・インスタンスのバックアップには、カスタム・イメージの作成、または、ブート・ボリュームのバックアップ機能が利用できます。  
ブート・ボリュームは、Oracle Cloud Infrastructure ブロック・ボリュームのバックアップ機能を使用して、バックアップを作成することができ、またバックアップポリシーによって自動化することもできます。  
コンピュート・インスタンスにブート・ボリューム以外のブロック・ボリュームをアタッチしている場合は、あわせてブロック・ボリュームのバックアップも必要です。

カスタム・イメージの作成、および、ブート・ボリュームのバックアップについては、下記資料もあわせてご参照ください。
    - [Oracle Cloud Infrastructure 技術資料 - コンピュート概要](https://oracle-japan.github.io/ocidocs/services/compute/compute-100/)
  
カスタム・イメージを作成するには、以下の手順を実行します。
1. 対象のコンピュート・インスタンスからカスタム・イメージの作成を行います。
    - [Oracle Cloud Infrastructureドキュメント : カスタム・イメージを作成するには](https://docs.oracle.com/ja-jp/iaas/Content/Compute/Tasks/managingcustomimages.htm#Using2)

    {{< hint type=important title="注意" >}}
実行中のインスタンスのイメージを作成すると、インスタンスはシャットダウンし、数分間使用できなくなります。  
プロセスが完了すると、インスタンスが再起動されます。
    {{< /hint >}}

ブート・ボリュームのバックアップ作成、およびリストアを行うには、以下の手順を実行します。

1. 対象のコンピュート・インスタンスのブート・ボリュームから手動バックアップの作成を行います。
    - [Oracle Cloud Infrastructureドキュメント : ブート・ボリュームのバックアップ](https://docs.oracle.com/ja-jp/iaas/Content/Block/Tasks/backingupabootvolume.htm)

2. ブート・ボリュームのバックアップから、新しいブート・ボリュームを作成します。
    - [Oracle Cloud Infrastructureドキュメント : ブート・ボリュームのリストア](https://docs.oracle.com/ja-jp/iaas/Content/Block/Tasks/creatingbootvolume.htm)

3. インスタンスをバックアップ時点に復元したい場合は、リストアしたブート・ボリュームからインスタンスの作成を行います。

4. 既存のインスタンスへアタッチする場合は、対象のコンピュート・インスタンスのブート・ボリュームをデタッチした上で、リストアしたブート・ボリュームをアタッチします。
    - [Oracle Cloud Infrastructureドキュメント : ブート・ボリュームのデタッチ](https://docs.oracle.com/ja-jp/iaas/Content/Block/Tasks/detachingabootvolume.htm)
    - [Oracle Cloud Infrastructureドキュメント : ブート・ボリュームのアタッチ](https://docs.oracle.com/ja-jp/iaas/Content/Block/Tasks/attachingabootvolume.htm)

    {{< hint type=important title="情報" >}}
バックアップはオブジェクトストレージに保持されるため、容量に応じたコストがかかります。
    {{< /hint >}}

    {{< hint type=tip title="情報" >}}  
ボリュームのバックアップについては、[OCIチュートリアル ブロック・ボリュームをバックアップする](https://oracle-japan.github.io/ocitutorials/intermediates/taking-block-volume-backups/) でも手順を紹介しています
    {{< /hint >}}

ブート・ボリュームのバックアップを自動化するには、以下の手順を実行します。

1. 自動化したいブート・ボリュームにバックアップ・ポリシーの割り当てを行います。
    - [Oracle Cloud Infrastructureドキュメント : ボリュームへのバックアップ・ポリシー割当ての管理](https://docs.oracle.com/ja-jp/iaas/Content/Block/Tasks/schedulingvolumebackups.htm#assignPolicies)


<br />

# インスタンスを別の可用性ドメインに移動させることはできますか?

## 質問

既存のコンピュート・インスタンスを、別の可用性ドメインに、移動、またはコピーする必要があります。どうすれば良いですか?

## 回答

既存のインスタンスを直接別の可用性ドメインに移動することはできません。
代わりに、対象のコンピュート・インスタンスからブート・ボリュームのバックアップを作成し、バックアップから別の可用性ドメインにブート・ボリュームをリストアすることで、コンピュート・インスタンスを目的の可用性ドメインに移動できます。

コンピュート・インスタンスを別の可用性ドメインに移動させるには、以下の手順を実行します。

1. ブート・ボリュームのバックアップを取得します
    - [Oracle Cloud Infrastructureドキュメント : ブート・ボリュームのバックアップ](https://docs.oracle.com/ja-jp/iaas/Content/Block/Tasks/backingupabootvolume.htm)

2. 作成したブート・ボリューム・バックアップからブート・ボリュームの作成を行い、その際に移動させたい可用性ドメインを選択します。
    - [Oracle Cloud Infrastructureドキュメント : ブート・ボリュームのリストア](https://docs.oracle.com/ja-jp/iaas/Content/Block/Tasks/creatingbootvolume.htm)

3. リストアしたブート・ボリュームからインスタンスの再作成を行います。

<br />

# インスタンスを別のサブネットに移動できますか?

## 質問

既存のコンピュート・インスタンスを、別のサブネットに移動、またはコピーする必要があります。どうすれば良いですか?

## 回答

既存のインスタンスを直接別のサブネットに移動することはできません。
代わりに、対象のコンピュート・インスタンスからブート・ボリュームのバックアップを作成し、バックアップから別のサブネットにブート・ボリュームをリストアすることで、コンピュート・インスタンスを目的のサブネットに移動できます。

コンピュート・インスタンスを別のサブネットに移動させるには、以下の手順を実行します。

1. ブート・ボリュームのバックアップを取得します
    - [Oracle Cloud Infrastructureドキュメント : ブート・ボリュームのバックアップ](https://docs.oracle.com/ja-jp/iaas/Content/Block/Tasks/backingupabootvolume.htm)

2. 作成したブート・ボリューム・バックアップからブート・ボリュームの作成を行い、その際に移動させたいサブネットを選択します。
    - [Oracle Cloud Infrastructureドキュメント : ブート・ボリュームのリストア](https://docs.oracle.com/ja-jp/iaas/Content/Block/Tasks/creatingbootvolume.htm)

3. リストアしたブート・ボリュームからインスタンスの再作成を行います。

<br />

# インスタンスを別のリージョンに移動できますか?

## 質問

コンピュート・インスタンスのコピーを起動して、他のOCIリージョンにサービスを提供し、アプリケーションに冗長性を持たせたいと考えています。コンピュート・インスタンスを別のOCIリージョンにコピーするには、どうすればよいですか?

## 回答

別のOCIリージョンに既存のコンピュート・インスタンスのバックアップをコピーするには、対象のコンピュート・インスタンスからカスタム・イメージの作成を行い、オブジェクト・ストレージを介して、目的の OCI リージョンへエクスポートする必要があります。

コンピュート・インスタンスを別のOCIリージョンに移動させるには、以下の手順を実行します。

1. 移動元のインスタンスが存在するリージョンで、オブジェクト・ストレージ・バケットを作成します。

1. 移動元のコンピュート・インスタンスからカスタム・イメージの作成を行います。
    - [Oracle Cloud Infrastructureドキュメント : カスタム・イメージを作成するには](https://docs.oracle.com/ja-jp/iaas/Content/Compute/Tasks/managingcustomimages.htm#Using2)

1. バケットに、作成したカスタム・イメージをイメージ・ファイルとしてエクスポートします。
    - [Oracle Cloud Infrastructureドキュメント : イメージのエクスポート](https://docs.oracle.com/ja-jp/iaas/Content/Compute/Tasks/imageimportexport.htm)

1. リージョン・セレクタを使って移動先のリージョンに遷移し、エクスポートしたイメージ・ファイルをカスタム・イメージとしてインポートします。
    - [Oracle Cloud Infrastructureドキュメント : イメージの読み込み(https://docs.oracle.com/ja-jp/iaas/Content/Compute/Tasks/imageimportexport.htm#Importing)

1. インポートしたカスタム・イメージから新しいコンピュート・インスタンスを作成します。

<br />

# インスタンスを別のテナンシに移動できますか?

## 質問

既存のコンピュート・インスタンスをOCIの別のテナンシへ移動(コピー)したいと考えています。どうすればよいですか?

## 回答

OCIの別テナンシに 既存のコンピュート・インスタンスをコピーするには、対象のコンピュート・インスタンスからカスタム・イメージの作成を行い、オブジェクト・ストレージを介して、目的のOCIテナンシにインポートする必要があります。  
その際、オブジェクト・ストレージの事前認証済リクエストを使うことで、他のテナンシに対してイメージ・ファイルをセキュアに共有することができます。

コンピュート・インスタンスを別の OCI テナンシーに移動させるには、以下の手順を実行します。

1. 移動元のインスタンスが存在するリージョンで、オブジェクト・ストレージ・バケットを作成します。

1. 移動元のコンピュート・インスタンスからカスタム・イメージの作成を行います。
    - [Oracle Cloud Infrastructureドキュメント : カスタム・イメージを作成するには](https://docs.oracle.com/ja-jp/iaas/Content/Compute/Tasks/managingcustomimages.htm#Using2)

1. バケットに、作成したカスタム・イメージをイメージ・ファイルとしてエクスポートします。
    - [Oracle Cloud Infrastructureドキュメント : イメージのエクスポート](https://docs.oracle.com/ja-jp/iaas/Content/Compute/Tasks/imageimportexport.htm)

1. エクスポートしたイメージ・ファイルに対して、別のテナンシからの読み取りを許可するためのオブジェクトの事前認証済リクエストを作成します。
    - [Oracle Cloud Infrastructureドキュメント : オブジェクトの事前認証済リクエストを作成するには](https://docs.oracle.com/ja-jp/iaas/Content/Object/Tasks/usingpreauthenticatedrequests.htm#usingconsole)

1. 移動先のテナンシで、エクスポートしたイメージ・ファイルをカスタム・イメージとしてインポートします。その際、インポートするイメージとして、作成した事前承認済リクエストのURIを使います。
    - [Oracle Cloud Infrastructureドキュメント : イメージの読み込み](https://docs.oracle.com/ja-jp/iaas/Content/Compute/Tasks/imageimportexport.htm#Importing)

1. インポートしたカスタム・イメージから新しいコンピュート・インスタンスを作成します。
