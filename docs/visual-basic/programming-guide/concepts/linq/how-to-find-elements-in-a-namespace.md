---
title: "方法: Namespace (XPATH-LINQ to XML) で要素を検索する (Visual Basic) |Microsoft ドキュメント"
ms.custom: 
ms.date: 2015-07-20
ms.prod: .net
ms.reviewer: 
ms.suite: 
ms.technology:
- devlang-visual-basic
ms.tgt_pltfrm: 
ms.topic: article
dev_langs:
- VB
ms.assetid: c7cb3b77-3424-4b54-9efa-4dc715948e41
caps.latest.revision: 3
author: dotnet-bot
ms.author: dotnetcontent
translationtype: Machine Translation
ms.sourcegitcommit: a06bd2a17f1d6c7308fa6337c866c1ca2e7281c0
ms.openlocfilehash: d665ddc1e7ad7340b05c97e790195abbc53e4f95
ms.lasthandoff: 03/13/2017


---
# <a name="how-to-find-elements-in-a-namespace-xpath-linq-to-xml-visual-basic"></a>方法: Namespace (XPATH-LINQ to XML) で要素を検索する (Visual Basic)
XPath 式を使用すると、特定の名前空間内のノードを検索できます。 XPath 式では、名前空間を指定する名前空間プレフィックスを使用します。 名前空間プレフィックスを含む XPath 式を解析するには、 <xref:System.Xml.IXmlNamespaceResolver>。</xref:System.Xml.IXmlNamespaceResolver>を実装する XPath メソッドにオブジェクトを渡す必要があります。 この例で使用<xref:System.Xml.XmlNamespaceManager>。</xref:System.Xml.XmlNamespaceManager>  
  
 XPath 式を次に示します。  
  
 `./aw:*`  
  
## <a name="example"></a>例  
 次の例では、2 つの名前空間を含む XML ツリーを読み込みます。 使用して、 <xref:System.Xml.XmlReader>XML ドキュメントを読み取る</xref:System.Xml.XmlReader>。 次に、取得、<xref:System.Xml.XmlNameTable>から、 <xref:System.Xml.XmlReader>、および<xref:System.Xml.XmlNamespaceManager><xref:System.Xml.XmlNameTable></xref:System.Xml.XmlNameTable></xref:System.Xml.XmlNamespaceManager></xref:System.Xml.XmlReader></xref:System.Xml.XmlNameTable>。 使用して、<xref:System.Xml.XmlNamespaceManager>要素を選択しています</xref:System.Xml.XmlNamespaceManager>。  
  
```vb  
Dim reader As XmlReader = _  
        XmlReader.Create("ConsolidatedPurchaseOrders.xml")  
Dim root As XElement = XElement.Load(reader)  
Dim nameTable As XmlNameTable = reader.NameTable  
Dim namespaceManager As XmlNamespaceManager = New XmlNamespaceManager(nameTable)  
namespaceManager.AddNamespace("aw", "http://www.adventure-works.com")  
  
Dim list1 As IEnumerable(Of XElement) = _  
        root.XPathSelectElements("./aw:*", namespaceManager)  
Dim list2 As IEnumerable(Of XElement) = _  
    From el In root.Elements() _  
    Where el.Name.Namespace = "http://www.adventure-works.com" _  
    Select el  
  
If list1.Count() = list2.Count() And _  
        list1.Intersect(list2).Count() = list1.Count() Then  
    Console.WriteLine("Results are identical")  
Else  
    Console.WriteLine("Results differ")  
End If  
For Each el As XElement In list2  
    Console.WriteLine(el)  
Next  
```  
  
 この例を実行すると、次の出力が生成されます。  
  
```  
Results are identical  
<aw:PurchaseOrder PONumber="11223" Date="2000-01-15" xmlns:aw="http://www.adventure-works.com">  
    <aw:ShippingAddress>  
      <aw:Name>Chris Preston</aw:Name>  
      <aw:Street>123 Main St.</aw:Street>  
      <aw:City>Seattle</aw:City>  
      <aw:State>WA</aw:State>  
      <aw:Zip>98113</aw:Zip>  
      <aw:Country>USA</aw:Country>  
    </aw:ShippingAddress>  
    <aw:BillingAddress>  
      <aw:Name>Chris Preston</aw:Name>  
      <aw:Street>123 Main St.</aw:Street>  
      <aw:City>Seattle</aw:City>  
      <aw:State>WA</aw:State>  
      <aw:Zip>98113</aw:Zip>  
      <aw:Country>USA</aw:Country>  
    </aw:BillingAddress>  
    <aw:DeliveryInstructions>Ship only complete order.</aw:DeliveryInstructions>  
    <aw:Item PartNum="LIT-01">  
      <aw:ProductID>Litware Networking Card</aw:ProductID>  
      <aw:Qty>1</aw:Qty>  
      <aw:Price>20.99</aw:Price>  
    </aw:Item>  
    <aw:Item PartNum="LIT-25">  
      <aw:ProductID>Litware 17in LCD Monitor</aw:ProductID>  
      <aw:Qty>1</aw:Qty>  
      <aw:Price>199.99</aw:Price>  
    </aw:Item>  
  </aw:PurchaseOrder>  
```  
  
## <a name="see-also"></a>関連項目  
 [LINQ to XML の XPath ユーザー (Visual Basic)](../../../../visual-basic/programming-guide/concepts/linq/linq-to-xml-for-xpath-users.md)
