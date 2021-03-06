---
title: "操作フォーマッタと操作セレクター | Microsoft Docs"
ms.custom: ""
ms.date: "03/30/2017"
ms.prod: ".net-framework-4.6"
ms.reviewer: ""
ms.suite: ""
ms.technology: 
  - "dotnet-clr"
ms.tgt_pltfrm: ""
ms.topic: "article"
ms.assetid: 1c27e9fe-11f8-4377-8140-828207b98a0e
caps.latest.revision: 19
author: "Erikre"
ms.author: "erikre"
manager: "erikre"
caps.handback.revision: 19
---
# 操作フォーマッタと操作セレクター
このサンプルでは、[!INCLUDE[indigo1](../../../../includes/indigo1-md.md)] 拡張ポイントを使用して、[!INCLUDE[indigo2](../../../../includes/indigo2-md.md)] で想定されている形式と異なる形式のメッセージ データを許可する方法を示します。[!INCLUDE[indigo2](../../../../includes/indigo2-md.md)] フォーマッタの既定では、メソッド パラメータが `soap:body` 要素の下に格納されることが想定されています。このサンプルでは、代わりに HTTP GET クエリ文字列のパラメータ データを解析するカスタム操作フォーマッタを実装し、そのデータを使用してメソッドを呼び出す方法を示します。  
  
 このサンプルは、`ICalculator` サービス コントラクトを実装する「[概要](../../../../docs/framework/wcf/samples/getting-started-sample.md)」に基づいています。加算、減算、乗算、および除算のメッセージを変更して、クライアントからサーバーへの要求を行うための HTTP GET と、サーバーからクライアントへの要求を行うための POX メッセージ付きの HTTP POST を使用する方法を示します。  
  
 これを行うために、サンプルには次の機能が用意されています。  
  
-   `QueryStringFormatter`。クライアントとサーバー用に、それぞれ <xref:System.ServiceModel.Dispatcher.IClientMessageFormatter> と <xref:System.ServiceModel.Dispatcher.IDispatchMessageFormatter> を実装し、クエリ文字列内のデータを処理します。  
  
-   `UriOperationSelector`。サーバー上に、GET 要求内の操作名に基づいて操作ディスパッチを実行する <xref:System.ServiceModel.Dispatcher.IDispatchOperationSelector> を実装します。  
  
-   `EnableHttpGetRequestsBehavior` エンドポイント動作 \(および対応する構成\)。必要な操作セレクタをランタイムに追加します。  
  
-   新しい操作フォーマッタをランタイムに挿入する方法を示します。  
  
-   このサンプルでは、クライアントとサービスは両方ともコンソール アプリケーション \(.exe\) です。  
  
> [!NOTE]
>  このサンプルのセットアップ手順とビルド手順については、このトピックの最後を参照してください。  
  
## 重要な概念  
 `QueryStringFormatter` \- この操作フォーマッタは [!INCLUDE[indigo2](../../../../includes/indigo2-md.md)] 内のコンポーネントで、メッセージをパラメータ オブジェクトの配列に変換したり、逆にパラメータ オブジェクトの配列をメッセージに変換したりします。この操作は、クライアント上では <xref:System.ServiceModel.Dispatcher.IClientMessageFormatter> インターフェイスを使用して、サーバー上では <xref:System.ServiceModel.Dispatcher.IDispatchMessageFormatter> インターフェイスを使用して行われます。これらのインターフェイスを使用すると、ユーザーは `Serialize` メソッドと `Deserialize` メソッドから要求メッセージと応答メッセージを取得できます。  
  
 このサンプルでは、`QueryStringFormatter` にこれら両方のインターフェイスが実装され、クライアントとサーバーで実装されます。  
  
 要求 :  
  
-   このサンプルでは、<xref:System.ComponentModel.TypeConverter> クラスを使用して要求メッセージ内のパラメータ データを文字列に変換したり、その逆の処理を行ったりします。<xref:System.ComponentModel.TypeConverter> が特定の型で使用できない場合は、サンプルのフォーマッタから例外がスローされます。  
  
