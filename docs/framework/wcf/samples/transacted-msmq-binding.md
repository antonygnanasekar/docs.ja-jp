---
title: "トランザクション MSMQ バインディング | Microsoft Docs"
ms.custom: ""
ms.date: "03/30/2017"
ms.prod: ".net-framework-4.6"
ms.reviewer: ""
ms.suite: ""
ms.technology: 
  - "dotnet-clr"
ms.tgt_pltfrm: ""
ms.topic: "article"
ms.assetid: 71f5cb8d-f1df-4e1e-b8a2-98e734a75c37
caps.latest.revision: 50
author: "Erikre"
ms.author: "erikre"
manager: "erikre"
caps.handback.revision: 50
---
# トランザクション MSMQ バインディング
このサンプルでは、メッセージ キュー \(MSMQ\) を使用して、トランザクション キューによる通信を実行する方法を示します。  
  
> [!NOTE]
>  このサンプルのセットアップ手順とビルド手順については、このトピックの最後を参照してください。  
  
 キュー通信では、クライアントはサービスとの通信にキューを使用します。厳密には、クライアントはメッセージをキューに送信します。サービスは、メッセージをキューから受信します。このため、キューを使用する通信では、サービスとクライアントは同時に実行されていなくてもかまいません。  
  
 メッセージの送受信にトランザクションが使用されている場合、実際には 2 つの別個のトランザクションが存在しています。クライアントがトランザクションのスコープ内でメッセージを送信する場合、トランザクションはクライアントおよびクライアント キュー マネージャにとってローカルとなります。サービスがトランザクションのスコープ内でメッセージを受信する場合、トランザクションはサービスおよび受信キュー マネージャにとってローカルとなります。クライアントとサービスは同じトランザクションに参加していません。むしろ、キューに対して操作 \(送信や受信など\) を実行する場合は異なるトランザクションを使用するので、注意してください。  
  
 このサンプルでは、クライアントはトランザクションのスコープ内からメッセージのバッチをサービスに送信します。キューに送信されたメッセージは、サービスによって定義されたトランザクション スコープ内のサービスによって受信されます。  
  
 サービス コントラクトは `IOrderProcessor` です。次のサンプル コードを参照してください。インターフェイスは、キューでの使用に適した一方向サービスを定義します。  
  
```  
[ServiceContract(Namespace="http://Microsoft.ServiceModel.Samples")]  
public interface IOrderProcessor  
{  
    [OperationContract(IsOneWay = true)]  
    void SubmitPurchaseOrder(PurchaseOrder po);  
}  
```  
  
 サービス動作は、`TransactionScopeRequired` が `true` に設定された操作動作を定義します。これにより、キューからのメッセージの取得に使用されるものと同じトランザクション スコープが、このメソッドによってアクセスされる任意のリソース マネージャにより使用されます。さらに、メソッドから例外がスローされた場合にメッセージがキューに返されることも保証されます。このサービス動作を設定しない場合、キューに置かれたチャネルはトランザクションを作成して、キューからのメッセージを読み取り、操作が失敗した場合には、ディスパッチによってメッセージが失われる前に、自動的にそのメッセージをコミットします。最もよく見られるシナリオは、キューからのメッセージの読み込みに使用するトランザクションにサービス操作をリストすることです。次のコードを参照してください。  
  
```  
  
 // This service class that implements the service contract.  
 // This added code writes output to the console window.  
 public class OrderProcessorService : IOrderProcessor  
 {  
     [OperationBehavior(TransactionScopeRequired = true, TransactionAutoComplete = true)]  
     public void SubmitPurchaseOrder(PurchaseOrder po)  
     {  
         Orders.Add(po);  
         Console.WriteLine("Processing {0} ", po);  
     }  
  …  
}  
  
```  
  
 サービスは自己ホスト型です。MSMQ トランスポートを使用する場合、使用するキューをあらかじめ作成しておく必要があります。手動で作成することもコードで作成することもできます。このサンプルでは、キューの存在を確認して、存在しない場合は作成するためのコードがサービスに含まれています。キュー名は構成ファイルから読み込まれます。ベース アドレスは、[ServiceModel メタデータ ユーティリティ ツール \(Svcutil.exe\)](../../../../docs/framework/wcf/servicemodel-metadata-utility-tool-svcutil-exe.md) で使用され、サービスにプロキシが生成されます。  
  
```  
// Host the service within this EXE console application.  
public static void Main()  
{  
    // Get the MSMQ queue name from appSettings in configuration.  
    string queueName = ConfigurationManager.AppSettings["queueName"];  
  
    // Create the transacted MSMQ queue if necessary.  
    if (!MessageQueue.Exists(queueName))  
        MessageQueue.Create(queueName, true);  
  
    // Create a ServiceHost for the OrderProcessorService type.  
    using (ServiceHost serviceHost = new ServiceHost(typeof(OrderProcessorService)))  
    {  
        // Open the ServiceHost to create listeners and start listening for messages.  
        serviceHost.Open();  
  
        // The service can now be accessed.  
        Console.WriteLine("The service is ready.");  
        Console.WriteLine("Press <ENTER> to terminate service.");  
        Console.WriteLine();  
        Console.ReadLine();  
  
        // Close the ServiceHost to shut down the service.  
        serviceHost.Close();  
    }  
}  
  
```  
  
 MSMQ キュー名は、構成ファイルの appSettings セクションに指定されます。次のサンプル構成を参照してください。  
  
