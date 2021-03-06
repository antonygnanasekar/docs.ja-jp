---
title: "要素のテキストの推論 | Microsoft Docs"
ms.custom: ""
ms.date: "03/30/2017"
ms.prod: ".net-framework-4.6"
ms.reviewer: ""
ms.suite: ""
ms.technology: 
  - "dotnet-ado"
ms.tgt_pltfrm: ""
ms.topic: "article"
ms.assetid: 789799e5-716f-459f-a168-76c5cf22178b
caps.latest.revision: 4
author: "JennieHubbard"
ms.author: "jhubbard"
manager: "jhubbard"
caps.handback.revision: 4
---
# 要素のテキストの推論
要素にテキストは含まれているが、テーブルとして推論される \(属性を持つ要素または繰り返し出現する要素などの\) 子の要素がない場合は、**TableName\_Text** という名前の新しい列が、要素に対して推論されるテーブルに追加されます。  要素に含まれているテキストはテーブルの行に追加され、新しい列に格納されます。  新しい列の **ColumnMapping** プロパティは、**MappingType.SimpleContent** に設定されます。  
  
 たとえば、次のような XML があるとします。  
  
```  
<DocumentElement>  
  <Element1 attr1="value1">Text1</Element1>  
</DocumentElement>  
```  
  
 推論プロセスにより、**attr1** および **Element1\_Text** という 2 つの列を持つ **Element1** という名前のテーブルが生成されます。  **attr1** 列の **ColumnMapping** プロパティは、**MappingType.Attribute** に設定されます。  **Element1\_Text** 列の **ColumnMapping** プロパティは、**MappingType.SimpleContent** に設定されます。  
  
 **DataSet:** DocumentElement  
  
 **Table:** Element1  
  
|attr1|Element1\_Text|  
|-----------|--------------------|  
|value1|Text1|  
  
 要素にテキストだけでなく、テキストを含む子の要素も含まれている場合は、その要素に含まれているテキストを格納するための列はテーブルに追加されません。  要素に含まれるテキストは無視されますが、子の要素のテキストはテーブルの行に追加されます。  たとえば、次のような XML があるとします。  
  
```  
<Element1>  
  Text1  
  <ChildElement1>Text2</ChildElement1>  
  Text3  
</Element1>  
```  
  
 推論プロセスにより、**ChildElement1** という 1 つの列を持つ **Element1** という名前のテーブルが生成されます。  **ChildElement1** 要素のテキストは、テーブルの行に追加されます。  その他のテキストは無視されます。  **ChildElement1** 列の **ColumnMapping** プロパティは、**MappingType.Element** に設定されます。  
  
 **DataSet:** DocumentElement  
  
 **Table:** Element1  
  
|ChildElement1|  
|-------------------|  
|Text2|  
  
## 参照  
 [XML からの DataSet リレーショナル構造の推論](../../../../../docs/framework/data/adonet/dataset-datatable-dataview/inferring-dataset-relational-structure-from-xml.md)   
 [XML からの DataSet の読み込み](../../../../../docs/framework/data/adonet/dataset-datatable-dataview/loading-a-dataset-from-xml.md)   
 [XML の DataSet スキーマ情報の読み込み](../../../../../docs/framework/data/adonet/dataset-datatable-dataview/loading-dataset-schema-information-from-xml.md)   
 [DataSet での XML の使用](../../../../../docs/framework/data/adonet/dataset-datatable-dataview/using-xml-in-a-dataset.md)   
 [DataSets、DataTables、および DataViews](../../../../../docs/framework/data/adonet/dataset-datatable-dataview/index.md)   
 [ADO.NET Managed Providers and DataSet Developer Center \(ADO.NET マネージ プロバイダーと DataSet デベロッパー センター\)](http://go.microsoft.com/fwlink/?LinkId=217917)