-   クライアントの `IClientMessageFormatter.SerializeRequest` メソッドでは、フォーマッタは適切な宛先アドレスを使用して URI を作成し、操作名をサフィックスとして追加します。この操作名は、サーバー上の適切な操作へのディスパッチに使用されます。次にパラメータ名と <xref:System.ComponentModel.TypeConverter> によって変換された値を使用して、パラメータ オブジェクトの配列を取得し、そのパラメータ データを URI クエリ文字列にシリアル化します。<xref:System.ServiceModel.Channels.MessageHeaders.To%2A> プロパティおよび <xref:System.ServiceModel.Channels.MessageProperties.Via%2A> プロパティは、この URI に設定されます。<xref:System.ServiceModel.Channels.MessageProperties> にアクセスするには、<xref:System.ServiceModel.Channels.Message.Properties%2A> プロパティを使用します。  
  
-   サーバーの `IDispatchMessageFormatter.DeserializeRequest` メソッドでは、フォーマッタは受信要求メッセージ プロパティ内で`Via` URI を検索します。次に URI クエリ文字列内にある名前と値の組み合わせを解析してパラメータ名と値に分け、そのパラメータ名と値を使用してこのメソッドに渡されるパラメータの配列を設定します。操作ディスパッチは既に発生しているので、このメソッドでは操作名のサフィックスは無視されます。  
  
 応答 :  
  
-   このサンプルでは、HTTP GET は要求のみに使用されます。応答を送信するには、フォーマッタを XML メッセージの生成時に使用できたはずの最初のフォーマッタに代行させます。このサンプルの目的の 1 つは、このような代行フォーマッタの実装方法を示すことです。  
  
### UriPathSuffixOperationSelector クラス  
 <xref:System.ServiceModel.Dispatcher.IDispatchOperationSelector> インターフェイスを使用すると、特定のメッセージをディスパッチする必要がある操作で、ユーザー独自のロジックを実装できます。  
  
 このサンプルでは、操作名がメッセージのアクション ヘッダーではなく、HTTP GET URI に含まれているため、適切な操作を選択するには、`UriPathSuffixOperationSelector` がサーバーに実装されている必要があります。サンプルは、大文字小文字が区別されない操作名のみを許可するようにセットアップされています。  
  
 `SelectOperation` メソッドは、受信メッセージを取得し、メッセージ プロパティ内の `Via` URI を検索します。次に、この URI から操作名のサフィックスを抽出し、内部テーブルを検索してメッセージをディスパッチする必要がある操作名を取得して、その操作名を返します。  
  
### EnableHttpGetRequestsBehavior クラス  
 `UriPathSuffixOperationSelector` コンポーネントは、プログラムまたはエンドポイント動作を使用してセットアップできます。サンプルでは、サービスのアプリケーション構成ファイルで指定された `EnableHttpGetRequestsBehavior` の動作を実装します。  
  
 サーバー側 :  
  
 <xref:System.ServiceModel.Dispatcher.DispatchRuntime.OperationSelector%2A> は <xref:System.ServiceModel.Dispatcher.IDispatchOperationSelector> 実装に設定されます。  
  
 [!INCLUDE[indigo2](../../../../includes/indigo2-md.md)] は、既定では完全一致のアドレス フィルタを使用します。受信メッセージ上の URI には、操作名のサフィックスと、その後にパラメータ データを格納するクエリ文字列が含まれています。したがって、アドレス フィルタは、エンドポイントの動作によっても、プレフィックスが一致するフィルタに変更されます。このために、[!INCLUDE[indigo2](../../../../includes/indigo2-md.md)]<xref:System.ServiceModel.Dispatcher.PrefixEndpointAddressMessageFilter> が使用されます。  
  
