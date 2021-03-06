---
title: "ロード・バランサ・サービス 技術FAQ"
excerpt: "Oracle Cloud Infrastructure ロード・バランサ・サービスに関して、利用時のよくある質問に対する回答や、技術的な問題に対する対処策をまとめています。"
layout: faq
categories:
  - FAQ
  - Networking
tags:
  - FAQ
tagline: "Oracle Cloud Infrastructure ロード・バランサ・サービスに関する技術FAQです<br>サービス仕様に関する⼀般的な質問や、既知の問題については下記の外部リンクもご参照ください" 
header:
  actions:
    - label: "Load Balancingに関するよくある質問"
      url: "https://www.oracle.com/jp/cloud/networking/load-balancing-faq.html"
    - label: "既知の問題 - ロード・バランシング"
      url: "https://docs.oracle.com/ja-jp/iaas/Content/knownissues.htm#loadbalancing"
---

# クライアントがロード・バランサに接続できない

## 質問

クライアントがロード・バランサに接続できない理由として考えられる原因を教えてください。

## 回答

ロード ・バランサの接続の問題の一般的な原因には、次のものがあります。

- クライアントが接続しようとしているロード バランサに適切なトラフィック・タイプ（TCP、HTTP、HTTPS）のリスナーが構成されていません。
- ロード ・バランサを含む仮想クラウド・ネットワーク（VCN）のセキュリティ・ルール（「セキュリティ・グループ」または「セキュリティ・リスト」）が、リスナーのトラフィックを許可していません。
- ロード・バランサの作成でセキュリティ・リスト・ルールを自動的に追加していない場合、ロード・バランサやバックエンド・サーバーのサブネットに適切なイングレスおよびエグレス・セキュリティ・ルールが構成されていません。
- セキュリティ・ルールにヘルス・チェック・リクエストのソースのIP範囲が含まれていない可能性があります。ヘルス・チェックのソースIPは、各バックエンド・サーバーの「詳細」ページで確認できます。APIを使用して、HealthCheckResultオブジェクトのsourceIpAddressフィールドでIPを検索することもできます。
- ルート表のルート・ルールの構成が不適切な場合、リクエストをブロックしているか、不適切にルーティングしています。

**関連情報 :**

