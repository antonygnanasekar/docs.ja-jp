---
title: "ピアツーピア コラボレーション | Microsoft Docs"
ms.custom: ""
ms.date: "03/30/2017"
ms.prod: ".net-framework"
ms.reviewer: ""
ms.suite: ""
ms.tgt_pltfrm: ""
ms.topic: "article"
dev_langs: 
  - "VB"
  - "CSharp"
  - "C++"
  - "jsharp"
ms.assetid: b6216d88-bccb-4a59-9f1c-9f751708e811
caps.latest.revision: 7
author: "mcleblanc"
ms.author: "markl"
manager: "markl"
caps.handback.revision: 7
---
# ピアツーピア コラボレーション
ピアツーピアにはネットワーキングにより、クライアント ベースの計算タスクのインターネット末に存在する比較的強力なコンピュータ \(パーソナル コンピュータ\) の使用になります。  現代パーソナル コンピュータ \(PC\) に非常に迅速に、プロセッサ広大なメモリ、なし、電子メールおよび Web 参照のような十分に共通タスクの計算を行う場合に使用されていない、ハード ディスクがあります。  現代 PC は、アプリケーションのさまざまなタイプのクライアントとサーバー \(ピア両方\) として簡単に処理できます。  
  
-   コラボレーション ピアツーピアの下部組織は、Windows Vista、後でプラットフォームの近くのユーザーの接続サービスを活用する Microsoft Windows ピアツーピア下部組織の実装簡単です。  これは、直近のユーザーの接続のサービスを行っているサブネット ピア内の有効なアプリケーションに最もよくインターネットのエンドポイントまたはサービス取引連絡先もできますが、使用されます。  これは、取引相手のエンドポイント、状態やプレゼンスを決定する Live Messenger などの稼働中で認識のアプリケーションで使用される共通の連絡先マネージャを取り入れています。  
  
## コラボレーションのアプリケーション  
 一般的なピアツーピア コラボレーションのアプリケーションは次の手順で構成されます:  
  
-   ピアは、コラボレーション セッションをホストに関心を持ってピア ID が決まります  
  
-   セッションをホスト要求は、どうかに送信され、ホストのピアは、コラボレーション活動を管理することに合意します。  
  
-   ホストはセッションにサブネットの取引連絡先 \(要求を含む元\) 招待します。  
  
-   協力するすべてのピアはその連絡先を追加またはマネージャでホストがあります。  
  
-   ピア、ほとんどのホストのピアに、承認、または適時に指定するかどうかという案内の応答を送信します。  
  
-   協力するすべてのピアがホストのピアに所属します。  
  
-   ピアが自分の最初のコラボレーション活動を実行すると、ホスト ピア、連絡先のマネージャに、ピアを追加するかがあります。  また、償却する応答、およびユーザーが承認するかを決定するすべての招待状の応答を処理します。  これは破棄応答、またはそのほかの活動を場合があります担当者にという案内を実行します。  
  
-   この時点で、ホストのピアは、すべての招待されたピアのコラボレーションのセッションを開始したり、コラボレーションの下部組織とアプリケーションを登録します。  P2P のアプリケーションはピアツーピア コラボレーションの下部組織と、ゲーム掲示板、協議などの serverless プレゼンスのアプリケーションの通信を調整するに <xref:System.Net.PeerToPeer.Collaboration> の名前空間を使用します。  
  
## ピアツーピア ネットワーキングのセキュリティ  
 Active Directory ドメインで、ドメイン コントローラは Kerberos を使用して認証サービスを提供します。  serverless ピア環境では、ピアは独自の認証を指定する必要があります。  ピアツーピア ネットワーキングの場合、そのノードが各ピアの信頼済ルートの店舗のルート CA 証明書の条件を削除として機能します。  認証証明書は X.509 として書式設定された証明書を使用して署名して入力されます。  これらは公開キー\/秘密キーの組み合わせとデータ キーを使用して署名する証明書の生成、各ピアによって作成された証明書です。  株主証明書署名が認証にピア エンティティに関する情報を提供するために使用されます。  X.509 認証と同様に、ピアのネットワーキング認証は信頼された公開キーには追跡証明書のグループに基づきます。  
  
## 参照  
 <xref:System.Net.PeerToPeer.Collaboration>   
 [System.Net.PeerToPeer.Collaboration 名前空間について](../../../docs/framework/network-programming/about-the-system-net-peertopeer-collaboration-namespace.md)