### 操作フォーマッタのインストール  
 フォーマッタを指定する操作の動作は一意です。このような動作は、常に既定で操作ごとに実装され、必要な操作フォーマッタを作成します。ただし、これらの動作は他の操作動作と同様に見えるため、他の属性では識別できません。置換動作をインストールするには、この実装で、既定で [!INCLUDE[indigo2](../../../../includes/indigo2-md.md)] 型ローダーによってインストールされる特定のフォーマッタの動作を検索し、その動作に置き換えるか、または互換性のある動作を追加して既定の動作後に実行する必要があります。  
  
 これらの操作フォーマッタの動作は、<xref:System.ServiceModel.Channels.CommunicationObject.Open%2A?displayProperty=fullName> を呼び出す前にプログラムでセットアップするか、または既定のフォーマッタ動作後に実行される操作動作を指定することによってセットアップすることができます。ただし、エンドポイント動作を介したセットアップ \(および構成を介したセットアップ\) は容易ではありません。この動作モデルでは、動作によって別の動作を置き換えることはできず、他の方法で説明ツリーを変更することもできないためです。  
  
 クライアント側 :  
  
 <xref:System.ServiceModel.Dispatcher.IClientMessageFormatter> の実装を行って、要求を HTTP GET 要求に変換し、応答を元のフォーマッタに代行させる必要があります。これを行うには、`EnableHttpGetRequestsBehavior.ReplaceFormatterBehavior` ヘルパー メソッドを呼び出します。  
  
 この手順は、`CreateChannel` を呼び出す前に行う必要があります。  
  
```  
void ReplaceFormatterBehavior(OperationDescription operationDescription, EndpointAddress address)  
{  
    // Remove the DataContract behavior if it is present.  
    IOperationBehavior formatterBehavior = operationDescription.Behaviors.Remove<DataContractSerializerOperationBehavior>();  
    if (formatterBehavior == null)  
    {  
        // Remove the XmlSerializer behavior if it is present.  
        formatterBehavior = operationDescription.Behaviors.Remove<XmlSerializerOperationBehavior>();  
        ...  
    }  
  
    // Remember what the innerFormatterBehavior was.  
    DelegatingFormatterBehavior delegatingFormatterBehavior = new DelegatingFormatterBehavior(address);  
    delegatingFormatterBehavior.InnerFormatterBehavior = formatterBehavior;  
   operationDescription.Behaviors.Add(delegatingFormatterBehavior);  
}  
```  
  
 サーバー側 :  
  
-   <xref:System.ServiceModel.Dispatcher.IDispatchMessageFormatter> インターフェイスを実装して、HTTP GET 要求を読み込み、応答の書き出しを元のフォーマッタに代行させる必要があります。これを行うには、クライアントと同じ`EnableHttpGetRequestsBehavior.ReplaceFormatterBehavior` ヘルパー メソッドを呼び出します \(前述のコード サンプルを参照\)。  
  
-   この手順は、<xref:System.ServiceModel.Channels.CommunicationObject.Open%2A> を呼び出す前に行う必要があります。このサンプルでは、<xref:System.ServiceModel.Channels.CommunicationObject.Open%2A> を呼び出す前にこのフォーマッタを手動で変更する方法を示します。同じ結果を得るための別の方法としては、オープン操作の前に `EnableHttpGetRequestsBehavior.ReplaceFormatterBehavior` を呼び出す <xref:System.ServiceModel.ServiceHost> からクラスを派生します \(ホスティングのドキュメントおよびサンプル例を参照してください\)。  
  
### ユーザー エクスペリエンス  
 サーバー側 :  
  
-   サーバーの `ICalculator` 実装は、変更する必要はありません。  
  
-   サービス用の App.config では、`textMessageEncoding` 要素の `messageVersion` 属性を `None` に設定するカスタムの POX バインディングを使用する必要があります。  
  
    ```  
    <bindings>  
      <customBinding>  
        <binding name="poxBinding">  
          <textMessageEncoding messageVersion="None" />  
          <httpTransport />  
        </binding>  
      </customBinding>  
    </bindings>  
    ```  
  
