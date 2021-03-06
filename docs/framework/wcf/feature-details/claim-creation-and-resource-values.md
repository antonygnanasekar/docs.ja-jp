---
title: "クレームの作成とリソース値 | Microsoft Docs"
ms.custom: ""
ms.date: "03/30/2017"
ms.prod: ".net-framework"
ms.reviewer: ""
ms.suite: ""
ms.technology: 
  - "dotnet-clr"
ms.tgt_pltfrm: ""
ms.topic: "article"
helpviewer_keywords: 
  - "クレーム [WCF], 作成とリソース値"
ms.assetid: 30431f76-cbe7-4bad-bad7-8e43e23a82d4
caps.latest.revision: 6
author: "Erikre"
ms.author: "erikre"
manager: "erikre"
caps.handback.revision: 6
---
# クレームの作成とリソース値
<xref:System.IdentityModel.Claims.Claim> クラスには、組み込みのクレームの種類のインスタンスを作成するためのメソッドが複数用意されています。これらのメソッドの中でも、次のものは指定されたリソースに対してセマンティックまたは形式のチェックを行いません。  
  
-   <xref:System.IdentityModel.Claims.Claim.CreateDnsClaim%2A>  
  
-   <xref:System.IdentityModel.Claims.Claim.CreateHashClaim%2A> \(長さまたはバイト配列のコンテンツは検査されません\)  
  
-   <xref:System.IdentityModel.Claims.Claim.CreateNameClaim%2A>  
  
-   <xref:System.IdentityModel.Claims.Claim.CreateSpnClaim%2A>  
  
-   <xref:System.IdentityModel.Claims.Claim.CreateThumbprintClaim%2A> \(長さまたはバイト配列のコンテンツは検査されません\)  
  
-   <xref:System.IdentityModel.Claims.Claim.CreateUpnClaim%2A>  
  
 上記のメソッドを使用する場合は、渡されるリソース値が正しい形式である、または正しい情報を含んでいること \(あるいは両方\) を確認するように注意してください。  
  
 次のメソッドは、特定の型を受け取ります。  
  
-   <xref:System.IdentityModel.Claims.Claim.CreateDenyOnlyWindowsSidClaim%2A>  
  
-   <xref:System.IdentityModel.Claims.Claim.CreateMailAddressClaim%2A>  
  
-   <xref:System.IdentityModel.Claims.Claim.CreateRsaClaim%2A>  
  
-   <xref:System.IdentityModel.Claims.Claim.CreateUriClaim%2A>  
  
-   <xref:System.IdentityModel.Claims.Claim.CreateWindowsSidClaim%2A>  
  
-   <xref:System.IdentityModel.Claims.Claim.CreateX500DistinguishedNameClaim%2A>  
  
## 参照  
 <xref:System.IdentityModel.Claims.Claim>   
 <xref:System.IdentityModel.Claims.ClaimSet>   
 [ID モデルを使用したクレームと承認の管理](../../../../docs/framework/wcf/feature-details/managing-claims-and-authorization-with-the-identity-model.md)