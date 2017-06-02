---
title: "&lt;Namespace&gt; 要素 (.NET ネイティブ) | Microsoft Docs"
ms.custom: ""
ms.date: "03/30/2017"
ms.prod: ".net-framework"
ms.reviewer: ""
ms.suite: ""
ms.technology: 
  - "dotnet-clr"
ms.tgt_pltfrm: ""
ms.topic: "article"
ms.assetid: 57c614e5-18a9-4e87-bfd5-d0fe3396a192
caps.latest.revision: 20
author: "rpetrusha"
ms.author: "ronpet"
manager: "wpickett"
caps.handback.revision: 20
---
# &lt;Namespace&gt; 要素 (.NET ネイティブ)
指定した名前空間内のすべての型にランタイム リフレクション ポリシーを適用します。  
  
## <a name="syntax"></a>構文  
  
```xml  
  
<Namespace Name="namespace_name"   
           Activate="policy_type"   
           Browse="policy_type" />  
           Dynamic="policy_setting"  
           Serialize="policy_setting"  
           DataContractSerializer="policy_setting"  
           DataContractJsonSerializer="policy_setting"  
           XmlSerializer="policy_setting"  
           MarshalObject="policy_setting"  
           MarshalDelegate="policy_setting"  
           MarshalStructure="policy_setting" />  
  
```  
  
## <a name="attributes-and-elements"></a>属性および要素  
 以降のセクションでは、属性、子要素、および親要素について説明します。  
  
### <a name="attributes"></a>属性  
  
|属性|属性の型|説明|  
|---------------|--------------------|-----------------|  
|`Name`|全般|必須の属性です。 名前空間の名前を指定します。|  
|`Activate`|反射|省略可能な属性です。 コンストラクターへの実行時アクセスを制御して、インスタンスのアクティブ化を有効にします。|  
|`Browse`|反射|省略可能な属性です。 プログラム要素に関する情報の照会を制御しますが、実行時アクセスは有効にしません。|  
|`Dynamic`|反射|省略可能な属性です。 コンストラクター、メソッド、フィールド、プロパティ、およびイベントを含むすべての型のメンバーへの実行時アクセスを制御して、動的プログラミングを有効にします。|  
|`Serialize`|シリアル化|省略可能な属性です。 コンストラクター、フィールド、およびプロパティへの実行時アクセスを制御し、Newtonsoft の JSON シリアライザーなどのライブラリによって型インスタンスをシリアル化および逆シリアル化できるようにします。|  
|`DataContractSerializer`|シリアル化|省略可能な属性です。 使用するシリアル化のポリシーを制御、 <xref:System.Runtime.Serialization.DataContractSerializer?displayProperty=fullName>クラスです。|  
|`DataContractJsonSerializer`|シリアル化|省略可能な属性です。 ポリシーを使用する JSON シリアル化を制御、 <xref:System.Runtime.Serialization.Json.DataContractJsonSerializer?displayProperty=fullName>クラスです。|  
|`XmlSerializer`|シリアル化|省略可能な属性です。 使用する XML シリアル化のポリシーを制御、<xref:System.Xml.Serialization.XmlSerializer?displayProperty=fullName>クラスです。|  
|`MarshalObject`|Interop|省略可能な属性です。 Windows ランタイムと COM に参照型をマーシャリングするためのポリシーを制御します。|  
|`MarshalDelegate`|Interop|省略可能な属性です。 ネイティブ コードへの関数ポインターとしてデリゲート型をマーシャリングするためのポリシーを制御します。|  
|`MarshalStructure`|Interop|省略可能な属性です。 ネイティブ コードに構造体をマーシャリングするためのポリシーを制御します。|  
  
## <a name="name-attribute"></a>Name 属性  
  