-   サービス用の App.config ではさらに、カスタムの `EnableHttpGetRequestsBehavior` を動作の拡張セクションに追加して使用することによって、これを指定する必要があります。  
  
    ```  
    <behaviors>  
      <endpointBehaviors>  
        <behavior name="enableHttpGetRequestsBehavior">  
          <enableHttpGetRequests />  
        </behavior>  
      </endpointBehaviors>  
    </behaviors>  
  
    <extensions>  
      <behaviorExtensions>  
        <!-- Enabling HTTP GET requests: Behavior Extension -->  
        <add   
          name="enableHttpGetRequests"           type="Microsoft.ServiceModel.Samples.EnableHttpGetRequestsBehaviorElement, QueryStringFormatter, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />  
      </behaviorExtensions>  
    </extensions>  
    ```  
  
-   <xref:System.ServiceModel.Channels.CommunicationObject.Open%2A> を呼び出す前に操作フォーマッタを追加します。  
  
 クライアント側 :  
  
-   クライアントの実装は、変更する必要はありません。  
  
-   クライアント用の App.config では、`textMessageEncoding` 要素の `messageVersion` 属性を `None` に設定するカスタムの POX バインディングを使用する必要があります。サービスとの違いは、クライアントでは送信先アドレスを変更できるように手動アドレス指定を有効にする必要があるという点です。  
  
    ```  
    <bindings>  
      <customBinding>  
        <binding name="poxBinding">  
          <textMessageEncoding messageVersion="None" />  
          <httpTransport manualAddressing="True" />  
        </binding>  
      </customBinding>  
    </bindings>  
    ```  
  
-   クライアント用の App.config では、サーバーと同じカスタムの `EnableHttpGetRequestsBehavior` を指定する必要があります。  
  
-   <xref:System.ServiceModel.ChannelFactory%601.CreateChannel> を呼び出す前に操作フォーマッタを追加します。  
  
 このサンプルを実行する場合は、操作要求および応答はクライアントのコンソール ウィンドウに表示されます。4 つすべての操作 \(加算、減算、乗算、および除算\) が正常に行われる必要があります。  
  
> [!IMPORTANT]
>  サンプルは、既にコンピューターにインストールされている場合があります。続行する前に、次の \(既定の\) ディレクトリを確認してください。  
>   
>  `<InstallDrive>:\WF_WCF_Samples`  
>   
>  このディレクトリが存在しない場合は、「[.NET Framework 4 向けの Windows Communication Foundation \(WCF\) および Windows Workflow Foundation \(WF\) のサンプル](http://go.microsoft.com/fwlink/?LinkId=150780)」にアクセスして、[!INCLUDE[indigo1](../../../../includes/indigo1-md.md)] および [!INCLUDE[wf1](../../../../includes/wf1-md.md)] のサンプルをすべてダウンロードしてください。このサンプルは、次のディレクトリに格納されます。  
>   
>  `<InstallDrive>:\WF_WCF_Samples\WCF\Extensibility\Formatters\QuieryStringFormatter`  
  
##### サンプルを設定、ビルド、および実行するには  
  
1.  「[Windows Communication Foundation サンプルの 1 回限りのセットアップの手順](../../../../docs/framework/wcf/samples/one-time-setup-procedure-for-the-wcf-samples.md)」が実行済みであることを確認します。  
  
2.  ソリューションをビルドするには、「[Windows Communication Foundation サンプルのビルド](../../../../docs/framework/wcf/samples/building-the-samples.md)」の手順に従います。  
  
3.  単一コンピュータ構成か複数コンピュータ構成かに応じて、「[Windows Communication Foundation サンプルの実行](../../../../docs/framework/wcf/samples/running-the-samples.md)」の手順に従います。  
  
## 参照