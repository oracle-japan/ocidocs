---
title: "Autonomous Database 技術FAQ"
excerpt: "Autonomous Database に関して、利用時のよくある質問に対する回答や、ノウハウ/Tipsについて記載しています。"
layout: faq
categories:
  - FAQ
  - services
  - Autonomous
tags:
  - FAQ
tagline: "Autonomous Database に関する技術FAQです<br>サービス仕様に関する⼀般的な質問や、既知の問題については下記の外部リンクもご参照ください" 
header:
  actions:
    - label: "Autonomous Databaseに関するFAQ"
      url: "https://www.oracle.com/jp/database/technologies/datawarehouse-bigdata/adb-faqs.html"
    - label: "既知の問題"
      url: "https://docs.oracle.com/ja-jp/iaas/Content/knownissues.htm"
---

# <span style="color: brown; ">■ サービス概要編</span>
<br/>

## Autonomous DatabaseはOracle Databaseとどう違うのでしょうか？

内部的にはOracle Database 19cを使用しています。(2024/2時点)

それに加えてAutonomous Databaseでは、データベース管理者(DBA)のタスクを減らすため、様々な自動化・自律化機能が備わっていることや、Exadataを基盤としているため高速化されていること、Enterprise Edition(EE)とRACなどの各種EEオプションがデフォルトで利用できることが特徴として挙げられます。

<br/>

## Autonomous DatabaseではExadataのモデルを選択できますか？

Serverless型とDedicated型で異なります。  
Serverless型ではモデルを選択することはできません。ExadataのSmartScanといったハードウェアのスペックに依存しないソフトウェアの機能による高速化がありますので、モデルを気にせずにお使いいただけます。それもメリットの一つです。  
一方、筐体を専有してご利用いただくDedicated型の場合は、利用できるECPU/ストレージの総量にも関係しますので、Exadata X8MまたはX9Mといったようにモデルをご選択いただくことが可能です。

<br/>

## Autonomous Database にはタイプがいくつかありますが、どれを選択すれば良いでしょうか？

ワークロードの特性に応じてご選択いただければと思います。分析目的の利用であれば、Autonomous Data Warehouse(ADW)、JSON中心のアプリケーション開発であればAutonomous JSON Database(AJD)、APEXアプリケーション開発であればAPEX Application Development、トランザクション処理やそれ以外のワークロードであればAutonomous Transaction Processing(ATP)をご選択いただければと思います。

<br/>

## デプロイメント・タイプとしてServerlessとDedicated、Cloud@Customerがありますが、どういった違いがありますか？

Serverlessは運用を極力シンプルにしたい場合や、コストを最優先に考えた場合に選択されることが多く、一方で、より大規模なシステムや、他のお客様との同居が許されないようなシステムではDedicatedを選択いただくことが可能です。

データを外に持ち出すことができないケースや、どうしてもアプリケーションサーバはオンプレミスに配置する必要があってネットワークレイテンシが問題となるようなケースではお客様のデータセンターに配置するCloud@Customerをご選択いただければと思います。

<br/>

## ServerlessとDedicatedに機能差はありますか？

