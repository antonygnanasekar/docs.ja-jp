---
title: "How to: Directly Execute SQL Commands | Microsoft Docs"
ms.custom: ""
ms.date: "03/30/2017"
ms.prod: ".net-framework-4.6"
ms.reviewer: ""
ms.suite: ""
ms.technology: 
  - "dotnet-ado"
ms.tgt_pltfrm: ""
ms.topic: "article"
ms.assetid: 04671bb0-40c0-4465-86e5-77986f454661
caps.latest.revision: 2
author: "JennieHubbard"
ms.author: "jhubbard"
manager: "jhubbard"
caps.handback.revision: 2
---
# How to: Directly Execute SQL Commands
<xref:System.Data.Linq.DataContext> 接続を使用すると仮定して、オブジェクトを返さない SQL コマンドを実行するために <xref:System.Data.Linq.DataContext.ExecuteCommand%2A> を使用できます。  
  
## 使用例  
 SQL Server で UnitPrice の値を 1.00 増やす方法の例を次に示します。  
  
 [!code-csharp[DLinqCommunicatingWithDatabase#3](../../../../../../samples/snippets/csharp/VS_Snippets_Data/DLinqCommunicatingWithDatabase/cs/Program.cs#3)]
 [!code-vb[DLinqCommunicatingWithDatabase#3](../../../../../../samples/snippets/visualbasic/VS_Snippets_Data/DLinqCommunicatingWithDatabase/vb/Module1.vb#3)]  
  
## 参照  
 [How to: Directly Execute SQL Queries](../../../../../../docs/framework/data/adonet/sql/linq/how-to-directly-execute-sql-queries.md)   
 [Communicating with the Database](../../../../../../docs/framework/data/adonet/sql/linq/communicating-with-the-database.md)