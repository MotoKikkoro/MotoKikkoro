# **AWS基本サービス全体像**

***
## 地域
- **エッジロケーション**: 世界中に200か所以上設置されている
- **リージョン**は24以上
- AZは75以上となっている
- リージョンは2つ以上の**AZ**で構成されている
- リージョン>AZ


***

## ★**VPC(Virtual Private Cloud)**
- AWSクラウドのネットワークからユーザー専用の領域を切り出してくれる仮想ネットワークのサービス
- 一つのAZに一つ以上のサブネットがVPCに必須。AWSのアカウント作成時、自動で作成される。
     - サブネット：VPCをさらに細かく区切る小規模なネットワーク。
        - パブリックサブネット：インターネットから到達可能。トラフィックがインターネットゲートウェイ（ルーターの機能）にルーティングされる。デフォルトで作成される。
        - プライベートサブネット：インターネットから到達不可能。インターネットゲートウェイへのルートがない。
        VPCのコンソール画面で作成すると、まずはプライベートサブネットが生成される。
        その後の設定でパブリックサブネットなどに構成することが可能。
        プライベートサブネットからインターネットに接続するには、パブリックサブネットに設定されたNATゲートウェイを経由する。
- 任意のIPアドレス範囲を選択　
- サブネットの作成、ルートテーブル・ネットワークゲートウェイの設定など仮想ネットワーキング環境を制御できる。設定上限数がそれぞれある。
- 接続オプション：インターネット経由・VPN接続/専用線(Direct Connect)。VPNの方が安価で使用までの時間が早いが、信頼性や品質は専用線がよい。
  - 専用線：自社オンプレ環境にあるデバイスに物理的に接続。Direct Connect Gatewayによって、同一アカウントに所属する複数リージョンの複数AZから複数VPCに接続できる
 
- 外部接続：パブリックのAWSネットワークまたは、エンドポイントを利用する
  - **VPCエンドポイント**：グロ＾バルIPを持つAWSサービスに対して、VPC内から直接アクセスするための出口
     - Gateway型：サブネットに特殊なルーティングを設定、VPC内部から直接外部に接続
     - PrivateLink型：サブネットにエンドポイント用のプライベートIPアドレスを生成し、DNSが名前解決でルーティングする
- VPC Flow logs：ネットワークトラフィックを取得し、CloudWatchでモニタリングする機能。無料のため、基本使用する。
- **VPC Peering**：異なるリージョン間、AWSアカウント間のVPCの接続ができる。基本1対1の関係。


## EC2
- EC2のみがアンマネージド型として提供されているAWSサービス
- 数分で利用可能となる仮想サーバー、従量課金制
- 管理者権限で利用可能
- 利用単位を**インスタンス**(=サーバー)とする
- AZ単位でセッティング
- インスタンスの停止状態では、請求は発生しない。
    
    終了状態では、インスタンスは完全に削除され、再起動できない。外部ストレージにデータを保存していない限り、インスタンス内のすべてのデータが削除される。

### EC2のセッティング
- **AMIイメージ**: OSセッティング方式
- **インスタンスタイプ**（?）: CPU・メモリ、ストレージ、ネットワークキャパシティなどで分かれている。
  - 汎用・コンピューティング最適化・メモリ最適化・高速コンピューティング・ストレージ最適化 
  - ファミリーと世代.nano: （例）t2.nano
    - ファミリーは用途によって分かれている
- ストレージ選択
- セキュリティグループ作成・選択
- キーペア: 自身がダウンロードした秘密鍵とマッチした公開鍵を有するインスタンスにアクセスする。
### EC2インスタンスの種類
- **リザーブドインスタンス**: 利用期間を長期指定して利用。値段が安くなる。
  - スタンダード: ・アベイラビリティーゾーンまたは適用範囲 ・ネットワークプラットフォーム (EC2-Classic または VPC) ・インスタンスサイズ（Linuxのみ）を変更可能
  - コンパーティブル: インスタンスファミリー。OSなども変更可能
- **オンデマンドインスタンス**: インスタンスの種類に応じて、時間または秒単位のコンピューティングキャパシティに支払いをする。
- **スポットインスタンス**: AWSの予備のコンピューティング容量を利用できる。格安だが、予備のため突然停止されることがある。一時的な拡張などにはいい。
- **物理対応可能なインスタンス**: ハードウェア専有インスタンス、Dedicated Host、Bare Metal
- 

