---
title: "権限の昇格 | Microsoft Docs"
ms.custom: ""
ms.date: "03/30/2017"
ms.prod: ".net-framework-4.6"
ms.reviewer: ""
ms.suite: ""
ms.technology: 
  - "dotnet-clr"
ms.tgt_pltfrm: ""
ms.topic: "article"
helpviewer_keywords: 
  - "権限の昇格 [WCF]"
  - "セキュリティ [WCF], 権限の昇格"
ms.assetid: 146e1c66-2a76-4ed3-98a5-fd77851a06d9
caps.latest.revision: 16
author: "Erikre"
ms.author: "erikre"
manager: "erikre"
caps.handback.revision: 16
---
# 権限の昇格
*権限の昇格*は、本来付与されたものよりも高い承認アクセス許可を攻撃者に与えた結果として発生します。たとえば、"読み取り専用" アクセス許可の権限セットを持つ攻撃者が、何らかの方法で権限セットを "読み取り\/書き込み" アクセス許可を含むものに昇格させます。  
  
## SAML トークンのクレームには信頼された STS による署名が必要  
 SAML \(Security Assertions Markup Language\) トークンは、発行済みトークンの既定の型である汎用 XML トークンです。SAML トークンは、通常の交換においてエンド Web サービスから信頼されたセキュリティ トークン サービス \(STS\) によって作成できます。SAML トークンには、ステートメント内にクレームが格納されます。攻撃者は、有効なトークンからクレームをコピーして新しい SAML トークンを作成し、別の発行者による署名を行うことがあります。この目的は、サーバーが発行者を検証するかどうかを確認し、検証しない場合に、この脆弱性を利用して信頼された STS が意図したものよりも高い権限を与える SAML トークンを作成することです。  
  
 <xref:System.IdentityModel.Tokens.SamlAssertion> クラスは、SAML トークンに含まれるデジタル署名を検証します。<xref:System.ServiceModel.Security.IssuedTokenServiceCredential> クラスの <xref:System.ServiceModel.Security.IssuedTokenServiceCredential.CertificateValidationMode%2A> が <xref:System.ServiceModel.Security.X509CertificateValidationMode> に設定されている場合、既定の <xref:System.IdentityModel.Selectors.SamlSecurityTokenAuthenticator> は、SAML トークンが有効な X.509 証明書によって署名されていることを要求します。SAML トークンの発行者を信頼できるかどうか判断するには、`ChainTrust` モードだけでは不十分です。より詳細な信頼モデルを必要とするサービスは、承認ポリシーと強制ポリシーを使用して、発行済みトークン認証によって生成されたクレーム セットの発行者をチェックするか、<xref:System.ServiceModel.Security.IssuedTokenServiceCredential> の X.509 検証設定を使用して、許可する署名証明書のセットを制限できます。[!INCLUDE[crdefault](../../../../includes/crdefault-md.md)]「[ID モデルを使用したクレームと承認の管理](../../../../docs/framework/wcf/feature-details/managing-claims-and-authorization-with-the-identity-model.md)」および「[フェデレーションと発行済みトークン](../../../../docs/framework/wcf/feature-details/federation-and-issued-tokens.md)」を参照してください。  
  
## セキュリティ コンテキストを使用しない ID の切り替え  
 次の内容は、[!INCLUDE[vstecwinfx](../../../../includes/vstecwinfx-md.md)] にのみ適用されます。  
  
 クライアントとサーバー間の接続が確立すると、[!INCLUDE[indigo2](../../../../includes/indigo2-md.md)] クライアントを開いた後に次のすべての条件に該当する場合を除き、クライアントの ID は変更されません。  
  
-   セキュリティ コンテキストを確立するプロシージャ \(トランスポート セキュリティ セッションまたはメッセージ セキュリティ セッションを使用\) が無効になっている \(メッセージ セキュリティの場合は、<xref:System.ServiceModel.NonDualMessageSecurityOverHttp.EstablishSecurityContext%2A> プロパティが `false`  に設定されている。トランスポート セキュリティの場合は、セキュリティ セッションを確立できないトランスポート \(HTTPS など\) が使用されている\)。  
  
