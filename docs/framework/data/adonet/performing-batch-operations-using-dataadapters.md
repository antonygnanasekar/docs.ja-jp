---
title: "DataAdapter によるバッチ操作の実行 | Microsoft Docs"
ms.custom: ""
ms.date: "03/30/2017"
ms.prod: ".net-framework"
ms.reviewer: ""
ms.suite: ""
ms.technology: 
  - "dotnet-ado"
ms.tgt_pltfrm: ""
ms.topic: "article"
ms.assetid: e72ed5af-b24f-486c-8429-c8fd2208f844
caps.latest.revision: 3
author: "JennieHubbard"
ms.author: "jhubbard"
manager: "jhubbard"
caps.handback.revision: 3
---
# DataAdapter によるバッチ操作の実行
ADO.NET のバッチ サポートを利用すると、<xref:System.Data.Common.DataAdapter> は、<xref:System.Data.DataSet> または <xref:System.Data.DataTable> から INSERT、UPDATE、および DELETE の各操作を 1 操作ずつサーバーに送信するのではなく、グループ化してサーバーに送信できます。  こうすることで、サーバーへのラウンド トリップの回数が減少し、大幅なパフォーマンスの向上が期待できます。  バッチ更新は、SQL Server \(<xref:System.Data.SqlClient>\) および Oracle \(<xref:System.Data.OracleClient>\) の .NET データ プロバイダーでサポートされています。  
  
 以前のバージョンの ADO.NET では、<xref:System.Data.DataSet> に格納されている変更内容をデータベースに反映する場合、`DataAdapter` の `Update` メソッドを実行して、1 行ずつデータベースを更新していました。  このメソッドは、指定された <xref:System.Data.DataTable> 内の行を反復処理すると、各 <xref:System.Data.DataRow> を調べ、行が変更されたことを確認します。  行が変更されている場合、その行の <xref:System.Data.DataRow.RowState%2A> プロパティの値に基づいて、適切な `UpdateCommand`、`InsertCommand`、または `DeleteCommand` のいずれかを呼び出します。  各行の更新では、データベースへのネットワーク ラウンドトリップが発生します。  
  
 ADO.NET 2.0 以降では、<xref:System.Data.Common.DbDataAdapter> は <xref:System.Data.Common.DbDataAdapter.UpdateBatchSize%2A> プロパティを公開します。  `UpdateBatchSize` を正の整数値に設定すると、データベースの更新が指定されたサイズのバッチとして送信されます。  たとえば、`UpdateBatchSize` を 10 に設定すると、10 個の個別のステートメントがグループ化され、単一のバッチとして送信されます。  `UpdateBatchSize` を 0 に設定すると、<xref:System.Data.Common.DataAdapter> は、サーバーが処理できる最大のバッチ サイズを使用します。  1 に設定すると、バッチ更新が無効になり、1 行ずつ送信されます。  
  
 サイズの大きいバッチを実行すると、パフォーマンスが低下する可能性があります。  そのため、アプリケーションを実装する前に、バッチの最適なサイズ設定をテストする必要があります。  
  
## UpdateBatchSize プロパティの使用  
 バッチ更新を有効にする場合、DataAdapter の `UpdateCommand`、`InsertCommand` および `DeleteCommand` の <xref:System.Data.IDbCommand.UpdatedRowSource%2A> プロパティ値を、<xref:System.Data.UpdateRowSource> または <xref:System.Data.UpdateRowSource> に設定する必要があります。  バッチ更新を実行する際、<xref:System.Data.UpdateRowSource> または <xref:System.Data.UpdateRowSource> のコマンドの <xref:System.Data.IDbCommand.UpdatedRowSource%2A> プロパティ値は、無効になります。  
  
 `UpdateBatchSize` プロパティを使用するプロシージャを次に示します。  このプロシージャは、2 つの引数を取ります。1 つは、**Production.ProductCategory** テーブル内の **ProductCategoryID** フィールドおよび **Name** フィールドを表す列を持つ <xref:System.Data.DataSet> オブジェクトで、もう 1 つは、バッチ サイズ \(バッチ ファイル内の行数\) を表す整数です。  このコードにより、新しい <xref:System.Data.SqlClient.SqlDataAdapter> オブジェクトが作成され、その <xref:System.Data.SqlClient.SqlDataAdapter.UpdateCommand%2A> プロパティ、<xref:System.Data.SqlClient.SqlDataAdapter.InsertCommand%2A> プロパティ、および <xref:System.Data.SqlClient.SqlDataAdapter.DeleteCommand%2A> プロパティが設定されます。  このコードは、<xref:System.Data.DataSet> オブジェクトによって行が変更済みになっていることを前提としています。  このオブジェクトは、`UpdateBatchSize` プロパティを設定し、更新を実行します。  
  
