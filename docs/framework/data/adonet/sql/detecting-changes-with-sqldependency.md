---
title: "SqlDependency を使用した変更の検出 | Microsoft Docs"
ms.custom: ""
ms.date: "03/30/2017"
ms.prod: ".net-framework-4.6"
ms.reviewer: ""
ms.suite: ""
ms.technology: 
  - "dotnet-ado"
ms.tgt_pltfrm: ""
ms.topic: "article"
ms.assetid: e6a58316-f005-4477-92e1-45cc2eb8c5b4
caps.latest.revision: 3
author: "JennieHubbard"
ms.author: "jhubbard"
manager: "jhubbard"
caps.handback.revision: 3
---
# SqlDependency を使用した変更の検出
クエリ結果が最初に取得されたクエリ結果と異なることを検出するために、<xref:System.Data.SqlClient.SqlDependency> オブジェクトを <xref:System.Data.SqlClient.SqlCommand> に関連付けることができます。  さらに、`OnChange` イベントにデリゲートを割り当てることができます。このイベントは、関連付けられたコマンドの結果が変わったときに発生します。  コマンドを実行する前に、コマンドを <xref:System.Data.SqlClient.SqlDependency> に関連付ける必要があります。  また、<xref:System.Data.SqlClient.SqlDependency> の `HasChanges` プロパティを使用しても、データが最初に取得されて以降にクエリ結果が変化したかどうかを判別できます。  
  
## セキュリティの考慮事項  
 指定されたコマンドについて基になるデータが変更されたという通知を受け取るために、依存関係を持つインフラストラクチャでは <xref:System.Data.SqlClient.SqlDependency.Start%2A> が呼び出されると、<xref:System.Data.SqlClient.SqlConnection> が開かれることを利用します。  クライアントが `SqlDependency.Start` の呼び出しを開始できるかどうかは、<xref:System.Data.SqlClient.SqlClientPermission> を使用し、コード アクセス セキュリティ属性を利用することで制御します。  詳細については、「[クエリ通知の有効化](../../../../../docs/framework/data/adonet/sql/enabling-query-notifications.md)」および「[コード アクセス セキュリティと ADO.NET](../../../../../docs/framework/data/adonet/code-access-security.md)」を参照してください。  
  
### 例  
 次の手順に従って、依存関係を宣言し、コマンドを実行して、結果セットが変更された場合に通知を受け取ります。  
  
1.  サーバーへの `SqlDependency` 接続を開始します。  
  
2.  <xref:System.Data.SqlClient.SqlConnection> および <xref:System.Data.SqlClient.SqlCommand> オブジェクトを作成し、サーバーに接続して、Transact\-SQL ステートメントを定義します。  
  
3.  `SqlDependency` オブジェクトを新しく作成するか、既存のものを使用して、それを `SqlCommand` オブジェクトにバインドします。  内部的には、これによって <xref:System.Data.Sql.SqlNotificationRequest> オブジェクトが作成され、必要に応じてコマンド オブジェクトにバインドされます。  この通知要求には、この `SqlDependency` オブジェクトを一意に識別する内部 ID が含まれます。  クライアント リスナーがまだアクティブでない場合、それも起動されます。  
  
4.  `SqlDependency` オブジェクトの `OnChange` イベントにイベント ハンドラーを定期受信します。  
  
5.  `SqlCommand` オブジェクトの任意の `Execute` メソッドを使用して、コマンドを実行します。  このコマンドは通知オブジェクトにバインドされているため、サーバーは通知を生成する必要があることを認識し、キュー情報が依存関係を持つキューにポイントされます。  
  
6.  サーバーへの `SqlDependency` 接続を停止します。  
  
 その後、基になるデータをユーザーが変更すると、Microsoft SQL Server ではその変更に対する保留通知を検出し、通知を送信します。その通知は、`SqlDependency.Start` を呼び出すことで作成された基になる `SqlConnection` を利用して処理され、クライアントに転送されます。  クライアント リスナーは無効なメッセージを受け取ります。  クライアント リスナーでは、関連付けられている `SqlDependency` オブジェクトを特定し、`OnChange` イベントを発生させます。  
  
 次のコード フラグメントに、サンプル アプリケーションの作成に利用されるデザイン パターンを示します。  
  
```vb  
Sub Initialization()  
    ' Create a dependency connection.  
    SqlDependency.Start(connectionString, queueName)  
End Sub  
  
Sub SomeMethod()   
    ' Assume connection is an open SqlConnection.  
    ' Create a new SqlCommand object.  
    Using command As New SqlCommand( _  
      "SELECT ShipperID, CompanyName, Phone FROM dbo.Shippers", _  
      connection)  
  
        ' Create a dependency and associate it with the SqlCommand.  
        Dim dependency As New SqlDependency(command)  
        ' Maintain the refence in a class member.  
        ' Subscribe to the SqlDependency event.  
        AddHandler dependency.OnChange, AddressOf OnDependencyChange  
  
        ' Execute the command.  
        Using reader = command.ExecuteReader()  
            ' Process the DataReader.  
        End Using  
    End Using  
End Sub   
  
' Handler method  
Sub OnDependencyChange(ByVal sender As Object, _  
    ByVal e As SqlNotificationEventArgs)   
    ' Handle the event (for example, invalidate this cache entry).  
End Sub  
  
Sub Termination()  
    ' Release the dependency  
    SqlDependency.Stop(connectionString, queueName)  
End Sub  
```  
  
```csharp  
void Initialization()  
{  
    // Create a dependency connection.  
    SqlDependency.Start(connectionString, queueName);  
}  
  
void SomeMethod()  
{  
    // Assume connection is an open SqlConnection.  
  
    // Create a new SqlCommand object.  
    using (SqlCommand command=new SqlCommand(  
        "SELECT ShipperID, CompanyName, Phone FROM dbo.Shippers",   
        connection))  
    {  
  
        // Create a dependency and associate it with the SqlCommand.  
        SqlDependency dependency=new SqlDependency(command);  
        // Maintain the refence in a class member.  
  
        // Subscribe to the SqlDependency event.  
        dependency.OnChange+=new  
           OnChangeEventHandler(OnDependencyChange);  
  
        // Execute the command.  
        using (SqlDataReader reader = command.ExecuteReader())  
        {  
            // Process the DataReader.  
        }  
    }  
}  
  
// Handler method  
void OnDependencyChange(object sender,   
   SqlNotificationEventArgs e )  
{  
  // Handle the event (for example, invalidate this cache entry).  
}  
  
void Termination()  
{  
    // Release the dependency.  
    SqlDependency.Stop(connectionString, queueName);  
}  
```  
  
## 参照  
 [SQL Server のクエリ通知](../../../../../docs/framework/data/adonet/sql/query-notifications-in-sql-server.md)   
 [ADO.NET Managed Providers and DataSet Developer Center \(ADO.NET マネージ プロバイダーと DataSet デベロッパー センター\)](http://go.microsoft.com/fwlink/?LinkId=217917)