-   Windows 認証を使用している。  
  
-   資格情報を明示的に設定していない。  
  
-   偽装されたセキュリティ コンテキストでサービスを呼び出している。  
  
 これらの条件に該当する場合、[!INCLUDE[indigo2](../../../../includes/indigo2-md.md)] クライアントを開いた後で、サービスに対するクライアントの認証に使用する ID が変更されることがあります \(偽装された ID ではなく、プロセス ID になります\)。この状況が発生するのは、サービスに対するクライアントの認証に使用する Windows 資格情報がすべてのメッセージと共に送信され、認証に使用する資格情報が現在のスレッドの Windows ID から取得されるためです。\(たとえば、別の呼び出し元を偽装することによって\) 現在のスレッドの Windows ID が変更された場合、メッセージに添付され、サービスに対するクライアントの認証に使用する資格情報も変更される可能性があります。  
  
 偽装と共に Windows 認証を使用する場合に動作を確定する必要があるときは、Windows 資格情報を明示的に設定するか、サービスでセキュリティ コンテキストを確立する必要があります。これを行うには、メッセージ セキュリティ セッションまたはトランスポート セキュリティ セッションを使用します。たとえば、net.tcp トランスポートは、トランスポート セキュリティ セッションを提供します。また、サービスの呼び出し時に、クライアント操作の同期バージョンだけを使用する必要があります。メッセージ セキュリティ コンテキストを確立する場合は、構成済みセッションの更新時間よりも長い時間、サービスへの接続を開いたままにしないようにしてください。セッション更新プロセスの間にも ID が変更される可能性があるためです。  
  
### 資格情報のキャプチャ  
 次の内容は、[!INCLUDE[netfx35_long](../../../../includes/netfx35-long-md.md)] 以降のバージョンに適用されます。  
  
 クライアントまたはサービスが使用する資格情報は、現在のコンテキストのスレッドに基づきます。資格情報は、クライアントまたはサービスの `Open` メソッド \(または、非同期呼び出しの場合は `BeginOpen`\) が呼び出された場合に取得されます。<xref:System.ServiceModel.ServiceHost> クラスおよび <xref:System.ServiceModel.ClientBase%601> クラスのいずれの場合も、`Open` メソッドおよび `BeginOpen` メソッドは、<xref:System.ServiceModel.Channels.CommunicationObject> クラスの <xref:System.ServiceModel.Channels.CommunicationObject.Open%2A> メソッドおよび <xref:System.ServiceModel.Channels.CommunicationObject.BeginOpen%2A> メソッドから継承されます。  
  
> [!NOTE]
>  `BeginOpen` メソッドを使用する場合、キャプチャされた資格情報は、このメソッドを呼び出したプロセスの資格情報であることが保証されません。  
  
## トークン キャッシュによる以前のデータを使用した再生の許可  
 [!INCLUDE[indigo2](../../../../includes/indigo2-md.md)] は、ローカル セキュリティ機関 \(LSA\) の `LogonUser` 関数を使用して、ユーザー名とパスワードによってユーザーを認証します。ログオン関数はコストの高い操作であるため、[!INCLUDE[indigo2](../../../../includes/indigo2-md.md)] では、パフォーマンスを向上させるために、認証済みユーザーを表すトークンをキャッシュできます。キャッシュ機構は、それ以降に使用できるように `LogonUser` の結果を保存します。この機構は、既定で無効になっています。有効にするには、<xref:System.ServiceModel.Security.UserNamePasswordServiceCredential.CacheLogonTokens%2A> プロパティを `true` に設定するか、[\<userNameAuthentication\>](../../../../docs/framework/configure-apps/file-schema/wcf/usernameauthentication.md) の `cacheLogonTokens` 属性を使用します。  
  
 <xref:System.ServiceModel.Security.UserNamePasswordServiceCredential.CachedLogonTokenLifetime%2A> プロパティを <xref:System.TimeSpan> に設定するか、`userNameAuthentication` 要素の `cachedLogonTokenLifetime` 属性を使用することで、キャッシュされたトークンの有効期間 \(TTL\) を設定できます。既定値は 15 分です。Windows からユーザー アカウントが削除された場合や、パスワードが変更されている場合でも、トークンがキャッシュされている間は、同じユーザー名とパスワードを指定すると、どのクライアントもこのトークンを使用できます。TTL が終了し、トークンがキャッシュから削除されるまで、[!INCLUDE[indigo2](../../../../includes/indigo2-md.md)] は悪意のあるユーザーである可能性があっても、ユーザー認証を許可します。  
  
 これをできるだけ防ぐには、`cachedLogonTokenLifetime` の設定値をユーザーが必要とする最低の期間に限定して攻撃領域を減らします。  
  