|値|説明|  
|-----------|-----------------|  
|*namespace_name*|名前空間の名前。 場合、 <> \>要素の子では、 [ <> \</> \> ](../../../docs/framework/net-native/application-element-net-native.md)、 [ <> \</> \> ](../../../docs/framework/net-native/library-element-net-native.md)、または[ <> \> ](../../../docs/framework/net-native/assembly-element-net-native.md)要素、 *namespace_name*完全修飾名前空間名である必要があります。 場合、 <> \>要素は、別の子<>\>要素、 *namespace_name*空間の相対名である必要があります。|  
  
## <a name="all-other-attributes"></a>その他すべての属性  
  
|値|説明|  
|-----------|-----------------|  
|*policy_setting*|名前空間内のすべての型について、このポリシーの種類に適用する設定です。 指定できる値は、`All`、`Auto`、`Excluded`、`Public`、`PublicAndInternal`、`Required Public`、`Required PublicAndInternal`、および `Required All` です。 詳細については、次を参照してください。[ランタイム ディレクティブのポリシー設定](../../../docs/framework/net-native/runtime-directive-policy-settings.md)します。|  
  
### <a name="child-elements"></a>子要素  
  
|要素|説明|  
|-------------|-----------------|  
|`<Namespace>`|親名前空間内のすべての型にランタイム リフレクション ポリシーを適用します。|  
|[<>\>](../../../docs/framework/net-native/type-element-net-native.md)|型にリフレクション ポリシーを適用します。|  
|[<>\>](../../../docs/framework/net-native/typeinstantiation-element-net-native.md)|構築されたジェネリック型にリフレクション ポリシーを適用します。|  
  
### <a name="parent-elements"></a>親要素  
  
|要素|説明|  
|-------------|-----------------|  
|[<>\>](../../../docs/framework/net-native/application-element-net-native.md)|実行時にリフレクションで使用可能なメタデータを持つ、アプリケーション全体の型と型のメンバーのコンテナーとして機能します。 [ <> \> ](../../../docs/framework/net-native/application-element-net-native.md)要素が&0; または&1; を持つ[ <> \> ](../../../docs/framework/net-native/assembly-element-net-native.md)要素。|  
|[<>\>](../../../docs/framework/net-native/assembly-element-net-native.md)|指定したアセンブリ内のすべての型にランタイム リフレクション ポリシーを適用します。|  
|[<>\>](../../../docs/framework/net-native/library-element-net-native.md)|実行時にリフレクションに使用可能なメタデータを持つ型と型のメンバーを含むアセンブリを定義します。 [ <> \> ](../../../docs/framework/net-native/library-element-net-native.md)要素が&0; または&1; を持つ[ <> \> ](../../../docs/framework/net-native/assembly-element-net-native.md)要素。|  
|`<Namespace>`|親名前空間内のすべての型にリフレクション ポリシーを適用します。|  
  
## <a name="remarks"></a>コメント  
 `Activate` 属性、`Browse` 属性、`Dynamic`、および `Serialize` 属性はすべて省略可能です。 いずれも存在しない場合、`<Namespace>` 要素は子要素のコンテナーとしてのみ機能します。 存在する場合は、`<Namespace>` 要素は、指定された名前空間内のすべての型にランタイム リフレクション ポリシーを適用します。  
  
 子である場合、 [ <> \> ](../../../docs/framework/net-native/assembly-element-net-native.md)要素、`<Namespace>`要素によって定義されたランタイム リフレクション ポリシーをオーバーライド、 [ <> \> ](../../../docs/framework/net-native/assembly-element-net-native.md)要素。  
  
## <a name="see-also"></a>関連項目  
 [ランタイム ディレクティブ ポリシーの設定](../../../docs/framework/net-native/runtime-directive-policy-settings.md)   
 [ランタイム ディレクティブ (rd.xml) 構成ファイル リファレンス](../../../docs/framework/net-native/runtime-directives-rd-xml-configuration-file-reference.md)   
 [ランタイム ディレクティブ要素](../../../docs/framework/net-native/runtime-directive-elements.md)