- [Oracle Cloud Infrastructureドキュメント : リスナーの管理](https://docs.oracle.com/ja-jp/iaas/Content/Balance/Tasks/managinglisteners.htm#Managing_Listeners){:target="_blank"}
- [Oracle Cloud Infrastructureドキュメント : セキュリティ・ルール](https://docs.oracle.com/ja-jp/iaas/Content/Network/Concepts/securityrules.htm#Security_Rules){:target="_blank"}
- [Oracle Cloud Infrastructureドキュメント : バックエンド・サーバーの管理](https://docs.oracle.com/ja-jp/iaas/Content/Balance/Tasks/managingbackendservers.htm#Managing_Backend_Servers){:target="_blank"}
- [Oracle Cloud Infrastructureドキュメント : ヘルス・ステータス](https://docs.oracle.com/ja-jp/iaas/Content/Balance/Tasks/managingbackendservers.htm#healthstatus){:target="_blank"}
- [Oracle Cloud Infrastructureドキュメント : HealthCheckResult](https://docs.oracle.com/ja-jp/iaas/api/#/en/loadbalancer/20170115/HealthCheckResult/){:target="_blank"}  

<a href="#main" class="back-to-top">{{ site.data.ui-text[site.locale].back_to_top | default: 'Back to Top' }} &uarr;</a>
<br />

# ホストベースのルーティングを設定するには?

## 質問

ホストベースのルーティングを設定するにはどうすればよいですか？

## 回答

リクエスト・ルーティングの仮想ホスト名をリスナーに割り当てることで、ホスト名単位でのトラフィックのルーティングを設定することが可能です。
また、リスナーあたりの仮想ホスト名は最大16個まで割り当てることが可能です。

仮想ホスト名をリスナーに適用するには、最初に、1つ以上の仮想ホスト名を作成します。
次に、ロード・バランサのリスナーを作成または更新し、使用する1つ以上の仮想ホスト名を指定します。

- **仮想ホスト名を作成するには**

  1. ナビゲーション・メニューを開きます。「**ネットワーキング**」をクリックして「**ロード・バランサ**」をクリックします。
  2. 変更するロード・バランサを含む**コンパートメント**を選択し、**ロード・バランサの名前**をクリックします。
  3. 「**リソース**」メニューで「**ホスト名**」をクリックし、「**ホスト名の作成**」をクリックします。
  4. 「**ホスト名の作成**」ダイアログ・ボックスで、次を入力します:
      - 名前: 必須。ホスト名のわかりやすい名前を指定します。名前は一意である必要があり、変更することはできません。機密情報の入力は避けてください。
      - ホスト名: 必須。仮想ホスト名を指定します。有効なホスト名の構成および動作の説明は、仮想ホスト名を参照してください。
  5. 「**作成**」をクリックします。「**作業リクエスト送信済**」ダイアログ・ボックスが開きます。
  6. ダイアログ・ボックスを閉じるには、「**閉じる**」をクリックします。「**作業リクエスト**」ページを開いて作業リクエストのステータスを表示するには、「**すべての作業リクエストの表示**」をクリックします。

仮想ホスト名を作成すると、関連付けられたロード・バランサでその名前を使用できるようになります。  
ホスト名を適用するには、[リスナーを作成または更新](https://docs.oracle.com/ja-jp/iaas/Content/Balance/Tasks/managinglisteners.htm#create){:target="_blank"} します。

**関連情報 :**
- [Oracle Cloud Infrastructureドキュメント : リクエスト・ルーティングの管理](https://docs.oracle.com/ja-jp/iaas/Content/Balance/Tasks/managingrequest.htm#routing__vhost){:target="_blank"}

<a href="#main" class="back-to-top">{{ site.data.ui-text[site.locale].back_to_top | default: 'Back to Top' }} &uarr;</a>
<br />

# パスベースのルーティングを実現するには?

## 質問

ロード・バランサでのパスベースのルーティングを実現するにはどうすればよいですか？

## 回答

リクエスト・ルーティングのパス・ルート・ルールをリスナーに割り当てることで、パスベースのルーティングを設定することが可能です。

パス・ルート・ルールをリスナーに適用するには、最初に、ルールが含まれる「**パス・ルート・セット**」を作成します。
パス・ルート・セットは、ロード・バランサの構成の一部になります。
次に、ロード・バランサのリスナーを作成または更新するときに、使用する「**パス・ルート・セット**」を指定します。
リスナーからパス・ルート・セットを削除するには、リスナーを編集し、「**パス・ルート・セット**」オプションとして「**なし**」を選択します。

詳細の手順については、[リクエスト・ルーティングの管理](https://docs.oracle.com/ja-jp/iaas/Content/Balance/Tasks/managingrequest.htm#){:target="_blank"} の「パス・ルート・セットを作成するには」を参照してください。

**関連情報 :**

- [Oracle Cloud Infrastructureドキュメント : リスナーを編集](https://docs.oracle.com/ja-jp/iaas/Content/Balance/Tasks/managinglisteners.htm#edit){:target="_blank"}
- [Oracle Cloud Infrastructureドキュメント : リクエスト・ルーティングの管理](https://docs.oracle.com/ja-jp/iaas/Content/Balance/Tasks/managingrequest.htm#){:target="_blank"}

<a href="#main" class="back-to-top">{{ site.data.ui-text[site.locale].back_to_top | default: 'Back to Top' }} &uarr;</a>
<br />

# カスタムHTTPヘッダーに基づいてルーティングするには?

## 質問

ロードバランサーを使用して、カスタムHTTPヘッダーに基づきトラフィックをルーティングする方法を教えてください。

## 回答

ロード・バランサのリスナーに適用されるルール・セットのリクエスト/レスポンス・ヘッダー・ルールを作成する事で、カスタムHTTPヘッダーの値に基づいて、リクエストに対する特定のアクションを実行できます。

ルール・セットをリスナーに適用するには、最初に、ルールが含まれるルール・セットを作成します。  
ロード・バランサのリスナーを作成または更新するときに、使用するルール・セットを指定できます。

- **ルール・セットを作成するには**

1. ナビゲーション・メニューを開きます。「**ネットワーキング**」に移動して「**ロード・バランサ**」をクリックします。
2. 変更するロード・バランサを含む**コンパートメント**を選択し、**ロード・バランサの名前**をクリックします。
3. 「**リソース**」メニューで、「**ルール・セット**」をクリックし、「**ルール・セットの作成**」をクリックします。
4. 「**ルール・セットの作成**」ダイアログ・ボックスで、次を入力します:
    - **名前**: 必須。ルール・セットのわかりやすい名前を指定します。名前は一意である必要があり、変更することはできません。機密情報の入力は避けてください。
    - **リクエスト・ヘッダー・ルールの指定**: リクエスト・ヘッダー・ルールを追加する場合は、このボックスを選択します。
        - **順序**: オプション。複数のルールがある場合は、上矢印または下矢印をクリックして、対応するルールを移動できます。
        - **アクション**: ルールで適用するアクションを選択します。使用可能なアクションを示します:
            - **リクエスト・ヘッダーの追加**  
            指定したヘッダーと値を受信リクエストに追加します。  
            指定したヘッダーがすでに存在する場合は、システムによって置き換えられます。  
            同じ名前のヘッダーが複数存在する場合、それらはすべて削除され、指定したヘッダーと値に対応するヘッダーが1つ追加されます。  
            - **リクエスト・ヘッダーの拡張**  
            指定した接頭辞または接尾辞を受信リクエストに追加します。  
            このアクションを選択する場合は、接頭辞の値または接尾辞の値(あるいはその両方)を指定します。  
            複数の値を持つヘッダーについては、このルールはサポートされていません。  
            - **リクエスト・ヘッダーの削除**  
            指定されたヘッダーを削除します。
            同じヘッダーがリクエスト内に複数回出現する場合、ロード・バランサは、指定されたヘッダーのすべての出現部分を削除します。
        - **ヘッダー**: RFC 7230に準拠するヘッダー名。
        - **値**: (追加ルールのみ。)RFC 7230に準拠するヘッダー値。
        - **接頭辞**: (拡張ルールのみ。)既存のヘッダー名の先頭に追加する文字列。結果のヘッダーはRFC 7230に準拠している必要があります。
        - **接尾辞**: (拡張ルールのみ。)既存のヘッダー名の末尾に追加する文字列。結果のヘッダーはRFC 7230に準拠している必要があります。
        - **+別のリクエスト・ヘッダー・ルール** (オプション)。このボタンをクリックして別のルールを作成するか、対応する「X」をクリックして既存のルールを削除します。
    - **レスポンス・ヘッダー・ルールの指定**: レスポンス・ヘッダー・ルールを追加する場合は、このボックスを選択します。
        - **順序**: オプション。複数のルールがある場合は、上矢印または下矢印をクリックして、対応するルールを移動できます。
        - **アクション**: ルールで適用するアクションを選択します。使用可能なアクションを示します:
            - **レスポンス・ヘッダーの追加**  
            指定したヘッダーと値を送信レスポンスに追加します。  
            指定したヘッダーがすでに存在する場合は、システムによって置き換えられます。  
            同じ名前のヘッダーが複数存在する場合、それらはすべて削除され、指定したヘッダーと値に対応するヘッダーが1つ追加されます。
            - **レスポンス・ヘッダーの拡張**  
            指定した接頭辞または接尾辞を受信リクエストに追加します。  
            このアクションを選択する場合は、接頭辞の値または接尾辞の値(あるいはその両方)を指定します。  
            複数の値を持つヘッダーについては、このルールはサポートされていません。
            - **レスポンス・ヘッダーの削除**  
            指定されたヘッダーを削除します。  
            同じヘッダーがレスポンス内に複数回出現する場合、ロード・バランサは、指定されたヘッダーのすべての出現部分を削除します。
        - **ヘッダー**: RFC 7230に準拠するヘッダー名。
        - **値**: (追加ルールのみ。)RFC 7230に準拠するヘッダー値。
        - **接頭辞**: (拡張ルールのみ。)既存のヘッダー名の先頭に追加する文字列。結果のヘッダーはRFC 7230に準拠している必要があります。
        - **接尾辞**: (拡張ルールのみ。)既存のヘッダー名の末尾に追加する文字列。結果のヘッダーはRFC 7230に準拠している必要があります。
        - **+別のリクエスト・ヘッダー・ルール** (オプション)。このボタンをクリックして別のルールを作成するか、対応する「X」をクリックして既存のルールを削除します。
5. 「**作成**」をクリックします。

ルール・セットを作成すると、関連付けられたロード・バランサでそのセットを使用できるようになります。  
ルール・セットを適用するには、[リスナーを更新](https://docs.oracle.com/ja-jp/iaas/Content/Balance/Tasks/managinglisteners.htm#edit){:target="_blank"} します。

**関連情報 :**
- [Oracle Cloud Infrastructureドキュメント : ルール・セットの管理](https://docs.oracle.com/ja-jp/iaas/Content/Balance/Tasks/managingrulesets.htm){:target="_blank"}
- [Oracle Cloud Infrastructureドキュメント : リクエストおよびレスポンス・ヘッダー・ルール](https://docs.oracle.com/ja-jp/iaas/Content/Balance/Tasks/managingrulesets.htm#header-rules){:target="_blank"}

<a href="#main" class="back-to-top">{{ site.data.ui-text[site.locale].back_to_top | default: 'Back to Top' }} &uarr;</a>
<br />

# クライアントのソースIPに基づいてルーティングするには?

## 質問

ロード・バランサを使用して、クライアントのソースIPに基づいてリクエストをルーティングするにはどうすればよいですか？

## 回答

[ロード・バランサを作成](https://docs.oracle.com/ja-jp/iaas/Content/Balance/Tasks/managingloadbalancer.htm#Managing_Load_Balancers){:target="_blank"} した後に、トラフィック分散を制御するポリシーであるIPハッシュ・ポリシーをバックエンド・サーバーに適用することで、送信元IPアドレスのハッシュキーに基づいたトラフィックのルーティングが可能です。  
IPハッシュ・ポリシーでは、受信リクエストのソースIPアドレスがハッシュ・キーとして使用され、スティッキーではないトラフィックが同じバックエンド・サーバーにルーティングされます。  
ロード・バランサは、サーバーが使用可能であるかぎり、同じクライアントから同じバックエンド・サーバーにリクエストをルーティングします。

**関連情報：**  
[Oracle Cloud Infrastructureドキュメント : IPハッシュ](https://docs.oracle.com/ja-jp/iaas/Content/Balance/Reference/lbpolicies.htm#hash){:target="_blank"}
[Oracle Cloud Infrastructureドキュメント : ロード・バランシング・ポリシーの仕組み](https://docs.oracle.com/ja-jp/iaas/Content/Balance/Reference/lbpolicies.htm#hash){:target="_blank"}

<a href="#main" class="back-to-top">{{ site.data.ui-text[site.locale].back_to_top | default: 'Back to Top' }} &uarr;</a>
<br />

# あるドメインを別のドメインにリダイレクトするには?

## 質問

ロード・バランサを使用して、あるドメインを別のドメインにリダイレクトするにはどうすればよいですか？

## 回答

ロード・バランサのリスナーに適用されるルール・セットの[URLリダイレクト・ルール](https://docs.oracle.com/ja-jp/iaas/Content/Balance/Tasks/managingrulesets.htm#redirect){:target="_blank"} を作成する事で、HTTPからHTTPSへのリダイレクト、ドメイン名のリダイレクトやパスベースでのリダイレクトが可能です。

ルール・セットをリスナーに適用するには、最初に、ルールが含まれるルール・セットを作成します。  
ロード・バランサのリスナーを作成または更新するときに、使用するルール・セットを指定できます。

- **ルール・セットを作成するには**

1. ナビゲーション・メニューを開きます。「**ネットワーキング**」に移動して「**ロード・バランサ**」をクリックします。
2. 変更するロード・バランサを含む**コンパートメント**を選択し、**ロード・バランサの名前**をクリックします。
3. 「**リソース**」メニューで、「**ルール・セット**」をクリックし、「**ルール・セットの作成**」をクリックします。
4. 「**ルール・セットの作成**」ダイアログ・ボックスで、次を入力します:
    - **名前**: 必須。ルール・セットのわかりやすい名前を指定します。名前は一意である必要があり、変更することはできません。機密情報の入力は避けてください。
    - **URLリダイレクト・ルールの指定**: URLリダイレクト・ルールを追加する場合は、このボックスを選択します。
        - **ソース・パス**: リダイレクト・ルールをトリガーする受信パス文字列を指定します。たとえば、/videoです。
        - **一致タイプ**: 受信パス文字列を評価するときに適用する一致条件を選択します。使用可能な一致タイプは次のとおりです:
            - FORCE_LONGEST_PREFIX_MATCH
              システムでは、受信URLパスの開始部分が最適に最長一致するリダイレクト・ルール・パス文字列を検索します。
            - EXACT_MATCH
              受信URLパスは、指定されたパス文字列と正確かつ完全に一致する必要があります。
            - PREFIX_MATCH
              受信URLパスの開始部分は、指定されたパス文字列と完全に一致する必要があります。
            - SUFFIX_MATCH
              受信URLパスの終了部分は、指定されたパス文字列と完全に一致する必要があります。
        - **リダイレクト先**: 少なくとも1つのURLコンポーネント・フィールドに値を指定します。変更しないコンポーネント・フィールドには、受信URLの値が保持されます。 オプションで、「完全なURLに切替え」リンクをクリックして、リダイレクトURLを手動で入力します。
        - **プロトコル**: リダイレクトURLで使用するHTTPプロトコルを指定します。有効な値は次のとおりです:
            - `{protocol}`
            - HTTPS
            - HTTP
        - **ホスト**: リダイレクトURLに対して有効なドメイン名(ホスト名)またはIPアドレスを指定します。このプロパティでは、すべてのリダイレクトURLトークンが有効です。
        - **ポート**: リダイレクトURLで使用する通信ポートを指定します。有効な値には、1から65535までの整数が含まれます。
        - **パス**: リダイレクトURLで使用するHTTP URLパス。このプロパティでは、すべてのリダイレクトURLトークンが有効です。
           > **✔重要**「パス文字列が`{path}`トークンで開始していない場合、スラッシュ文字/で開始する必要があります。」
        - **問合せ**: リダイレクトURLで使用する問合せ文字列を指定します。このプロパティでは、すべてのリダイレクトURLトークンが有効です。
           > **✔重要**「問合せ文字列が`{query}`トークンで開始していない場合、疑問符?文字で開始する必要があります。」
        - **レスポンス・コード**: 受信リクエストがリダイレクトされたときに返されるHTTPステータス・コードを指定します。デフォルトのレスポンス・コードは`302 Found`です。  
        標準HTTP仕様によるリダイレクトに有効なレスポンス・コードは次のとおりです:
            - `301 Moved Permanently`
            - `302 Found`
            - `303 See Other`
            - `307 Temporary Redirect`
            - `308 Permanent Redirect`
    - **+別のURLリダイレクト・ルール** (オプション)。このボタンをクリックして別のルールを作成するか、対応する「X」をクリックして既存のルールを削除します。
5. 「**作成**」をクリックします。

ルール・セットを作成すると、関連付けられたロード・バランサでそのセットを使用できるようになります。  
ルール・セットを適用するには、[リスナーを更新](https://docs.oracle.com/ja-jp/iaas/Content/Balance/Tasks/managinglisteners.htm#edit){:target="_blank"} します。

**関連情報 :**
- [Oracle Cloud Infrastructureドキュメント : ルール・セットの管理](https://docs.oracle.com/ja-jp/iaas/Content/Balance/Tasks/managingrulesets.htm){:target="_blank"}
- [Oracle Cloud Infrastructureドキュメント : URLリダイレクト・ルール](https://docs.oracle.com/ja-jp/iaas/Content/Balance/Tasks/managingrulesets.htm#redirect){:target="_blank"}

<a href="#main" class="back-to-top">{{ site.data.ui-text[site.locale].back_to_top | default: 'Back to Top' }} &uarr;</a>
<br />

# HTTP リクエストを HTTPS にリダイレクトするには?

## 質問

ロード・バランサを使い HTTP リクエストを HTTPS にリダイレクトするにはどうすればよいですか?

## 回答

ロード・バランサのリスナーに適用されるルール・セットのURLリダイレクト・ルールを作成する事で、HTTPからHTTPSへのリダイレクト、ドメイン名のリダイレクトやパスベースでのリダイレクトが可能です。

ルール・セットをリスナーに適用するには、最初に、ルールが含まれるルール・セットを作成します。  
ロード・バランサのリスナーを作成または更新するときに、使用するルール・セットを指定できます。

- **ルール・セットを作成するには**

1. ナビゲーション・メニューを開きます。「**ネットワーキング**」に移動して「**ロード・バランサ**」をクリックします。
2. 変更するロード・バランサを含む**コンパートメント**を選択し、**ロード・バランサの名前**をクリックします。
3. 「**リソース**」メニューで、「**ルール・セット**」をクリックし、「**ルール・セットの作成**」をクリックします。
4. 「**ルール・セットの作成**」ダイアログ・ボックスで、次を入力します:
    - **名前**: 必須。ルール・セットのわかりやすい名前を指定します。名前は一意である必要があり、変更することはできません。機密情報の入力は避けてください。
    - **URLリダイレクト・ルールの指定**: URLリダイレクト・ルールを追加する場合は、このボックスを選択します。
        - **ソース・パス**: リダイレクト・ルールをトリガーする受信パス文字列を指定します。たとえば、/videoです。
        - **一致タイプ**: 受信パス文字列を評価するときに適用する一致条件を選択します。使用可能な一致タイプは次のとおりです:
            - FORCE_LONGEST_PREFIX_MATCH
              システムでは、受信URLパスの開始部分が最適に最長一致するリダイレクト・ルール・パス文字列を検索します。
            - EXACT_MATCH
              受信URLパスは、指定されたパス文字列と正確かつ完全に一致する必要があります。
            - PREFIX_MATCH
              受信URLパスの開始部分は、指定されたパス文字列と完全に一致する必要があります。
            - SUFFIX_MATCH
              受信URLパスの終了部分は、指定されたパス文字列と完全に一致する必要があります。
        - **リダイレクト先**: 少なくとも1つのURLコンポーネント・フィールドに値を指定します。変更しないコンポーネント・フィールドには、受信URLの値が保持されます。 オプションで、「完全なURLに切替え」リンクをクリックして、リダイレクトURLを手動で入力します。
        - **プロトコル**: リダイレクトURLで使用するHTTPプロトコルを指定します。有効な値は次のとおりです:
            - `{protocol}`
            - HTTPS
            - HTTP
        - **ホスト**: リダイレクトURLに対して有効なドメイン名(ホスト名)またはIPアドレスを指定します。このプロパティでは、すべてのリダイレクトURLトークンが有効です。
        - **ポート**: リダイレクトURLで使用する通信ポートを指定します。有効な値には、1から65535までの整数が含まれます。
        - **パス**: リダイレクトURLで使用するHTTP URLパス。このプロパティでは、すべてのリダイレクトURLトークンが有効です。
            > **✔重要**「パス文字列が`{path}`トークンで開始していない場合、スラッシュ文字/で開始する必要があります。」
        - **問合せ**: リダイレクトURLで使用する問合せ文字列を指定します。このプロパティでは、すべてのリダイレクトURLトークンが有効です。
            > **✔重要**「問合せ文字列が`{query}`トークンで開始していない場合、疑問符?文字で開始する必要があります。」
        - **レスポンス・コード**: 受信リクエストがリダイレクトされたときに返されるHTTPステータス・コードを指定します。デフォルトのレスポンス・コードは`302 Found`です。  
        標準HTTP仕様によるリダイレクトに有効なレスポンス・コードは次のとおりです:
            - `301 Moved Permanently`
            - `302 Found`
            - `303 See Other`
            - `307 Temporary Redirect`
            - `308 Permanent Redirect`
    - **+別のURLリダイレクト・ルール** (オプション)。このボタンをクリックして別のルールを作成するか、対応する「X」をクリックして既存のルールを削除します。
5. 「**作成**」をクリックします。

ルール・セットを作成すると、関連付けられたロード・バランサでそのセットを使用できるようになります。  
ルール・セットを適用するには、[リスナーを更新](https://docs.oracle.com/ja-jp/iaas/Content/Balance/Tasks/managinglisteners.htm#edit){:target="_blank"} します。

**関連情報 ：**

- [Oracle Cloud Infrastructureドキュメント : ルール・セットの管理](https://docs.oracle.com/ja-jp/iaas/Content/Balance/Tasks/managingrulesets.htm){:target="_blank"}
- [Oracle Cloud Infrastructureドキュメント : URLリダイレクト・ルール](https://docs.oracle.com/ja-jp/iaas/Content/Balance/Tasks/managingrulesets.htm#redirect){:target="_blank"}
- [Oracle Cloud Infrastructureドキュメント : リスナーを更新](https://docs.oracle.com/ja-jp/iaas/Content/Balance/Tasks/managingrulesets.htm#redirect){:target="_blank"}

<a href="#main" class="back-to-top">{{ site.data.ui-text[site.locale].back_to_top | default: 'Back to Top' }} &uarr;</a>
<br />

# ロード・バランサでIPv6アドレスを使用するには?

## 質問

IPv6アドレスを使用するようにロード・バランサを設定するにはどうすればよいですか？

## 回答

IPv6 アドレスを使用するには、ロード・バランサを作成する際に「**IPv6アドレス割当ての有効化**」を選択してください。  
ロード・バランサを作成する場合、オプションでIPv4/IPv6デュアルスタック構成を選択できます。IPv6オプションを選択すると、ロード・バランシング・サービスによってIPv4とIPv6アドレスの両方がロード・バランサに割り当てられます。  
ロード・バランサは、割り当てられたIPv6アドレスに送信されるクライアント・トラフィックを受信しますが、バックエンド・サーバーとの通信は、IPv4アドレスのみを使用して通信します。ロード・バランサとバックエンド・サーバー間にIPv6通信はありません。

> IPv6アドレスの割当ては、ロード・バランサの作成時にのみ行われます。既存のロード・バランサにIPv6アドレスを割り当てることはできません。

**関連情報 ：**

- [Oracle Cloud Infrastructureドキュメント : IPv6アドレス](https://docs.oracle.com/ja-jp/iaas/Content/Network/Concepts/ipv6.htm){:target="_blank"}
- [Oracle Cloud Infrastructureドキュメント : ロード・バランサ](https://docs.oracle.com/ja-jp/iaas/Content/Balance/Concepts/balanceoverview.htm#Overview_of_Load_Balancing){:target="_blank"}

<a href="#main" class="back-to-top">{{ site.data.ui-text[site.locale].back_to_top | default: 'Back to Top' }} &uarr;</a>
<br />

# 予約済パブリックIPアドレスを新規もしくは既存のロード・バランサにアタッチする方法は?

## 質問

予約済パブリックIPアドレスを新規もしくは既存のロード・バランサにアタッチするには、どうすればよいですか？

## 回答

新規のロード・バランサを作成する時に「予約済パブリックIPアドレス」の選択が可能ですが、既存のロード・バランサに予約済パブリックIPアドレスをアタッチする方法はございません。

**予約済パブリックIPアドレスを新しいロード・バランサにアタッチする**

- **新規の予約済パブリックIPを作成するには**

1. 予約済パブリックIPを作成するリージョンおよびコンパートメントを表示していることを確認します。
2. ナビゲーション・メニューを開きます。「**ネットワーキング**」をクリックして、IP管理グループにある「**予約済IP**」をクリックします。
3. 「**パブリックIPアドレスの作成**」をクリックします。
4. 次を入力します:
    - **名前**: 予約済パブリックIPアドレスのわかりやすい名前。名前は一意である必要はなく、後で変更できます。機密情報の入力は避けてください。
    - **コンパートメント**: そのままにします。
    - **IPアドレス・ソース**を選択します。
    - (拡張オプション) **タグ**: リソースの作成権限がある場合、そのリソースにフリーフォーム・タグを適用する権限もあります。  
    定義済のタグを適用するには、タグ・ネームスペースを使用する権限が必要です。タグ付けの詳細は、リソース・タグを参照してください。  
    タグを適用すべきかわからない場合は、このオプションをスキップするか(後でタグを適用できます)、管理者に問い合せてください。
5. 「**パブリックIPの予約**」をクリックします。

作成した予約済パブリックIPアドレスを新規のロード・バランサにアタッチするには、ロード・バランサの作成時に「**予約済IPアドレス**」をクリックし、「**既存の予約済IPアドレスの選択**」を選択します。  
詳細は、[ロード・バランサの作成](https://docs.oracle.com/ja-jp/iaas/Content/Balance/Tasks/managingloadbalancer.htm){:target="_blank"} を参照してください。

**関連情報 ：**

- [Oracle Cloud Infrastructureドキュメント : パブリックIPアドレス](https://docs.oracle.com/ja-jp/iaas/Content/Network/Tasks/managingpublicIPs.htm){:target="_blank"}
- [Oracle Cloud Infrastructureドキュメント : ロード・バランサの作成](https://docs.oracle.com/ja-jp/iaas/Content/Balance/Tasks/managingloadbalancer.htm){:target="_blank"}

<a href="#main" class="back-to-top">{{ site.data.ui-text[site.locale].back_to_top | default: 'Back to Top' }} &uarr;</a>
<br />

# ヘルス・チェックの失敗を修正するには?

## 質問

ロード・バランサのトラブルシューティングを行い、ヘルス・チェックの失敗を修正するにはどうすればよいですか？

## 回答

ロード・バランサをトラブルシューティングし、ヘルス・チェックの失敗を修正するには：

1. ヘルス・チェック・エラーを見つけるために、ロード・バランサのアクセスとエラー・ログを有効にします。詳細については、[ログ管理](https://docs.oracle.com/en-us/iaas/Content/Balance/Tasks/managingloadbalancer_topic-Logging.htm#LogManagement){:target="_blank"} を参照してください。
2. 受け取ったヘルス・チェック・エラーについて、考えられる原因と解決策は以下の通りになります。

- **No Healthy Backends**  
    - **エラー・メッセージ**:  
      - `No healthy backends available in associated backendSet`  
    - **Oracle Cloud Infrastructureログ・カテゴリ**: エラー・ログ  
    - **症状**: クライアントは`502 Bad Gateway`エラーで失敗します。  
    - **考えられる原因**:  
      - バックエンド・セットにバックエンドがありません。  
      - ヘルス・チェックに応答するバックエンドはありません。  
    - **可能な解決策**:  
      - バックエンドがヘルス・チェックに応答しない理由を特定します。    
      - ステータス・コード、正規表現、間隔、タイムアウト、ポート、プロトコルなどのヘルス・チェック設定を確認して調整します。  
    - **トラブルシューティング・ドキュメント**: [ヘルス・チェック・ポリシーの編集](https://docs.oracle.com/ja-jp/iaas/Content/Balance/Tasks/editinghealthcheck.htm){:target="_blank"}  

- **Status Code Issues**  
    - **エラー・メッセージ**:  
      - `"msg":"invalid statusCode","statusCode":XXX,"expected":"200"`  
    - **Oracle Cloud Infrastructureログ・カテゴリ**: エラー・ログ  
    - **症状**:  
      - バックエンドはヘルス・チェックに失敗します。  
      - クライアントは`502 Bad Gateway`エラーで失敗します。  
      - `invalid statusCode`がエラー・ログに表示されます。  
    - **考えられる原因**:  
      - バックエンドが正しくない応答コードで応答しています。  
      - 応答コードが一致しないため、バックエンドのヘルス・チェックが失敗します。  
      - ヘルス・チェックの失敗は、正規表現の本文に予期しないステータス・コードが含まれていることが原因です。  
    - **可能な解決策**:  
      - バックエンドが間違った応答コードを送信している理由を特定してください。
      - ヘルス・チェックのパスまたはステータス・コードを調整して、バックエンドに一致させます。  
    - **トラブルシューティング・ドキュメント**: [ヘルス・チェック・ポリシーの編集](https://docs.oracle.com/ja-jp/iaas/Content/Balance/Tasks/editinghealthcheck.htm){:target="_blank"}  

- **Response Match Failed**  
    - **エラー・メッセージ**:  
      - `"response match result: failed"`  
    - **Oracle Cloud Infrastructureログ・カテゴリ**: エラー・ログ  
    - **症状**:  
      - バックエンドはヘルスチェックに失敗します。  
      - クライアントは`502 Bad Gateway`エラーで失敗します。  
      - `"response match result: failed"` がエラー・ログに表示されます。  
    - **考えられる原因**:  
      - 正規表現の不一致、不正な値が返される、またはヘルス・チェックに提供された不正な値が原因で、バックエンドのヘルス・チェックが失敗します。  
    - **可能な解決策**:  
      - バックエンドが正しくない本文を送信している理由を特定してください。  
      - ヘルス・チェックのパスまたは正規表現パターンを調整して、バックエンドに一致させます。  
    - **トラブルシューティング・ドキュメント**: [ヘルス・チェック・ポリシーの編集](https://docs.oracle.com/ja-jp/iaas/Content/Balance/Tasks/editinghealthcheck.htm){:target="_blank"}  

- **Unreachable Host**  
    - **エラー・メッセージ**:  
      - `"errno":"EHOSTUNREACH","syscall":"connect"`  
      - `"ECONNREFUSED","errno":"ECONNREFUSED"`  
    - **Oracle Cloud Infrastructureログ・カテゴリ**: エラー・ログ  
    - **症状**:  
      - バックエンドはヘルス・チェックに失敗します。  
      - クライアントは`502 Bad Gateway`エラーで失敗します。  
      - `"EHOSTUNREACH"`がエラー・ログに表示されます。
    - **考えられる原因**:  
      - ホストに到達できないため、バックエンドのヘルス・チェックが失敗します。  
      - 接続がリセットされたため、バックエンドのヘルス・チェックが失敗します。  
      - アプリケーションまたはファイアウォールが接続を積極的に拒否しています。  
    - **可能な解決策**:  
      - インスタンスのファイアウォールをチェックして、トラフィックが許可されていることを確認します。  
      - インスタンスをチェックして、アプリケーションが実行されていることを確認します。  
      - ネットワーク・セキュリティ・グループとセキュリティ・リストをチェックして、トラフィックが許可されていることを確認します。  
    - **トラブルシューティング・ドキュメント**: [アクセスとセキュリティ](https://docs.oracle.com/ja-jp/iaas/Content/Network/Concepts/permissions.htm#Access_and_Security){:target="_blank"}    

- **Health Status Issues**  
    - **エラー・メッセージ**:  
      - `"healthStatus":"Unhealthy to Healthy"`  
      - `"healthStatus":"Healthy to Unhealthy"`
    - **Oracle Cloud Infrastructureログ・カテゴリ**: エラー・ログ  
    - **症状**:  
      - クライアントは期待どおりに動作する時もありますが、定期的に失敗します。  
      - バックエンドはヘルス・チェックの状態を切り替えます。  
      - `"Unhealthy to Healthy"`または`"Healthy to Unhealthy"`がエラー・ログに表示されます。  
    - **考えられる原因**:  
      - 不健全なバックエンドが健全な状態になる。  
      - バックエンドのヘルス・ステータスが頻繁に変化する場合は、慢性的な問題を示している可能性があります。  
    - **可能な解決策**:  
      - インスタンスのヘルス・ステータスが異常に変化していないことを確認します。  
      - アプリケーション固有の問題がないか、バックエンド・サーバーのアプリケーション・ログを確認します。  

- **Connection Issues**  
    - **エラー・メッセージ**:  
      - `"msg":"connect timed out","elapsed":3000}`  
    - **Oracle Cloud Infrastructureログ・カテゴリ**: エラー・ログ  
    - **症状**:  
      - クライアントは`502 Bad Gateway`エラーで失敗します。  
      - バックエンドが定期的または慢性的にヘルス・チェックに失敗しています。  
      - `"connect timed out"` がエラー・ログに表示されます。
    - **考えられる原因**:  
      - バックエンド・サーバーは、予期された時間内にヘルス・チェックに応答していません。
      - データベース、アプリケーション・サービスまたはAPIを含むアップストリームの依存関係が遅いか、Oracle Cloud Infrastructure ファイル・ストレージ、ブロック・ストレージ、オブジェクト・ストレージなどのストレージ・サービスが遅い。
    - **可能な解決策**:  
      - バックエンドに対してローカル・テストを実行して、ロード・バランサを原因として排除します。  
      - すべてのアップストリームの依存関係のパフォーマンスを確認します。  
      - 何らかのタイムアウトを報告する依存関係がないか、バックエンド・サーバーのアプリケーション・ログを確認します。  
    - **トラブルシューティング・ドキュメント**: [TCP および HTTPバックエンド・サーバーのテスト](https://docs.oracle.com/en-us/iaas/Content/Balance/Reference/troubleshooting_load_balancer.htm#TestTCPHTTPBackendServers){:target="_blank"}

**関連情報 ：**

- [Oracle Cloud Infrastructureドキュメント : ログ管理](https://docs.oracle.com/en-us/iaas/Content/Balance/Tasks/managingloadbalancer_topic-Logging.htm#LogManagement){:target="_blank"}
- [Oracle Cloud Infrastructureドキュメント : ロード・バランシングのトラブルシューティング](https://docs.oracle.com/en-us/iaas/Content/Balance/Reference/troubleshooting_load_balancer.htm){:target="_blank"}
- [Oracle Cloud Infrastructureドキュメント : ヘルス・チェック・ポリシーの編集](https://docs.oracle.com/ja-jp/iaas/Content/Balance/Tasks/editinghealthcheck.htm){:target="_blank"}
- [Oracle Cloud Infrastructureドキュメント : アクセスとセキュリティ](https://docs.oracle.com/ja-jp/iaas/Content/Network/Concepts/permissions.htm#Access_and_Security){:target="_blank"}
- [Oracle Cloud Infrastructureドキュメント : TCP および HTTPバックエンド・サーバーのテスト](https://docs.oracle.com/en-us/iaas/Content/Balance/Reference/troubleshooting_load_balancer.htm#TestTCPHTTPBackendServers){:target="_blank"}