```  
<appSettings>  
    <add key="queueName" value=".\private$\ServiceModelSamplesTransacted" />  
</appSettings>  
```  
  
> [!NOTE]
>  <xref:System.Messaging> を使用してキューを作成する場合、キュー名では、ローカル コンピューターを表すのにドット \(.\) を使用し、パスの区切りにはバックスラッシュを使用します。[!INCLUDE[indigo1](../../../../includes/indigo1-md.md)] エンドポイントでは net.msmq スキームが含まれるキュー アドレスを使用します。ローカル コンピューターを表すのに "localhost" を使用し、パスの区切りにはスラッシュを使用します。  
  
 クライアントはトランザクション スコープを作成します。キューとの通信はトランザクションのスコープ内で実行され、すべてのメッセージがキューに送信されるか、またはメッセージがキューにまったく送信されないかを示す、atomic 単位として扱われます。トランザクションは、トランザクション スコープで <xref:System.Transactions.TransactionScope.Complete%2A> を呼び出すことでコミットされます。  
  
```  
// Create a client.  
OrderProcessorClient client = new OrderProcessorClient();  
  
// Create the purchase order.  
PurchaseOrder po = new PurchaseOrder();  
po.CustomerId = "somecustomer.com";  
po.PONumber = Guid.NewGuid().ToString();  
  
PurchaseOrderLineItem lineItem1 = new PurchaseOrderLineItem();  
lineItem1.ProductId = "Blue Widget";  
lineItem1.Quantity = 54;  
lineItem1.UnitCost = 29.99F;  
  
PurchaseOrderLineItem lineItem2 = new PurchaseOrderLineItem();  
lineItem2.ProductId = "Red Widget";  
lineItem2.Quantity = 890;  
lineItem2.UnitCost = 45.89F;  
  
po.orderLineItems = new PurchaseOrderLineItem[2];  
po.orderLineItems[0] = lineItem1;  
po.orderLineItems[1] = lineItem2;  
  
// Create a transaction scope.  
using (TransactionScope scope = new TransactionScope(TransactionScopeOption.Required))  
{  
    // Make a queued call to submit the purchase order.  
    client.SubmitPurchaseOrder(po);  
    // Complete the transaction.  
    scope.Complete();  
}  
  
// Closing the client gracefully closes the connection and cleans up resources.  
client.Close();  
  
Console.WriteLine();  
Console.WriteLine("Press <ENTER> to terminate client.");  
Console.ReadLine();  
```  
  
 トランザクションが動作していることを確認するには、次のサンプル コードで示すようにトランザクション スコープをコメント化してクライアントを変更し、ソリューションを再ビルドしてクライアントを実行します。  
  
```  
//scope.Complete();  
```  
  
 トランザクションは完了していないので、メッセージはキューに送信されません。  
  
 サンプルを実行すると、クライアントとサービスのアクティビティがサービスとクライアントの両方のコンソール ウィンドウに表示されます。サービスがクライアントからメッセージを受信するようすがわかります。どちらかのコンソールで Enter キーを押すと、サービスとクライアントがどちらもシャットダウンされます。キューが使用されているので、クライアントとサービスが同時に実行されている必要はありません。クライアントを実行してシャットダウンした後にサービスを起動しても、サービスはメッセージを受信します。  
  
```  
The service is ready.  
Press <ENTER> to terminate service.  
  
Processing Purchase Order: 7b31ce51-ae7c-4def-9b8b-617e4288eafd  
        Customer: somecustomer.com  
        OrderDetails  
                Order LineItem: 54 of Blue Widget @unit price: $29.99  
                Order LineItem: 890 of Red Widget @unit price: $45.89  
        Total cost of this order: $42461.56  
        Order status: Pending  
  
```  
  
### サンプルを設定、ビルド、および実行するには  
  
1.  「[Windows Communication Foundation サンプルの 1 回限りのセットアップの手順](../../../../docs/framework/wcf/samples/one-time-setup-procedure-for-the-wcf-samples.md)」が実行済みであることを確認します。  
  