### EC2で利用するストレージ
- **インスタンスストア**
  - EC2と不可分のブロックレベルの物理ストレージ
　- EC2の停止・終了と共にデータがクリアされる
- **ElasticBlockStore**
  - EC2と独立管理できる
  - SnapshotをS3に保持可能


- CloudFrontはエッジロケーションで展開され、グローバルにコンテンツ配信を実施



## AutoScaling
- インスタンスへのアクセスが高まったときに、自動で新しいインスタンスを増設してパフォーマンスを向上させる機能。無料。
ELBと連携してELBのターゲットグループ内に設置することが一般的。
- 特定のＶＰＣのサブネットを指定して、サブネットが利用されるＡＺを対象とする。
- EC2ステータス（EC2を停止した時にスケーリング）またはELBのヘルスチェックを利用する。
- 動的スケーリングと手動スケーリングがある。 

- 3つのコンポーネント
    - **スケーリングポリシー**: CloudWatchメトリクスとアラームを使用してアクションをトリガーする。
    指定した条件の発生またはスケジュールに基づいてスケーリングできる。
    - **AutoScalingグループ**: インスタンスの最小/最大容量を指定できる
    - **起動テンプレート**
- 終了ポリシー：需要減に基づくスケールインの際に、どのインスタンスから終了するかを設定。種類は以下の通り。
    - OldestInstance, NewestInstance
    - OldestLaunchConfiguration
    - ClosestToNextInstanceHour：次の課金が始まるタイミングが最も近いインスタンスから終了


## ELB (Elastic Load Balancer)
- アプリケーションの受信トラフィックを複数のターゲットに自動的に分散する、高可用性とスケーラビリティを供えたAWSマネージドサービス。
- ターゲットにEC2インスタンス、コンテナ、IPアドレス、Lambda関数などを指定できる。
    - EC2インスタンスの正常・異常を確認し、利用するEC2の振り分けを行う
- スティッキーセッション：セッション中に同じユーザーからきたリクエストを同じEC2インスタンスに送信する
- 主な構成要素：
    - **リスナー**（接続リクエストをチェックするプロセス）
    - **ターゲットグループ**（トラフィックを送信するリソースの集合）
    - **ルール**（ターゲットグループの中でソースIPアドレスとリソースを定義する接続命令のセット）
### ELBの種類
- CLB(Classic Load Balancer)：初期に提供されたELB、複雑な設定はできない
- **ALB(Application Load Balancer)**：レイヤー7の対応が強化された単一ロードバランサー。HTTP(S)トラフィックを負荷分散する。
パスルーティングで複数機能のバランシングが可能なため、複雑な設定ができる。削除保護が可能。
- **NLB(Network Load Balancer)**：レイヤー4で動さ、TPCとUDPトラフィックの負荷分散に適している。超低遅延。高スループットを維持しながら多くのリクエストを裁くように設定された新しいバランサー。
コンテナ化されたアプリのサポートもできる。
- Gateway Load Balancer: サードパーティの仮想アプライアンスをデプロイ、スケーリング、管理するのに役立つ。
複数の仮想アプライアンスにトラフィックを分散させながら、需要に基づいてスケールアップやスケールダウンする。



## アクセス制御
- **セキュリティグループ**：インスタンス(EC2)へのトラフィックのアクセス可否を設定するファイアウォール機能
    - サーバー単位で適用
    - ステートフル：インバウンドのみ設定すればアウトバウンドも許可される。設定したルールがすべて適用される。
- **ネットワークACL**：インスタンスにではなく、サブネット単位でトラフィック制御をする機能
    - VPC/サブネット単位で適用
    - インバウンド設定だけではアウトバウンドは許可されない。数字順で適用される。
    - デフォルトでは全ての通信を許可する設定

***