```vb  
Public Sub BatchUpdate( _  
  ByVal dataTable As DataTable, ByVal batchSize As Int32)  
    ' Assumes GetConnectionString() returns a valid connection string.  
    Dim connectionString As String = GetConnectionString()  
  
    ' Connect to the AdventureWorks database.  
    Using connection As New SqlConnection(connectionString)  
        ' Create a SqlDataAdapter.  
        Dim adapter As New SqlDataAdapter()  
  
        'Set the UPDATE command and parameters.  
        adapter.UpdateCommand = New SqlCommand( _  
          "UPDATE Production.ProductCategory SET " _  
          & "Name=@Name WHERE ProductCategoryID=@ProdCatID;", _  
          connection)  
        adapter.UpdateCommand.Parameters.Add("@Name", _  
          SqlDbType.NVarChar, 50, "Name")  
        adapter.UpdateCommand.Parameters.Add("@ProdCatID",  _  
          SqlDbType.Int, 4, " ProductCategoryID ")  
        adapter.UpdateCommand.UpdatedRowSource = _  
          UpdateRowSource.None  
  
        'Set the INSERT command and parameter.  
        adapter.InsertCommand = New SqlCommand( _  
          "INSERT INTO Production.ProductCategory (Name) VALUES (@Name);", _  
  connection)  
        adapter.InsertCommand.Parameters.Add("@Name", _  
          SqlDbType.NVarChar, 50, "Name")  
        adapter.InsertCommand.UpdatedRowSource = _  
          UpdateRowSource.None  
  
        'Set the DELETE command and parameter.  
        adapter.DeleteCommand = New SqlCommand( _  
          "DELETE FROM Production.ProductCategory " _  
          & "WHERE ProductCategoryID=@ProdCatID;", connection)  
        adapter.DeleteCommand.Parameters.Add("@ProdCatID", _  
           SqlDbType.Int, 4, " ProductCategoryID ")  
        adapter.DeleteCommand.UpdatedRowSource = UpdateRowSource.None  
  
        ' Set the batch size.  
        adapter.UpdateBatchSize = batchSize  
  
        ' Execute the update.  
        adapter.Update(dataTable)  
    End Using  
End Sub  
```  
  
```csharp  
public static void BatchUpdate(DataTable dataTable,Int32 batchSize)  
{  
    // Assumes GetConnectionString() returns a valid connection string.  
    string connectionString = GetConnectionString();  
  
    // Connect to the AdventureWorks database.  
    using (SqlConnection connection = new   
      SqlConnection(connectionString))  
    {  
  
        // Create a SqlDataAdapter.  
        SqlDataAdapter adapter = new SqlDataAdapter();  
  
        // Set the UPDATE command and parameters.  
        adapter.UpdateCommand = new SqlCommand(  
            "UPDATE Production.ProductCategory SET "  
            + "Name=@Name WHERE ProductCategoryID=@ProdCatID;",   
            connection);  
        adapter.UpdateCommand.Parameters.Add("@Name",   
           SqlDbType.NVarChar, 50, "Name");  
        adapter.UpdateCommand.Parameters.Add("@ProdCatID",   
           SqlDbType.Int, 4, "ProductCategoryID");  
         adapter.UpdateCommand.UpdatedRowSource = UpdateRowSource.None;  
  
        // Set the INSERT command and parameter.  
        adapter.InsertCommand = new SqlCommand(  
            "INSERT INTO Production.ProductCategory (Name) VALUES (@Name);",   
            connection);  
        adapter.InsertCommand.Parameters.Add("@Name",   
          SqlDbType.NVarChar, 50, "Name");  
        adapter.InsertCommand.UpdatedRowSource = UpdateRowSource.None;  
  
        // Set the DELETE command and parameter.  
        adapter.DeleteCommand = new SqlCommand(  
            "DELETE FROM Production.ProductCategory "  
            + "WHERE ProductCategoryID=@ProdCatID;", connection);  
        adapter.DeleteCommand.Parameters.Add("@ProdCatID",   
          SqlDbType.Int, 4, "ProductCategoryID");  
        adapter.DeleteCommand.UpdatedRowSource = UpdateRowSource.None;  
  
        // Set the batch size.  
        adapter.UpdateBatchSize = batchSize;  
  
        // Execute the update.  
        adapter.Update(dataTable);  
    }  
}  
```  
  
