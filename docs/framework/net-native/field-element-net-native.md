---
title: "&lt;Field&gt; 要素 (.NET ネイティブ) | Microsoft Docs"
ms.custom: ""
ms.date: "03/30/2017"
ms.prod: ".net-framework"
ms.reviewer: ""
ms.suite: ""
ms.technology: 
  - "dotnet-clr"
ms.tgt_pltfrm: ""
ms.topic: "article"
ms.assetid: 6a14125f-1a8d-41a1-8a32-659ca0ad12de
caps.latest.revision: 9
author: "rpetrusha"
ms.author: "ronpet"
manager: "wpickett"
caps.handback.revision: 9
---
# &lt;Field&gt; 要素 (.NET ネイティブ)
フィールドにランタイム リフレクション ポリシーを適用します。  
  
## <a name="syntax"></a>構文  
  
```xml  
  
<Field Name="field_name"  
       Browse="policy_type"  
       Dynamic="policy_type"  
       Serialize="policy_type" />  
  
```  
  
## <a name="attributes-and-elements"></a>属性および要素  
 以降のセクションでは、属性、子要素、および親要素について説明します。  
  
### <a name="attributes"></a>属性  
  
|属性|属性の型|説明|  
|---------------|--------------------|-----------------|  
|`Name`|全般|必須の属性です。 フィールド名を指定します。|  
|`Browse`|反射|省略可能な属性です。 フィールドに関する情報の照会やフィールドの列挙を制御しますが、実行時の動的アクセスは有効にしません。|  
|`Dynamic`|反射|省略可能な属性です。 フィールドへの実行時アクセスを制御して、動的プログラミングを有効にします。 このポリシーにより、実行時にフィールドを動的に設定または取得できるようになります。|  
|`Serialize`|シリアル化|省略可能な属性です。 フィールドへの実行時アクセスを制御して、型インスタンスを Newtonsoft の JSON シリアライザーなどのライブラリによってシリアル化できるようにしたり、データ バインディングで使用できるようにしたりします。|  
  
## <a name="name-attribute"></a>Name 属性  
  
|値|説明|  
|-----------|-----------------|  
|*メソッド名が*|フィールド名。 親フィールドの型が定義されている[ <> \> ](../../../docs/framework/net-native/type-element-net-native.md)または[ <> \> ](../../../docs/framework/net-native/typeinstantiation-element-net-native.md)要素。|  
  
## <a name="all-other-attributes"></a>その他すべての属性  
  
|値|説明|  
|-----------|-----------------|  
|*policy_setting*|フィールドのこのポリシーの種類に適用する設定です。 指定できる値は、`Auto`、`Excluded`、`Included`、および `Required` です。 詳細については、次を参照してください。[ランタイム ディレクティブのポリシー設定](../../../docs/framework/net-native/runtime-directive-policy-settings.md)します。|  
  
### <a name="child-elements"></a>子要素  
 なし。  
  
### <a name="parent-elements"></a>親要素  
  
|要素|説明|  
|-------------|-----------------|  
|[<>\>](../../../docs/framework/net-native/type-element-net-native.md)|型とそのすべてのメンバーにリフレクション ポリシーを適用します。|  
|[<>\>](../../../docs/framework/net-native/typeinstantiation-element-net-native.md)|構築されたジェネリック型とそのすべてのメンバーにリフレクション ポリシーを適用します。|  
  
## <a name="remarks"></a>コメント  
 フィールドのポリシーが明示的に定義されていない場合は、その親要素の実行時ポリシーを継承します。  
  
## <a name="see-also"></a>関連項目  
 [ランタイム ディレクティブ要素](../../../docs/framework/net-native/runtime-directive-elements.md)   
 [ランタイム ディレクティブ (rd.xml) 構成ファイル リファレンス](../../../docs/framework/net-native/runtime-directives-rd-xml-configuration-file-reference.md)   
 [ランタイム ディレクティブ ポリシーの設定](../../../docs/framework/net-native/runtime-directive-policy-settings.md)