## 発行済みトークンの承認 : 大きな値にリセットされた有効期限  
 一定の条件下で、<xref:System.IdentityModel.Policy.AuthorizationContext> の <xref:System.IdentityModel.Policy.AuthorizationContext.ExpirationTime%2A> プロパティが予期しない大きな値 \(<xref:System.DateTime.MaxValue> フィールド値から 1 日引いた値 \(December 20, 9999\)\) に設定される場合があります。  
  
 これは、<xref:System.ServiceModel.WSFederationHttpBinding>、およびクライアント資格情報の種類が発行済みトークンであるシステム提供のバインディングを使用している場合に発生します。  
  
 また、以下のメソッドのいずれかを使用して、カスタム バインディングを作成した場合にも発生します。  
  
-   <xref:System.ServiceModel.Channels.SecurityBindingElement.CreateIssuedTokenBindingElement%2A>  
  
-   <xref:System.ServiceModel.Channels.SecurityBindingElement.CreateIssuedTokenForCertificateBindingElement%2A>  
  
-   <xref:System.ServiceModel.Channels.SecurityBindingElement.CreateIssuedTokenForSslBindingElement%2A>  
  
-   <xref:System.ServiceModel.Channels.SecurityBindingElement.CreateIssuedTokenOverTransportBindingElement%2A>  
  
 これをできるだけ防ぐには、承認ポリシーによって各承認ポリシーのアクションと有効期限をチェックする必要があります。  
  
## クライアントで予定していたものと異なる証明書をサービスで使用する場合  
 一定の条件下で、クライアントが X.509 証明書を使用してメッセージにデジタル署名したときに、予定していたものと異なる証明書をサービスが取得する場合があります。  
  
 これは、次のような状況で発生する可能性があります。  
  
-   クライアントが X.509 証明書を使用してメッセージにデジタル署名したときに、その X.509 証明書をメッセージに添付するのではなく、サブジェクト キー識別子を使用して証明書を参照しているだけの場合。  
  
-   サービスのコンピューターに同じ公開キーを持つ複数の証明書が格納されており、それらの証明書に含まれる情報が異なる場合。  
  
-   サービスがサブジェクト キー識別子と一致する証明書を取得したが、クライアントが使用する予定だったものではない場合。[!INCLUDE[indigo2](../../../../includes/indigo2-md.md)] でメッセージを取得し署名を検証すると、[!INCLUDE[indigo2](../../../../includes/indigo2-md.md)] は予定されていたものと異なる X.509 証明書の情報をクレーム セットに対応付けます。このクレーム セットは、クライアントが必要とした内容とは異なり、昇格されている可能性があります。  
  
 これをできるだけ防ぐには、X.509 証明書を別の方法 \(<xref:System.ServiceModel.Security.Tokens.X509KeyIdentifierClauseType> の使用など\) で参照します。  
  
## 参照  
 [セキュリティの考慮事項](../../../../docs/framework/wcf/feature-details/security-considerations-in-wcf.md)   
 [情報の漏えい](../../../../docs/framework/wcf/feature-details/information-disclosure.md)   
 [サービス拒否](../../../../docs/framework/wcf/feature-details/denial-of-service.md)   
 [リプレイ攻撃](../../../../docs/framework/wcf/feature-details/replay-attacks.md)   
 [改変](../../../../docs/framework/wcf/feature-details/tampering.md)   
 [サポートされていないシナリオ](../../../../docs/framework/wcf/feature-details/unsupported-scenarios.md)