[こちら](https://docs.oracle.com/en/cloud/paas/autonomous-database/dedicated/gjgms/index.html#articletitle)をご参照ください。

<br/>

## Autonomous JSON Database(AJD) は ACID が保証されているのでしょうか？もしくは一般的なNoSQLサービスと言う理解でしょうか？

ACIDが保証されているサービスとなります。
良くMongoDBと比較されますが、Autonomous JSON DatabaseはOracle Databaseにドキュメントを格納するため、RDBMSの特徴をそのまま享受できる点がMongoDBと異なる最大の特徴となります。
詳細については[こちら](https://speakerdeck.com/oracle4engineer/autonomous-json-database-ji-shu-gai-yao?slide=6)に記載がありますので、併せてご確認ください。

<br/>

## (APEX)ADB上のOracle REST Data Serviceで作成したAPIはどうやって認証するでしょうか?

[こちら](https://docs.oracle.com/cd/F25233_01/aelig/developing-REST-applications.html)のドキュメントに認証に関する記載がありますので、ご一読くださいますようお願いいたします。
* 3.3 RESTfulサービスへのセキュアなアクセスの構成
* 3.3.5 チュートリアル: リソースの保護およびアクセス

<br/>

## Autonomous JSON Database(AJD)からAutonomous Transaction Processing(ATP)への変更はできますか？ 

はい、できます。ATPからAJDへの変更は不可、またADWへの変更は不可です。
同様にAPEX Application Development(APEX)からATPへの変更は可能ですが、ATPからAPEXへの変更は不可、またADWへの変更も不可です。

[ページトップに戻る](#)

<br/>

# <span style="color: brown; ">■ 課金/ライセンス</span>
<br/>

## ECPU、ストレージの課金は時間単位でしょうか？

[価格表](https://www.oracle.com/jp/cloud/price-list.html#adw)にはストレージはTB/月(ADW)・GB/月(ATP)、CPUはECPU数/時で記載されていますが、 実際はどちらも秒単位の請求となります。ECPU、ストレージ共に最低1分から秒単位での利用が可能です。  

<br/>

## ADBインスタンスを停止した場合、課金はどうなりますか？

停止した場合、ECPUの課金は止まります。しかし格納されているデータはそのまま削除されずに残るので、ストレージへの課金は発生します。

なお、インスタンスを終了(Terminate)した場合、ECPU、ストレージの課金も止まります。

<br/>

## Auto Scaling 有効時の課金について教えてください

分単位でCPU使用率を算出し、それを時間で平均したものが課金されます。少数点以下は切り上げとなります。例えば、ベースが4 ECPUでAuto ScalingをONにすると最大12 ECPUとなり、1時間あたりの平均消費ECPUが60%であれば8ECPU分が課金されます。

<br/>

## Auto Scaling を有効にした場合のCPU単価は割高になりますか？

いいえ、同一価格となります。

<br/>

## SE BYOLで既存ライセンスを持ち込む場合のルールはありますか？

1Processorあたり16ECPU、10NUPあたり4ECPUになります。また、SE BYOLでは1ADBインスタンスあたり32ECPUの上限があります。

Autonomous Data Guardを利用する場合でも、Active Data Guardオプションは不要です。

<br/>

## EE BYOLで既存ライセンスを持ち込む場合のルールはありますか？

1Processorあたり8ECPU、25NUPあたり8ECPUになります。また、65ECPU以上EE BYOLで使いたい場合、65ECPU以降8ECPUにつき1Processorまたは25NUPのRACオプションが必要になります。

Autonomous Data Guardを利用する場合、8ECPUにつき1Processorまたは25NUPのActive Data Guardオプションが必要です。

<br/>

## SE BYOLで既存ライセンスを持ち込む場合、1インスタンスあたり32ECPUの上限があります。Auto Scalingを有効にした場合、OCPUはいくつが上限になりますか？

ライセンスタイプは「ライセンス持込み (BYOL)」エディションを「Oracle Database Standard Edition(SE)」を選択してください。その場合、設定できるECPU数は32まで、またAuto Scalingが利用された場合もBYOLの1インスタンスの最大である32 ECPUで頭打ちとなります。
32ECPU以上の環境にしたい場合は、SE BYOLは利用できません。ライセンス・タイプをBYOLから「ライセンス込み」に変更してご利用ください。
こちらの操作もオンラインで実施できます。ただし、この場合は全ECPUがライセンス込みの価格になりますので、ご注意ください。

<br/>

## Oracle Analytics Desktop （旧Data Visualization Desktop : DVD）は無償でしょうか？

Autonomous Databaseをデータソースとしてご利用いただく限り無償です。[PaaS and IaaS Universal Credits Service Descriptions](https://www.oracle.com/a/ocom/docs/paas-iaas-universal-credits-3940775.pdf)に記載があります。
なお、Oracle Analytics Desktopは検証用の扱いのため、本番環境で利用する際にはOracle Analytics Cloudの利用をご検討ください。

<br/>

## Autonomous Data Guardを利用する場合の課金はどうなりますか？

プライマリと同じリージョンにスタンバイを構成するローカルAutonomous Data Guardの場合、スタンバイ側のECPUとストレージ費用は、プライマリと同じ分発生します。そのため全体としては、プライマリの2倍課金されます。

プライマリと異なるリージョンにスタンバイを構成するクロスリージョンAutonomous Data Guardの場合、スタンバイ側のECPUはプライマリのECPUと同様になりますが、ストレージ費用はプライマリの2倍発生します。これはプライマリの変更をスタンバイに適用するために転送したログをスタンバイ側で保管するストレージが必要となるためです。アウトバウンド通信の通信料はこちらに含まれる形になります。
そのため全体としては、ECPUがプライマリの2倍、ストレージがプライマリの3倍課金されます。

なお、同様の理由により、クロスリージョンでのリフレッシュ可能クローン構成の場合も、リフレッシュ可能クローン側のストレージはクローン元となるADBの2倍の課金となります。

<br/>

[ページトップに戻る](#)

<br/>

# <span style="color: brown; ">■ 構成関連</span>
<br/>

## インフラ構成やI/O帯域、搭載メモリなどのスペックは公開されていますか？

詳細なアーキテクチャ、スペックは公開しておりません。それらの管理・運用の手間からユーザを開放することがAutonomous Databaseのコンセプトです。  
I/OスループットはCPUの数に合わせてスケーリングします（Disk、FlashのI/O帯域がcpu_countの値に比例するよう制御されています）。

またメモリもECPUに比例する形で割り当てられます。自動スケーリングを有効にすると、I/O帯域はベースの3倍確保されますが、メモリはベースと変わりません。

<br/>

## I/O性能の指標はありますか？

具体的な指標はありません。Autonomous DatabaseはExadataで構成されていますが、ExadataはI/Oを効率化する各種機能があり、単純なI/O能力だけでは性能を判断できないからです。実際にご利用いただいているお客様の評価を[事例サイト](https://www.oracle.com/jp/cloud/infrastructure/case-studies/)でご確認ください。

<br/>

## ストレージの最大容量はどのように制御されているのでしょうか、また指定できる最大容量はいくつでしょうか？

指定した容量に基づいてデータベースのプロパティで設定がされます。設定は以下で確認できます。  

```sql
SQL> select PROPERTY_VALUE FROM database_properties WHERE property_name = 'MAX_PDB_STORAGE';
SQL> select max_size from v$pdbs;  
```
指定できる最大容量は384TBです。それ以上のサイズが必要な場合は別途お問い合わせください。

<br/>

## ストレージ容量として指定したサイズはデータベース全体のサイズでしょうか、それとも格納するデータ量に対してでしょうか？

データベース全体のサイズです。ユーザーデータを格納する表領域以外のデータベースを稼働させるために必要な表領域も含まれます。
含まれる表領域については[Autonomous Database Cloud 技術詳細](https://speakerdeck.com/oracle4engineer/autonomous-database-cloud-ji-shu-xiang-xi)の"ストレージ領域の管理"の章をご確認ください。

<br/>

## ストレージ容量以上のデータを格納しようとした場合はどうなりますか？ 

ストレージ容量が不足した場合は、Insert処理がエラーとなります（ORA-65114: コンテナ内の領域の使用量が高すぎます）
ただし、Database Actionsのデータベース・ダッシュボードの使用率表示は余裕を持たせてあり、100%ではなく、110%でエラーが発生するようになります。 この場合、ストレージ容量を手動でスケールアップする、もしくはストレージの自動拡張機能を有効にすることでエラーを解消できます。ストレージの自動拡張を有効にすると、設定値の3倍まで予約され、割り当て可能になります。課金は割り当て済みのストレージをTB単位に切り上げた値にかかります。これにより、想定外のデータ量の増加に対応できます。

<br/>

## ECPUのAuto Scalingと手動スケーリングは同時に利用できますか？

同時に利用することができます。Auto Scalingを有効にした状態で、ECPU数を手動で調整することが可能です。

<br/>

## 一時表領域のサイズは決められますか？

ストレージサイズの30%が最大サイズで自動拡張され、ユーザーは変更不可です。

<br/>

[ページトップに戻る](#)

<br/>

# <span style="color: brown; ">■ 可用性関連</span>
<br/>

## ディザスタリカバリ（DR/Disaster Recovery）は可能でしょうか？

可能です。Autonomous Data Guardをリージョン内（ローカル）、クロス・リージョンで構成することができます。
また、バックアップベースのディザスタ・リカバリというオプションもございます。
リモート・リージョンにバックアップのコピーを保持しておくことが可能です。

<br/>

## 障害が発生した場合の復旧時間について教えてください。

ハードウェア障害やデータ破損発生時の障害復旧時間目標は[こちら](https://docs.oracle.com/en/cloud/paas/autonomous-database/adbsa/availability-slo.html#GUID-D7F065C4-68C5-4F1F-B8D4-4A43538E7384)をご確認ください。

Autonomous Data Guard を構成いただいた場合は、SLA99.995%となり、データ破損などの障害からの復旧時間が短くなります。RTO/RPOについては[マニュアル](https://docs.oracle.com/ja-jp/iaas/adbdataguard/autonomous-data-guard-about.html#GUID-AF736587-4F6D-45C3-9886-7211B0A33745)をご確認ください。

<br/>

## 東日本-西日本などリージョン間を跨いだ冗長化を検討する場合、もっともRTO/RPOを短くすることができる構成はどのようになりますでしょうか？ 

Autnomous Data Guardを利用した構成をご検討いただけます。RTO/RPOについては[マニュアル](https://docs.oracle.com/ja-jp/iaas/adbdataguard/autonomous-data-guard-about.html#GUID-AF736587-4F6D-45C3-9886-7211B0A33745)に記載があります。他の方法として、GoldenGateを利用した論理レプリケーションがあります。それぞれについてチュートリアルを公開しておりますのでご利用ください。

* [Autonomous Data Guardチュートリアル](https://oracle-japan.github.io/ocitutorials/adb/adb212-audg/)
* [OCI GoldenGateチュートリアル](https://oracle-japan.github.io/ocitutorials/adb/adb401-oci-goldengate/)

<br/>

## 自動バックアップはどこに取得されるのでしょうか。任意のオブジェクト・ストレージに取得することはできますか？ 

オラクル社が管理するオブジェクト・ストレージに取得されます。取得先に任意のオブジェクト・ストレージを指定することはできません。自動バックアップが取得されるオブジェクト・ストレージはユーザがアクセスすることはできず、課金対象になります。(ECPUモデル)

<br/>

## 自動バックアップからのリストア時間はどれくらいでしょうか？

データ量、更新量に依存するため変動する可能性はありますが、サービスレベル目標については[こちら](https://docs.oracle.com/en-us/iaas/autonomous-database-shared/doc/availability-slo.html)を参照ください。

<br/>

## バックアップを任意の場所にリストアすることや、BaseDBなど別のサービスに展開することは可能ですか？

できません。Autonomous Database のバックアップは、同じデータベースにリストアしてデータを復元する目的にのみ使用できます。
バックアップからではなく、Data Pump でオブジェクト・ストレージにエクスポートし、別のサービスへインポートを行うことや、クローン機能でクローンやリフレッシュ可能なクローンを別のコンパートメントに作成することをご検討ください。

<br/>

## Autonomous Database Serverless型はソースDBを止めずにクローンできると思いますが、どの断面で作成されるのですか？作成をクリックしたタイミングですか？ 

クローン実行中のどこかのタイミングが断面となります。クリック直後といった明確な基準は定義されておりません。  
明確に断面を確保したい場合はアプリケーション側で読み取り専用（Readonly)として更新が入らないように制御する、またはインスタンスを停止してのクローン、バックアップを利用して時間指定でのクローンをご利用ください。  
なお、バックアップから作成する場合はオブジェクトストレージからのインスタンス作成になるため、クローン作成は比較的時間がかかることにご注意ください（データ量、圧縮率に依存）

[参考：クローンに関するチュートリアル](https://oracle-japan.github.io/ocitutorials/adb/adb211-clone/)

<br/>

## Data Pump を利用した論理バックアップはできますか？ 

はい、できます。スキーマ単位、表単位でバックアップを取っておきたい場合に利用します。
詳しい手順はこちらのマニュアルをご参照ください。

[参Autonomous DatabaseでのOracle Data Pumpを使用したデータのインポート考：](https://docs.oracle.com/cd//E83857_01/paas/autonomous-database/serverless/adbsb/load-data-data-pump.html#GUID-30DB1EEA-DB45-49EA-9E97-DF49A9968E24)

<br/>

## 60日以上バックアップを保存する方法はありますか？

長期バックアップを利用すれば、最低3カ月最長10年間バックアップを保存可能です。

ただし、長期バックアップはリストア不可、そのバックアップをソースとしたクローン作成のみ可能となっている点、保存先はExadataストレージになるのでExadataストレージ分の課金が発生する点にご注意ください。(2024/2時点）詳しくは[こちら](https://docs.oracle.com/en/cloud/paas/autonomous-database/adbsa/backup-long-term.html#GUID-BD76E02E-AEB0-4450-A6AB-5C9EB1F4EAD0)をご参照ください。

もしくは先述のData Pumpを利用した論理バックアップを取っておくことで、60日以上のバックアップを保存することが可能です。

<br/>

## ソースADBがあるリージョンと異なるリージョンのバックアップ（リモート・バックアップ）を取ることは可能ですか？

ADBの自動バックアップ機能では遠隔地(別リージョン)へバックアップすることはできません。ADBにおけるDR対応は下記の方法がございます。
  ・Autonomous Data Guardを使用し、スタンバイDBを構成する
  ・バックアップベースのディザスタ・リカバリのクロスリージョン・バックアップ・コピーを構成する
  ・別リージョンにクローンを作成する（平時はインスタンスを停止することでECPUの課金は抑えることが可能です）
  ・Data Pumpで Object Storageに論理バックアップを取得し、Object Storageのリージョン・コピー機能で別リージョンに複製する

<br/>

## flashback databaseは使えますか？

ADBではflashback databaseはユーザーは明示的に使用できません。ユーザー指定のタイムスタンプにリストアすることが可能であり、内部的に使用されています。

なお、flashback tableやflashback queryは可能です。[ドキュメント](https://docs.oracle.com/cd//E83857_01/paas/autonomous-database/adbsa/autonomous-oracle-flashback.html#GUID-CEE36DC8-E878-481C-8366-9507D0911C25)

また、データベース・オブジェクトの過去の状態を表示できるFlashback Time Travelも使用できます。[ドキュメント](https://docs.oracle.com/cd/E83857_01/paas/autonomous-database/serverless/adbsb/flashback-time-travel-autononomous.html#GUID-A98E1F8B-FAE4-4FFF-955D-3A0E5F8EBC4A)

<br/>

## セッションの瞬断対策として何か講じることはできますか？ 

Application Continuityを有効化することで、セッション瞬断の影響を受ける処理を自動回復させることができます。接続サービスごとに有効化するだけでアプリからは透過的に利用することができます。詳しい手順はこちらのチュートリアルをご参照ください。

[参考：Application Continuityを設定しよう](https://oracle-japan.github.io/ocitutorials/adb/adb213-tac/)

<br/>

[ページトップに戻る](#)

<br/>

# <span style="color: brown; ">■ 接続・データロード/連携関連</span>
<br/>

## Autonomous Databaseに接続がサポートされている製品(サードパーティ製品)を教えてください。

サポートについては接続を行う製品ベンダー側にご確認いただくことになります。
[オラクル社で動作確認したツールのリスト](https://www.oracle.com/jp/autonomous-database/tools/)
もありますが、リストに記載がなくてもOracle Clientの要件を満たしていれば基本的に動作可能とお考え下さい。  

<br/>

## Autonomous Databaseに割り当てられたIPアドレスは固定でしょうか。

Private IPは固定ですが、Public IPは変更される可能性があります。Public IPの場合はホスト名をご利用ください。

<br/>

## Autonomous Databaseの同時接続セッション数はいくつでしょうか。また同時接続セッション数を超えて接続しようとした場合はエラーになりますか？

同時接続セッション数は1ECPUあたり75セッションです。  
Autonomous Databaseの初期化パラメータSESSIONSとして設定されます。この値は接続サービスをまたいで有効であり、例えば、ECPU=4のAutonomous Transaction Processingにおいて、TPURGENTで100、TPで200セッションが接続している場合、新たにセッションを作成することはできません。セッション数を超えた場合は 「ORA-00018: 最大セッション数を超えました」というエラーとなり、セッションの作成に失敗します。  
なお、Autonomous DatabaseはOracle MultitenantのPluggable Databaseであるため、バックグランドプロセスや再帰セッション分のセッション数を考慮する必要はありません。

<br/>

## Autonomous Databaseで動作しているトランザクション系システムのデータを分析系システムへ渡したい場合、なるべく低負荷でデータを伝搬させる手段はありますでしょうか？ 

OCI GoldenGateの利用をご検討ください。またDatabase Linkをご利用いただくことも可能です。  

[参考：OCI GoldenGateチュートリアル](https://oracle-japan.github.io/ocitutorials/database/adb401-oci-goldengate/)

<br/>

## IoTデータ等の大規模ファイルを扱う場合、外部表を利用してオブジェクト・ストレージに配置して都度アクセスするか、一旦Autonomous Database内にロードしてから利用するのとではどちらが良いでしょうか？ 

ケース・バイ・ケースですが、参照頻度によって決めることをおすすめします。  
SQLでの参照頻度が低いようであれば、オブジェクト・ストレージの方がAutonomous DatabaseのExadata Storageよりも安価ですので、外部表を利用してアクセスするほうがコストを抑えることができます。
また頻繁にアクセスするようでしたらAutonomous Database内にロードした方が、SmartScan機能による高速化を有効に利用できます。  
なお、外部表を利用して参照する場合も、Autonomous Databaseにロードする場合も、オブジェクト・ストレージ上のファイルは分割しておくことをおすすめします。ファイルを分割することで複数のコアを利用した高速な並列処理が可能になります。

[参考：Autonomous Databaseを使用した外部データの問合せ](https://docs.oracle.com/cd/E83857_01/paas/autonomous-database/adbsa/query-external.html#GUID-ABF95242-3E04-42FF-9361-52707D14E833)

<br/>

## Autonomous Database とOracle Database 以外のデータベースの連携は可能ですか？ 

Oracle Database Gatewayを経由することでDB Linkで連携可能です。Redshift、MySQL EE、PostgreSQL、SnowflakeなどいくつかのデータベースへのDB Linkでは、Oracle Database Gatewayを用意しなくとも利用可能です。

参照するリモートDBにはいくつか要件がございます。詳しくはこちらのマニュアルをご参照ください。

[参考：非Oracle Databaseへのデータベースリンクの作成](https://docs.oracle.com/cd/E83857_01/paas/autonomous-database/serverless/adbsb/autonomous-database-links-non-oracle-db.html#GUID-6C925566-2067-4D3B-A3FF-CA73C60506C9)

<br/>

[ページトップに戻る](#)

<br />

# <span style="color: brown; ">■ セキュリティ関連</span>
<br/>

## ウイルスソフトは導入できますか？
　
OSレイヤーは解放していないため、ウィルスソフトのインストールはできません。オラクル社にて対策を十分に講じておりますので、お客様側で対応いただく必要はございません。

<br/>

## ハッキングや、ウィルス対策はどの様になっていますでしょうか？
　
オラクル社にて対策を十分に講じております。セキュリティ・ポリシーに関しては[セキュリティガイド](https://docs.oracle.com/cd/E97706_01/Content/Security/Concepts/security_guide.htm)を参照ください。

<br/>

## 暗号化は必須ですか？ 

はい、必須です。  
Autonomous Databaseではデータファイル（表領域）やバックアップファイル、およびネットワークなど、全てのレイヤーにおいてデフォルトで暗号されております。これらの暗号化にはAdvanced Security Option(ASO）を利用しております。ASOはオンプレミスでは有償オプションですが、Autonomous Databaseを含むOCIのPaaSサービスではサブスクリプションの範囲として追加費用なくご利用いただけます。

<br/>

## Autonomous Databaseの表領域暗号化のアルゴリズムは何を利用していますか? 

現時点でAES-256で暗号化されております。
V$ENCRYPTED_TABLESPACESビューを参照いただくことで確認可能です。

```sql
SELECT NAME,ENCRYPTIONALG FROM V$ENCRYPTED_TABLESPACES a,V$TABLESPACE b
WHERE a.TS# = b.TS#
```

<br/>

## Autonomous Databaseの通信はTLS1.2ですか？

はい、TLS1.2です。[Oracle Autonomous Databaseの職責モデル](https://docs.oracle.com/cd/E83857_01/paas/autonomous-database/shared-responsibility-model.html)のアプリケーション・セキュリティの欄に記載があります。

<br/>

## ADBでデータベース監査の結果はすべてコマンドで取得できますか？ 

はい、Oracle Databaseの統合監査機能ですので、UNIFIED_AUDIT_TRAILビューでご確認いただくことが可能です。なお、GUIで参照するには、Data Safeをご利用ください。  

[参考：Oracle Data Safeチュートリアル](https://oracle-japan.github.io/ocitutorials/id-security/data-safe-tutorials/)

<br/>

## ADBの停止、終了などのインスタンス操作の監査ログを取ることはできますか？

OCI Audit機能で可能です。詳しくは[こちら](https://docs.oracle.com/ja-jp/iaas/Content/Audit/home.htm)をご参照ください。

<br/>

## Autonomous Databaseにおいてマスキングする場合、プロシージャ等で実行できるのでしょうか？ 

現時点で、Autonomous DatabaseでOracle Data Masking and Subsetting Packは利用できません。マスキングの機能を利用するには、Data Safeをご利用ください。 

[参考：Oracle Data Safeチュートリアル](https://oracle-japan.github.io/ocitutorials/id-security/data-safe-tutorials/)

<br/>

## 行や列単位でアクセス制御をかけることはできますか？ 

はい、仮想プライベートデータベース(VPD)というソリューションで可能です。詳しくはこちらのチュートリアルをご参照ください。

[参考：Virtual Private Database(VPD)チュートリアル](https://oracle-japan.github.io/ocitutorials/adb/adb210-VPD/)

また、特権ユーザーに対しても情報にアクセスさせないようにするDatabase Vaultも設定可能です。詳しくはこちらのチュートリアルをご参照ください。

[参考：Database Vaultチュートリアル](https://oracle-japan.github.io/ocitutorials/adb/adb209-DV/)

<br/>

## ADMINユーザーのパスワードを期限切れにならないように変更できますか？

ADMINユーザーのプロファイルは、セキュリティ観点からデフォルトの360日から変更できないようになっております。代替案として、必要な権限を持つユーザーを追加で作成し、このユーザーをパスワードの有効期限を360日以上に設定したプロファイルに割り当てることを検討してください。

[ページトップに戻る](#)

<br/>

# <span style="color: brown; ">■ オプション・機能関連</span>
<br/>

## Autonomous Database でSQL計画管理(SPM：SQL Plan Management)は利用できますか？ 

はい、ご利用いただけます。19cから自動タスクの中でパフォーマンスが優れた計画はベースラインに追加されるようになっているため、SPMは自動的に利用されています。また、状況に応じてベースラインの実行計画から、利用する実行計画を選択することも可能です。

* SPMは自動で動いており、ベースラインも自動で取得している
* 手動取得も可能(Session単位でのみ可能。alter system によるOPTIMIZER_CAPTURE_SQL_PLAN_BASELINESの有効化(TRUE)は不可)。

参考：  
[ドキュメント SQL計画管理の概要](https://docs.oracle.com/cd/F19136_01/tgsql/overview-of-sql-plan-management.html#GUID-F1C45056-F998-43E5-B362-83F88DA49E58)  
[ドキュメント Oracle Database SQLチューニング・ガイドのこのリリースでの変更](https://docs.oracle.com/cd/F19136_01/tgsql/release-changes.html#GUID-7F884CC2-24AD-4CB9-B387-84C43BD2B5D9)  
[ホワイトペーパー Oracle Database 19cのSQL計画管理](https://www.oracle.com/technetwork/jp/database/bi-datawarehousing/twp-sql-plan-mgmt-19c-5324207-ja.pdf)

<br/>

## Autonomous Database でSQLプロファイルは利用できますか？ 

SQLプロファイルとは、SQLチューニングアドバイザを利用して生成される補足統計です。
Autonomous Databaseでは自動SQLチューニング（自動タスクによるSQLチューニングアドバイザの実行）は行われておりませんが、オンデマンドでのSQLチューニングアドバイザの実行は可能です。   

参考：  
[ドキュメント：SQLプロファイルの統計](https://docs.oracle.com/cd/F19136_01/tgsql/managing-sql-profiles.html#GUID-0DF775E3-8E88-437F-8A30-1173497EB971)  
[ドキュメント：SQLチューニング・タスクの作成](https://docs.oracle.com/cd/F19136_01/tgsql/sql-tuning-advisor.html#GUID-36E17A9F-7912-483D-A10F-7748BBB924E0)

<br/>

## Autonomous Database でSQLパッチは利用できますか？ 

はい、明示的に与えたいヒントをSQLパッチとして作成し、SQLに適用することができます。  

参考：  
[津島博士のパフォーマンス講座 第72回](https://blogs.oracle.com/otnjp/tsushima-hakushi-72)  
[ドキュメント：DBMS_SQLDIAG](https://docs.oracle.com/cd/F19136_01/arpls/DBMS_SQLDIAG.html)

<br/>

## Autonomous Database でReal Application Testing(RAT)は利用できますか？ 

はい、Real Application Testing(RAT)のSQL Performance Analyzer(SPA)、DB Replayを利用できます。  
Autonomous Databaseへの移行を検討される際やAutonomous Databaseのバージョンアップの際に、個々のSQLの性能調査に有効です。手順は以下のチュートリアルを参照ください。

[参考：SQL Performance Analyzer(SPA)チュートリアル](https://oracle-japan.github.io/ocitutorials/adb/adb216-patch-spa/)

<br/>

## Autonomous Database でResult Cacheは利用できますか？

はい、利用できます。  
ワークロードの違いに合わせてデフォルト設定がされています。Autonomous Data Warehouse(ADW)は有効、Autonmous Transaction Processing(ATP)は無効です。

<br />

## Autonomous Database でHybrid Columnar Compression（HCC/ハイブリッド・カラム圧縮）は利用できますか？

Autonomous Databaseはワークロードにあわせて表領域単位で圧縮レベルがデフォルトで設定されています。

*  Autonomous Transaction Processing(ATP)は非圧縮
*  Autonomous Data Warehouse(ADW)は、HCC（QUERY HIGH ROW LEVEL LOCKINGモード）

表単位で非圧縮やBASIC、OLTP、Query Lowといった圧縮モードへの変更は可能です。
ADWで更新が発生するような表の場合は、明示的に非圧縮に設定してください。

>QUERY HIGH ROW LEVEL LOCKINGモードとは  
>HCCは1CU につき1ロックを使用しますが、Oracle Database 12cから行レベル・ロックの有効化が可能となりました。
>OnP Exadata におけるHCCのデフォルト設定は、NO ROW LEVEL LOCKINGです。

<br/>

## Autonomous Database でDatabase In-Memory(DBIM）を利用できますか？

Dedicatedでは可能です。
ただし、Autonomous Databaseは内部的にExadataのフラッシュ・キャッシュでDatabase In-Memory機能を利用して性能を最適化します。

<br/>

## 一部の重たいクエリによるリソースの大量消費を防ぐ仕組みはありますか？ 

Runaway Queryの管理で、SQLの実行時間やIO量の上限を設定することができます。セッションは接続されたまま、上限を超えるクエリは強制キャンセルされます。

[参考：Runaway Query マニュアル](https://docs.oracle.com/en/cloud/paas/autonomous-database/adbsa/manage-sql-statements.html#GUID-4861BA7F-F9FA-4909-8DC0-4F46AFF80706)

<br/>

[ページトップに戻る](#)

<br/>

# <span style="color: brown; ">■ 運用管理・監視関連</span>
<br />

## データファイルのサイズ変更（リサイズ）や最大サイズの変更を行うことはできますか？
はい、できます。DATA表領域のデータファイルに対してリサイズと最大サイズの変更が許可されています。

```sql
--リサイズ
 ALTER DATABASE DATAFILE <FILE_ID> RESIZE <リサイズ後のサイズ>;
-- 自動拡張の最大サイズの変更
 ALTER DATABASE DATAFILE <FILE_ID> AUTOEXTEND ON MAX SIZE <Maxサイズ> ;
```
なお、表領域の作成、削除、表領域の設定値の変更および、デフォルト表領域の変更は許可されていません。
デフォルトで自動拡張が有効化されており、ユーザが指定したストレージ容量の範囲内で自動的に拡張されます。  

<br />

## 権限やリソースの制御はどのようにおこなっているのでしょうか、変更可能でしょうか？

データベースレベルでの権限やリソースの制御はPDBロックダウン・プロファイル機能を利用しています。
Autonomous Database では、PDBロックダウン・プロファイルの設定は変更できません。
設定内容の確認は以下のSQLで可能です。

```sql
SELECT * FROM V$LOCKDOWN_RULES WHERE STATUS LIKE 'DISABLE%' ORDER BY RULE_TYPE ;
```

ユーザーが許されているリソース変更は、接続サービスのリソース割り当て（SHARES）や長時間かかるSQLの自動切断設定などがあります。詳細は[Autonomous Database Cloud 技術詳細](https://speakerdeck.com/oracle4engineer/autonomous-database-cloud-ji-shu-xiang-xi)を参照ください。

[参考ドキュメント PDBロックダウン・プロファイルを使用したPDBでの操作の制限](https://docs.oracle.com/cd/F19136_01/dbseg/configuring-privilege-and-role-authorization.html#GUID-AB5E62DB-7E2A-4B5A-BA96-A2BD2DF15275)

<br />

## AWRは取得できますか？AWRの保持期限、スナップショット間隔は変更で可能でしょうか？

可能です。パフォーマンスハブからのダウンロードまたはSQLで取得できます。取得方法は[OCIチュートリアル](https://oracle-japan.github.io/ocitutorials/database/adb203-bulk-query/#anchor3-2)に記載していますのでご確認ください。
AWRの取得設定は通常のOracle Databaseと同様にDBMS_WORKLOAD_REPOSITORY.MODIFY_SNAPSHOT_SETTINGSにて変更可能ですが、再起動すると保持期間はデフォルトの30日に戻ります。

<br />

## オプティマイザ関連の設定は変更できますか。  

基本的に不可です。全てオプティマイザに任せるのがコンセプトです。Autonomous Databaseの初期化パラメータなどの制限事項ついては[ドキュメント](https://docs.oracle.com/cd/E83857_01/paas/autonomous-database/adbsa/experienced-database-users.html#GUID-58EE6599-6DB4-4F8E-816D-0422377857E5)をご確認ください。

<br />

## 統計情報を手動で取ることはできますか？

はい、可能です。DBMS_STATS.GATHER_TABLE_STATS等で取得できます。

<br/>

## 統計情報の固定・リストアは出来ますか？  

統計情報のリストア（DBMS_STATS.RESTORE_TABLE_STATS）、固定化（dbms_stats.lock_table_stats）は可能です。
ただし、Autonomous Database では自動統計収集が有効化されていますので、基本的に全てAutonomous Databaseに任せることが可能です。

<br />

## パフォーマンスハブで確認すると、Consumer Groupに接続サービスのグループ（HIGH/MIDDLE/LOW)のほかにOTHER_GROUPSとInternalがあります。これは何でしょうか。 

Cloud Ops、もしくはスケジューラプロセスが利用するグループです。お客様は利用できません。

<br />

## アラートログ、トレースファイルの確認はできますか。  

SQLを利用してアラートログ(V$DIAG_ALERT_EXT)やトレースファイル(V$DIAG_TRACE_FILE_CONTENTS)を確認することは可能です。[OCIチュートリアル](https://oracle-japan.github.io/ocitutorials/database/adb502-report/)に記載しておりますのでご確認ください。

<br />

## Autonomous Database で監視と通知の設定を行うことは可能でしょうか？ 

はい、可能です。
以下を必要に応じて組み合わせてご利用ください。詳細については[Autonomous Database Cloud 技術詳細](https://speakerdeck.com/oracle4engineer/autonomous-database-cloud-ji-shu-xiang-xi)を参照ください。 
+ OCIにおけるテナント管理者への各種通知（オラクル社が実施する各種メンテナンス等に関して）
+ ADBインスタンス単位で指定する連絡先リストへの通知（オラクル社が実施する各種メンテナンス等に関して）
+ OCI Monitoringの監視メトリックやOCI Eventsにてサポートされるイベントについて通知（OCI Notificationsと連携）
+ Oracle Management CloudやEnterprise Managerを用いた監視・通知

<br />

## SYSDATEのタイムゾーンがUTCですが、JSTに変更は可能でしょうか？

はい、SYSDATE_AT_DBTIMEZONEを使用することで可能です。
以下の記事に手順が記載されていますのでご確認ください。
[[OCI]Autonomous Database：SYSDATE_AT_DBTIMEZONEを使用してsysdateが日本時間を返すようにしてみた](https://qiita.com/500InternalServerError/items/d741a7144de7b35e04ed)

<br />

[ページトップに戻る](#)

<br />

# <span style="color: brown; ">■ パッチ・メンテナンス関連</span>
<br />

## Autonomous Database Serverless型は適宜自動でパッチが適用されていくと思いますが、パッチに伴う性能影響が心配です。 

パッチ適用に伴うSQLの実行計画の変動を気にされているかと思いますが、Autonomous Databaseに定期的に自動適用されるパッチについては、オプティマイザの更新はデフォルトOFFのままとなっております。したがって、パッチ適用に伴う実行計画の変動は基本的に発生しません。また強化されたSQL実行計画管理機能（SPM）により実行計画の変化に伴う性能影響が出ないよう自動管理されております。

<br />

## Autonomous Databaseのパッチ適用作業時にインスタンスの起動は必要でしょうか？ 

いいえ、必要ありません。メンテナンス実施時にインスタンスが停止していてもパッチは適用されます。インスタンス停止中に適用できないパッチ(DataPatch等)については、インスタンスが起動した際に適用されるようになっています。

<br />

## Autonomous Databaseのパッチ適用には、OSなどのインフラのパッチも含まれますか？

はい、含まれます。Databaseのパッチだけでなく、Exadata Infrastructureや、セキュリティパッチも含まれます。

<br/>

## 過去のメンテナンスの情報は確認可能でしょうか？

各Autonomous Databaseのメンテナンスについては、コンソールのメンテナンス履歴で確認いただけます。
また、適用されたパッチについては、DBA_CLOUD_PATCH_INFOビューで詳細を取得できます。

```sql
SELECT * FROM DBA_CLOUD_PATCH_INFO;
```

サービスレベルでの過去の緊急メンテナンス等の実績はOCIコンソールのお知らせ(Announcements)をご覧ください。

<br />

## ハードウェア更改時の対応はオラクルが行うのでしょうか？

はい。オラクル社の責任範囲ですので、オラクル社でH/W更改を含むメンテナンスを実施します。  
メンテナンスに伴いインスタンスの稼働に影響が及ぶ場合は、事前にOCIのテナント管理者、およびAutonomous Databaseインスタンスの連絡先リストに記載のメールアドレス宛に通知されます。

<br />

[ページトップに戻る](#)

<br />

# <span style="color: brown; ">■ 性能検証編(環境作成)</span>
<br />

ここではAutonomous Database を利用して検証作業を実施するためのノウハウを中心に記載しています。  
なお、“簡単”というAutonomous のコンセプトから乖離するような細かい内容も含まれていますが、基本的に簡単すぐに良い性能が得られています。

<br />

## ECPUはいくつからスタートすれば良いですか？ 

Autonomous Databaseにおいて性能試験をされる場合、最低でもECPU=4からスタートしてください。より高い性能を得るためのパラレル処理が利用可能になります。

<br />

## データロードについて、DBMS_CLOUD.COPY_DATAによるデータロードを高速化するには？ 

OCIチュートリアルの[コマンドラインから大量データをロードしてみよう](https://oracle-japan.github.io/ocitutorials/adb/adb202-dataload-dbms-cloud/)の「よくある質問」に記載していますのでご参照ください

<br />

## 統計情報はいつ取得すれば良いですか？ 

自動的に取得されているため、考慮不要です。

* Autonomous Database ではデータをロード（ダイレクト・パス・ロード）する際に、自動で統計情報が取得されます
* その後、実際のワークロードに基づいて統計情報が定期的に自動取得されます
* さらに、クエリ実行時に統計情報が欠損している際は、ダイナミック・サンプリングが動きます

それでも期待した性能が出ない場合は、以下のようにコマンドで取得することも可能です。

```sql
exec dbms_stats.gather_table_stats( ownname => 'YYY', tabname => 'XXX') ;
```

<br />

[ページトップに戻る](#)

<br />

# <span style="color: brown; ">■ 性能検証編(クエリ実行)</span>
<br />

## IOがボトルネックになっている処理の性能向上を図るには？ 

Autonomous Database はECPU数を増やすことでコア数だけでなくIO帯域も増えるため、IOバウンドな処理に対してもECPUを増やすことで高速化できます。  
ECPU増はコストに跳ね返ってきますが、ECPUはオンラインでスケールアップ・ダウンが可能なので、週末・夜間など使わないタイミングはコア数を減らすことでコストを削減できます。  

その他、Autonomous Databaseでは高速化のためのオプション機能であるパーティションが追加費用なく利用できます。またマテリアライズド・ビュー機能やAutonomous Transaction Processing(ATP)の場合はHCCによる圧縮も利用できるため（Autonomous Data Warehouse(ADW)はデフォルトで圧縮され格納される）、これらの機能を活用することもご検討ください。

<br />

## ヒントは利用できますか？ 

* Autonomous Transaction Processing(ATP)の場合
    * 通常Oracle Databaseと同様、特に気にせずにヒント句を利用できます。
* Autonomous Data Warehouse(ADW) の場合
    * 基本的にオプティマイザに全て任せることで高速化できるため、ヒントは不要です。
        * optimizer_ignore_hints がTRUEであり既存ヒントは無効化されている
        * ヒントを利用するためにはセッション単位でヒントを有効化する必要があります。（NO_RESULT_CACHEヒントを除く。後述）

        ```sql
        alter session set optimizer_ignore_hints = false ;
        select /*+ xxxxx */ count(1) from test_table ;
        ```


おすすめの検証手順としては、まずはそのままの状態で計測してください。その後、予期した性能が出ない場合に限り、ヒントの効果を確認してください。

<br />

## Result Cache（結果キャッシュ）を無効化することはできますか？ 

Autonomous Data Warehouse (ADW) はDWHの高速化機能の一つであるResult Cacheがデフォルトで有効化されており、あくまでもそのままお使いいただくことを推奨しています。（Autonomous Transaction Processing(ATP)はデフォルト無効です）

とは言え、性能試験にてSQLの処理時間を計測したい場合、Result Cacheが有効だとどんなに時間がかかるSQLでも2回目の計測以降は1秒未満の結果となるため、Result Cacheを無効化したいケースがあります。  
Result Cacheを無効化したい場合は以下をお試しください。

* ヒントで制御
    ```sql
    SELECT /*+ NO_RESULT_CACHE */ count(*) from ssb.customer ;
    ```

* プロシージャで制御
    ```sql
    exec DBMS_RESULT_CACHE.BYPASS(TRUE);
    SELECT DBMS_RESULT_CACHE.STATUS FROM DUAL;  -- 確認用SQL
    SELECT count(*) from ssb.customer ;
    ```

ちなみに、性能試験としてResult Cacheをクリアしたい場合は以下を実施ください。
    ```sql
    execute dbms_result_cache.flush ;
    ```

<br />

## Oracle Database 以外のDBのSQLをOracle DB用に変換するには？ 

簡単なSQLであれば、[Online SQL Conversion tool](http://www.sqlines.com/online)というフリーのツールがあります。
SQL Developer のMigration Workbenchの利用もご検討ください。

<br />

## 結果件数が多いクエリはどうすればよい？ 

「期待した性能が出ないんだけど、なぜ？」という問い合わせにおいて、比較的多いのがこのパターンです。

SQLの結果件数が多い場合、例えばCSVファイルに結果セットを書き出すといったような場合は、データベースの処理は一瞬でも、その後のデータ転送に時間がかかるケースがあります。本来比較したいデータベース自体の性能を評価できません。

そのような場合は、以下の2パターンを計測ください。

* クライアントにデータを転送するパターン  
    SQL*Plusを例に記載していますが、効率よく転送するためのオプション利用をご検討ください。
    ```sh
    # FASTオプションを付与してログイン
    $ sqlplus –s –Fast user/xxxx@yyyy_high
    ```
    ```sql
    set timing on
    set echo on
    set feedback only -- 画面出力を抑制
    set arraysize -- アレイサイズを設定
    
    select xxxx -- 計測対象のSQL
    ```

    詳細については[津島博士のパフォーマンス講座 第62回](https://blogs.oracle.com/otnjp/tsushima-hakushi-62)を参照ください。


* データベースとしての処理時間を計測するパターン  
    以下のように結果セットを他の表にロードすることで、DBからクライアントへの結果セットの転送時間の影響を排除してみてください。
    ```sql
    -- 結果セット格納用のワーク表を作成
    create table TMP_TABLE as <計測対象のクエリ> where 1=2 ;

    -- ワーク表に対して結果セットを流し込む
    insert /*+ APPEND */ into TMP_TABLE <計測対象のクエリ> ;

    ```
    DBの中で処理を完結できるため、純粋にデータベースの性能を比較することが可能です。

<br />

## インスタンスのキャッシュをクリアするにはどうしたらよいですか？   

検証実施時にデータベースのキャッシュ（Buffer Cache）による性能向上効果を排除して計測したいという背景からこのような質問を頂戴しますが、Autonomous Database は alter system による変更は基本的に許されていないため、インスタンスを再起動いただく必要があります。


なお、再起動に際してはGUIだけでなくOCICLIをはじめ、APIを利用して自動化することも可能です。
OCICLIでの実施方法は[こちら](https://oracle-japan.github.io/ocitutorials/adb/adb501-ocicli/)を参照ください。

<br />

## パラレルクエリを利用して高速化したいのだが、どう設定すれば良い？？ 

接続サービスを選択いただきます。接続サービスに関しては[こちら](https://oracle-japan.github.io/ocitutorials/adb/adb201-service-names/)を参照ください。  


* HIGH / MEDIUMの接続の場合
    * 自動でパラレル処理されます。
    
* TPURGENTの接続の場合
    * これまで通り以下の方法でパラレル処理を設定します。
        * ヒントを利用（ただし、ADWの場合はヒントを有効化する必要があります）
            ```sql
            alter session set optimizer_ignore_parallel_hints = false ;
            select /*+ parallel(2) */ count(1) from ssb.customer ;  -- 括弧内はパラレル度
            ```

        * 表のDEGREE属性を指定する
            ```sql
            alter table XXXX degree(2) ;
            ```

        * セッション毎に指定
            ```sql
            alter session enable(force) parallel query;
            select .... ;
            ```

* TP/LOWの場合
    * パラレル処理はできません

<br />

## INSERT処理を高速化するにはどうしたら良いですか？

接続サービスを選択いただきます。接続サービスの説明に関しては[こちら](https://oracle-japan.github.io/ocitutorials/adb/adb201-service-names/)を参照ください。 


* MEDIUM、HIGH接続の場合
    * 特に操作は不要です。  
        > 自動的にパラレル処理となり、ダイレクト・ロードが選択されるため高速に処理されます。

* TPURGENT接続の場合
    * セッション毎にパラレルDMLの設定を行う。
        ```sql
        alter session enable pararel query;
        alter session enable pararel dml;
        insert  .... ;
        commit; -- (or Rollback;)  
        ```
        
        > 自動的にダイレクト・ロードとなるため、従来のAPPENDヒントは不要です。  
        パラレル処理後は必ずトランザクションを終了させてください。続けてInsert等の処理を実行するとエラーになります。
        
* TP/LOW接続の場合
    * シリアル処理になります。
        ```sql
        INSERT /*+ APPEND */ INTO XXX SELECT * FROM YYY ;
        ```
        
        > この場合ダイレクト・ロードにならないため、ダイレクト・ロードによる高速化を指定したい場合は、従来通り明示的にAPPENDヒントを指定してください。  
        APPENDヒントについては、alter session set optimizer_ignore_hints = false ;は不要です  
        なお、CTAS（Create table as select xxxx）については、デフォルトでダイレクト・ロードになり高速処理されます。  
        ただし、HIGH/MEDIUMを選択するか、TPURGENTについては一般的なOracle Databaseと同様でパラレルDDLの設定を事前に行う必要があります。

<br />

[ページトップに戻る](#)

<br />

# <span style="color: brown; ">■ その他 </span>
<br />

## Autonomous Database Serverless型(ADB-S)のタイムアウトの設定は？

[ドキュメント](https://docs.oracle.com/cd/E83857_01/paas/autonomous-database/serverless/adbsb/idle-tile-limits.html#GUID-241F4C85-24E5-4F8A-B9EE-E3FCEF566D36)に記載がありますとおり、5分以上のアイドルセッションはそれによって消費されるリソースが他のユーザに必要になると自動切断されます。またリソースが必要とされない場合でも最大で48時間にて切断されます。  
なお、ネットワークレイヤー、クライアントツールにおいてもタイムアウトの設定が設けられているケースがありますので、予期せず切断されているような場合は、それらも併せてご確認ください。
またそのような場合の一般的な回避策としてはクライアントツールにおけるKeepAliveパラメータの設定があります。

<br />

## Autonomous Databaseインスタンスを削除するには？

Autonomous Databaseインスタンスを削除（利用終了, Terminate）するには、お客様にてインスタンスの終了（Terminate）を実施いただきます。終了の手順は[こちら](https://docs.oracle.com/ja-jp/iaas/Content/Database/Tasks/adbmanaging.htm#console)に記載があります。

なお、自動バックアップもインスタンスと一緒に削除されます。
<br />

[ページトップに戻る](#)

<br />

以上