## バッチ更新に関連するイベントおよびエラーの処理  
 **DataAdapter** には **RowUpdating** と **RowUpdated** の 2 つの更新に関するイベントがあります。  以前のバージョンの ADO.NET では、バッチ処理が無効になっていると、処理された行ごとにこれらのイベントがそれぞれ生成されます。  更新が行われる前に **RowUpdating** が生成され、データベースの更新が完了した後に **RowUpdated** が生成されます。  
  
### バッチ更新とイベント動作の違い  
 バッチ処理が有効になっている場合、1 度のデータベース操作で複数の行が更新されます。  このため、`RowUpdating` イベントは処理された各行ごとに発生しますが、`RowUpdated` イベントは各バッチ処理につき、1 つしか発生しません。  バッチ処理が無効になっている場合、1 対 1 のインターリーブを伴う 2 つのイベントが発生します。つまり、1 つの行に対し、`RowUpdating` イベントが 1 つ、`RowUpdated` イベントが 1 つ発生します。すべての行が処理されるまで、次の行に対して `RowUpdating` イベントが 1 つ、`RowUpdated` イベントが 1 つ発生します。  
  
### 更新された行へのアクセス  
 バッチ処理が無効になっている場合、更新された行には、<xref:System.Data.Common.RowUpdatedEventArgs> クラスの <xref:System.Data.Common.RowUpdatedEventArgs.Row%2A> プロパティを使用してアクセスできます。  
  
 バッチ処理が有効になっている場合、複数の行に対して 1 つの `RowUpdated` イベントが生成されます。  つまり、各行の `Row` プロパティ値は null ですが、  `RowUpdating` イベントは各行に対して生成されます。  処理された行にアクセスするには、<xref:System.Data.Common.RowUpdatedEventArgs> クラスの <xref:System.Data.Common.RowUpdatedEventArgs.CopyToRows%2A> メソッドにより、配列に行への参照をコピーします。  処理される行がない場合、`CopyToRows` が <xref:System.ArgumentNullException> をスローします。  <xref:System.Data.Common.RowUpdatedEventArgs.CopyToRows%2A> メソッドを呼び出す前に、<xref:System.Data.Common.RowUpdatedEventArgs.RowCount%2A> プロパティを使用して、処理されている行数を取得できます。  
  
### データ エラーの処理  
 ステートメントを個別に実行した場合とバッチ処理を実行した場合では効果は同じです。  ステートメントはバッチに追加された順序と同じ順序で実行されます。  エラーは、バッチ モードでも、バッチ モードが無効な場合と同様に処理されます。  つまり、各行が個別に処理されます。  データベース内で正常に処理された行だけが、<xref:System.Data.DataTable> 内の対応する <xref:System.Data.DataRow> で更新されます。  
  
 バッチ処理の実行に対してサポートされる SQL 構成は、データ プロバイダーとバックエンド データベースが決定します。  サポートされていないステートメントが実行時に送信されると、例外がスローされる場合があります。  
  
## 参照  
 [DataAdapter と DataReader](../../../../docs/framework/data/adonet/dataadapters-and-datareaders.md)   
 [DataAdapter によるデータ ソースの更新](../../../../docs/framework/data/adonet/updating-data-sources-with-dataadapters.md)   
 [DataAdapter のイベント処理](../../../../docs/framework/data/adonet/handling-dataadapter-events.md)   
 [ADO.NET Managed Providers and DataSet Developer Center \(ADO.NET マネージ プロバイダーと DataSet デベロッパー センター\)](http://go.microsoft.com/fwlink/?LinkId=217917)