## ストレージ
### ブロックストレージ
ブロックでデバイスに保存される。
アプリケーションとファイルシステムはブロック単位でデータのアクセス・結合・変更を行う。
- **EC2インスタンスストア**: EC2インスタンスの一時的なブロックレベルストレージ。EC2のホスト（物理サーバ）にあらかじめ設定・アタッチされている。
EC2インスタンスが
停止・終了すると、データが失われる。
- **Elastic Block Store**: EC2にアタッチされるブロックレベルのストレージサービス。アベイラビリティゾーンに関連付けられた取り外し可能なストレージ。
インスタンスを停止・終了しても、EB2ボリューム上に保持される。終了するインスタンスから取り外し、別のインスタンスにアタッチできる。
★高トランザクションのデータベースストレージレイヤーに最適。
- セキュリティグループで管理する対象ではない
- 一つのAZ内で使うもの。基本的に一つのEBSはインスタンスと繋がる。インスタンスは複数のEBSを使うことができる。
- AWS KMS と連携してEBS ボリュームを暗号化および復号できる
    - EBSスナップショットサービス: 別のAZにバックアップやコピーをする。
    バックアップを取得し、それを基に同じEBSを復元・複製できる⇒別のAZにも使用可能、リージョン間をまたいで利用できる。
    いつでも実行可能でEBS操作に影響を与えない。保存期間や世代数は無制限。★DLMを利用して定期的にスケジューリングできる
        - ★**AMI**との違い：AMI(Amazon Machine Image)は、OS設定のイメージ。これを用いて、新規インスタンス設定に転用。
           - AMIには、スナップショットと同じ機能も含まれているので、AMIさえとっていれば、ストレージもバックアップされる。
    - カテゴリ: 
    
        ・SSD(ソリテッドステートドライブ）⇒小中規模・高速　
    
        ・HDD（ハードディスクドライブ）⇒大規模・高いスループットが必要とされるストリーミングワークロード向け。

 

    - 構成
        - RAID0: ストライピング（複数のディスクを1台のディスクのように扱い読み書きを高速化する）
        - RAID1: ミラーリング（二つのボリュームを同時にミラーリングできる


### ファイルストレージ
ユーザー、アプリケーション、サービスが共有ファイルシステムのデータにアクセスできる。

- **Elastic File System(EFS)**: クラウドサービスとオンプレミスのリソースで使用されるスケーラブルなファイルシステム。
★同じファイルセットにアクセスする複数のサーバーがある場合に最適。
- **FSx for Windows**: ファイルサーバー。信頼性が高い完全マネージド型ファイルストレージ。

### オブジェクトストレージ
ファイルがオブジェクトとして保存される。オブジェクトキーが一意に識別する。オブジェクトストレージでファイル更新する場合は、ファイルオブジェクト全体の更新が必要となる。

- **Simple Storage Service(S3)**: オブジェクトレベルのストレージサービス。インスタンスにアタッチせず、ウェブを介してデータを取得する。保存データの容量は無制限。 
★1度書きこんで何度も読み込むストレージ・ビデオ、写真、音楽ファイルなど大きなオブジェクトに最適。
    
    - 保存される情報
        - データ
        - メタデータ：更新日・コンテンツタイプなど
        - キー: オブジェクトを一意に識別するもの
    - **バケット**: リージョン内に作成。グローバルに一意の名前をつける。
    デフォルトでプライベートになっているが、アクセス許可を割り当てられる。
    - セキュリティ: 
        - 方法?@IAMポリシーを利用して、ユーザー・グループ・ロールにアクセス権を付与する。
        - 方法?Aバケットポリシーを利用。JSONベースのIAMポリシー言語で作成。
    - ストレージクラス:
        - 標準
        - 標準低頻度アクセス: データ取り出し料はかかる
        - 1ゾーン低頻度アクセス: 低コスト
        - Intelligent-Tiering: アクセスパターンが変化するデータに使用すると、費用対効果の高いアクセスレイヤーに自動的に移動し、
        ストレージコストを最適化してくれる。
        - Glacier: データのアーカイブに使用。数分でデータを取得できる。
        - Glacier Deepアーカイブ: アクセス頻度が非常に低いもの（年に1~2回程度）に使う。取得時間がデフォルトで12時間。金融やヘルスケア・官公庁などの規制が厳しい業界で使われる。
    - バージョニング:
        - アップロードするたびにオブジェクトの新しいバージョンを生成し、前のものと置き換えられるが、
        同じバケット内でオブジェクトの複数のバージョンを保持する。


***

## データベース
|data baseタイプ|AWSのサービス|
|--------|---------|
|リレーショナル|Aurora, RDS|
|キー・バリュー|DynamoDB|
|インメモリ|ElastiCache|
|ドキュメント|DocumentDB|
|ワイドカラム|Keyspaces|
|グラフ|Neptune|
|時系列|Timestream|
|台帳|Quantum,Ledger Database|


- **RDS**: マネージドウェブサービス。データベースエンジンのAuroraやオープンソースデータベース（MySQL, PostgreSQL）、
商用データベース（Oracle）など多くをサポートしている。
    - DBインスタンス: クラウド内で実行される他と分離されたデータベース環境。
   　ホスト環境へ直接アクセスすることはできない。一つのDBエンジンの実行をサポート。
     内部的にAWSによって管理されるEC2インスタンス上で実行される。
     DBインスタンスは、VPCのプライベートサブネット内に作成。
         - Standard
         - メモリ最適化: 大量にメモリを消費するもの
         - バースト可能パフォーマンス
    - DBインスタンスストレージ: データベースおよびログのストレージにEBSボリュームを使用する。
        - 汎用SSD
        - プロビジョンドIOPS: 低いレイテンシーとスループットを必要とするI/O集約型のデータベースワークロードに使用
        - マグネティックストレージ: 下位互換性のためにサポート。新しいストレージのニーズには適さない。
    - 自動バックアップ: デフォルトで有効、
        - **データベーススナップショット**: バックアップを35日以上保持したい場合は、DBインスタンスの手動スナップショットを利用する。
    - マルチAZ機能: 有効化すると、別のAZにコピーを作成してデータベースを冗長化する。
- **DynamoDB**: 高速で予測可能なパフォーマンスとシームレスなスケーラビリティを提供するフルマネージド型NoSQLサービス。
<font color="Green">サーバーレス</font>。機密データ保管時、暗号化する。
    - コアコンポーネント: テーブル、項目（これ以上分解されない基礎的なデータ要素）、属性（一意に識別可能な属性の集まり）


***

## セキュリティ
### Security GuardDuty
### インフラストラクチャ保護
- **Amazon Detective**: セキュリティデータの分析、視覚化をして、潜在的なセキュリティ問題の根本原因を迅速に特定する
- **AWS Shield**: AWS Platformが提供するDOoSサービス。
L3.4レイヤーへのアタックからの防御
- **AWS WAF**: AWSが提供するウェブ　アプリケーション　ファイアーウォールフレームワーク。
カスタムルールによるアクセス制御。SQLインジェクションやXSS攻撃などへの対応

### データ保護
- **AWS KMS**: 暗号鍵管理サービス
- **AWS CloudHSM**: クラウドベースのハードウェアセキュリティモジュール
- **Amazon Macie**: 機械学習を用いた機密情報の検出、分類、保護

### インシデントリスポンス
- **AWS Lambda**: イベントをトリガーにコード実行
- **CloudWatch**: AWSサービスのリソースモニタリング、ログモニタリング。リソース使用率の最適化も支援する。
ほとんどのAWSサービスでデフォルトで有効。
    - **CloudWatch Logs**: ログを取得して、ログを保存、モニタリング。Lambdaなど一部のサービスではデフォルトでCloudWatch Logsにログを送信する。他のサービスでは追加の設定が必要。
- **AWS Systems Manager**: システムの自動構成と継続的な管理

### リスクコンプライアンス
- **Trusted Advisor**: サービスの使用・構成に関して分析・推奨事項を提案
- **AWS Config**: AWSリソースの構成管理・変更管理。継続的モニタリング・評価。構成変更が行われるまでを記録。
-ログはS3に保存。フルマネージド型サービス。
- **Amazon Inspector**: 自動化されたセキュリティ評価・診断サービス

## IAM(Identity and Access Management)
- パスワードやキーを共有せずに、リソースを管理
### ルートユーザー
- 関連づけられている認証情報: アクセスキー（アクセスキーIDとシークレットアクセスキー）、AWSアカウントの作成に使用したユーザ名とパスワード
### IAMユーザー
- AWSのサービスとリソースを利用する人またはアプリケーションを表すアイデンティティ
### IAMポリシー
- AWSのサービスとリソースへの悪アセスを許可または拒否するJSONドキュメント。ベストプラクティス、
### IAMグループとIAMロール
- **IAMグループ**: IAMユーザの集合、IAMポリシーをグループに割り当てる。
- **IAMロール**: 各リソースに対して一つ割り当てられる。**AWS Security Token Service**を利用し、**AssumeRole**操作を行う⇒他のAWSリソースやサービスへの一時的なアクセスのために人またはサービスが引き受けることができるアイデンティティ。セッションの一時的なセキュリティ認証情報を取得できる。