2.  サービスを最初に実行すると、サービスはキューが存在するかどうかを確認します。キューが存在しない場合、サービスによってキューが作成されます。最初にサービスを実行してキューを作成することも、MSMQ キュー マネージャーでキューを作成することもできます。Windows 2008 でキューを作成するには、次の手順に従います。  
  
    1.  [!INCLUDE[vs_current_long](../../../../includes/vs-current-long-md.md)] でサーバー マネージャーを開きます。  
  
    2.  **\[機能\]** タブを展開します。  
  
    3.  **\[プライベート メッセージ キュー\]** を右クリックし、**\[新規作成\]**、**\[専用キュー\]** の順にクリックします。  
  
    4.  **\[トランザクション\]** ボックスをオンにします。  
  
    5.  新しいキューの名前として、「`ServiceModelSamplesTransacted`」と入力します。  
  
3.  ソリューションの C\# 版または Visual Basic .NET 版をビルドするには、「[Windows Communication Foundation サンプルのビルド](../../../../docs/framework/wcf/samples/building-the-samples.md)」の手順に従います。  
  
4.  サンプルを単一コンピューター構成または複数コンピューター構成で実行するには、「[Windows Communication Foundation サンプルの実行](../../../../docs/framework/wcf/samples/running-the-samples.md)」の手順に従います。  
  
 <xref:System.ServiceModel.NetMsmqBinding> を使用する場合の既定では、トランスポート セキュリティが有効です。MSMQ トランスポート セキュリティには、<xref:System.ServiceModel.MsmqTransportSecurity.MsmqAuthenticationMode%2A> および <xref:System.ServiceModel.MsmqTransportSecurity.MsmqProtectionLevel%2A> という 2 つの関連プロパティがあります。既定では、認証モードは `Windows` に、保護レベルは `Sign` に、それぞれ設定されます。MSMQ で認証機能と署名機能を実現するには、MSMQ をドメインに含め、MSMQ の Active Directory 統合オプションをインストールする必要があります。この条件を満たしていないコンピューターでこのサンプルを実行すると、エラーになります。  
  
### ワークグループに属しているコンピューターまたは Active Directory 統合のないコンピューターでこのサンプルを実行するには  
  
1.  ドメインに属していないコンピューター、または Active Directory 統合がインストールされていないコンピューターを使用する場合は、トランスポート セキュリティをオフにします。オフにするには、認証モードと保護レベルを `None` にします。この構成コードの例を次に示します。  
  
    ```  
    <system.serviceModel>  
      <services>  
        <service name="Microsoft.ServiceModel.Samples.OrderProcessorService"  
                 behaviorConfiguration="OrderProcessorServiceBehavior">  
          <host>  
            <baseAddresses>  
              <add baseAddress="http://localhost:8000/ServiceModelSamples/service"/>  
            </baseAddresses>  
          </host>  
          <!-- Define NetMsmqEndpoint. -->  
          <endpoint  
              address="net.msmq://localhost/private/ServiceModelSamplesTransacted"  
              binding="netMsmqBinding"  
              bindingConfiguration="Binding1"  
           contract="Microsoft.ServiceModel.Samples.IOrderProcessor" />  
          <!-- The mex endpoint is explosed at http://localhost:8000/ServiceModelSamples/service/mex. -->  
          <endpoint address="mex"  
                    binding="mexHttpBinding"  
                    contract="IMetadataExchange" />  
        </service>  
      </services>  
  
      <bindings>  
        <netMsmqBinding>  
          <binding name="Binding1">  
            <security mode="None" />  
          </binding>  
        </netMsmqBinding>  
      </bindings>  
  
        <behaviors>  
          <serviceBehaviors>  
            <behavior name="OrderProcessorServiceBehavior">  
              <serviceMetadata httpGetEnabled="True"/>  
            </behavior>  
          </serviceBehaviors>  
        </behaviors>  
  
      </system.serviceModel>  
  
    ```  
  
2.  サーバーとクライアントの両方の構成を変更したことを確認してから、サンプルを実行します。  
  
    > [!NOTE]
    >  `security` `mode` を `None` に設定することは、<xref:System.ServiceModel.MsmqTransportSecurity.MsmqAuthenticationMode%2A>、<xref:System.ServiceModel.MsmqTransportSecurity.MsmqProtectionLevel%2A>、および `Message` のセキュリティを `None` に設定することと同じです。  
  
> [!IMPORTANT]
>  サンプルは、既にコンピューターにインストールされている場合があります。続行する前に、次の \(既定の\) ディレクトリを確認してください。  
>   
>  `<InstallDrive>:\WF_WCF_Samples`  
>   
>  このディレクトリが存在しない場合は、「[.NET Framework 4 向けの Windows Communication Foundation \(WCF\) および Windows Workflow Foundation \(WF\) のサンプル](http://go.microsoft.com/fwlink/?LinkId=150780)」にアクセスして、[!INCLUDE[indigo1](../../../../includes/indigo1-md.md)] および [!INCLUDE[wf1](../../../../includes/wf1-md.md)] のサンプルをすべてダウンロードしてください。このサンプルは、次のディレクトリに格納されます。  
>   
>  `<InstallDrive>:\WF_WCF_Samples\WCF\Basic\Binding\Net\MSMQ\Transacted`  
  
## 参照