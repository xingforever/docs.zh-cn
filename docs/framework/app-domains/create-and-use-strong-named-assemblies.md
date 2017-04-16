---
title: "创建和使用具有强名称的程序集 | Microsoft Docs"
ms.custom: ""
ms.date: "03/30/2017"
ms.prod: ".net-framework"
ms.reviewer: ""
ms.suite: ""
ms.technology: 
  - "dotnet-bcl"
ms.tgt_pltfrm: ""
ms.topic: "article"
helpviewer_keywords: 
  - "程序集 [.NET Framework], 签名"
  - "程序集 [.NET Framework], 具有强名称的"
  - "程序集绑定, 具有强名称的"
  - "对程序集进行签名"
  - "强名称跳过功能"
  - "具有强名称的程序集"
  - "具有强名称的程序集, 关于具有强名称的程序集"
  - "具有强名称的程序集, 加载到信任的应用程序域中"
  - "具有强名称的程序集, 方案"
ms.assetid: ffbf6d9e-4a88-4a8a-9645-4ce0ee1ee5f9
caps.latest.revision: 17
author: "rpetrusha"
ms.author: "ronpet"
manager: "wpickett"
caps.handback.revision: 15
---
# 创建和使用具有强名称的程序集
强名称是由程序集的标识加上公钥和数字签名组成的。其中，程序集的标识包括简单文本名称、版本号和区域性信息（如果提供的话）。  它使用相应私钥从程序集文件生成。  （程序集文件包含程序集清单，该清单包含组成程序集的所有文件的名称和哈希。）  
  
 具有强名称的程序集只能使用其他具有强名称的程序集的类型。  否则，将会危害具有强名称的程序集的安全性。  
  
 本概述包含以下几节：  
  
-   [强名称方案](#strong_name_scenario)  
  
-   [跳过受信任程序集的签名验证](#bypassing_signature_verification)  
  
-   [相关主题](#related_topics)  
  
<a name="strong_name_scenario"></a>   
## 强名称方案  
 下面的方案概述了对具有强名称的程序集进行签名以及稍后使用该名称对其进行引用的过程。  
  
1.  使用以下方法之一创建具有强名称的程序集 A：  
  
    -   使用支持创建强名称的开发环境，如 [!INCLUDE[vsprvslong](../../../includes/vsprvslong-md.md)]。  
  
    -   使用[强名称工具 \(Sn.exe\)](../../../docs/framework/tools/sn-exe-strong-name-tool.md) 创建加密密钥对，并使用命令行编译器或[程序集链接器 \(Al.exe\)](../../../docs/framework/tools/al-exe-assembly-linker.md) 将密钥对分配到程序集。  Windows 软件开发工具包 \(SDK\) 同时提供 Sn.exe 和 Al.exe。  
  
2.  开发环境或工具对包含具有开发人员私钥的程序集清单的文件的哈希进行签名。  此数字签名存储在包含程序集 A 的清单的可移植可执行 \(PE\) 文件中。  
  
3.  程序集 B 是程序集 A 的使用者。  程序集 B 清单的引用部分包含表示程序集 A 公钥的标记。  标记是完整公钥的一部分，并且使用它而不是密钥本身以节省空间。  
  
4.  当程序集放置在全局程序集缓存中时，公共语言运行时验证强名称签名。  当在运行时通过强名称绑定时，公共语言运行时将存储在程序集 B 清单的密钥与用来生成程序集 A.的强名称的密钥进行比较。  如果通过 .NET Framework 安全检查，并且绑定成功，则程序集 B 具有一个保证，可保证程序集 A 的位未被篡改，并且这些位确实来自程序集 A 的开发人员。  
  
> [!NOTE]
>  此方案不解决信任问题。  除强名称外，程序集可携带完整的 Microsoft Authenticode 签名。  Authenticode 签名包括建立信任的证书。  请务必注意强名称不要求代码以这种方式进行签名。  事实上，用于生成强名称签名的密钥不需要与用于生成 Authenticode 签名的密钥相同。  
  
 [返回页首](#top)  
  
<a name="bypassing_signature_verification"></a>   
## 跳过受信任程序集的签名验证  
 从 [!INCLUDE[net_v35SP1_long](../../../includes/net-v35sp1-long-md.md)]开始，当程序集加载到完全信任的应用程序域（如 `MyComputer` 区域的默认应用程序域）时，不会验证强名称签名。  这被称之为强名称跳过功能。  在完全信任的环境中，对于已签名的完全信任的程序集，对 <xref:System.Security.Permissions.StrongNameIdentityPermission> 的需求总是成功，而不考虑其签名。  这种情况下，强名称跳过功能可避免完全信任程序集不必要的强名称签名验证开销，允许更快地加载程序集。  
  
 跳过功能适用于使用强名称进行签名及具有以下特征的任何程序集：  
  
-   完全受信任，无需 <xref:System.Security.Policy.StrongName> 证据（如具有 `MyComputer` 区域证据）。  
  
-   加载到完全受信任的 <xref:System.AppDomain>。  
  
-   加载自该 <xref:System.AppDomain> 的 <xref:System.AppDomainSetup.ApplicationBase%2A> 属性下的某个位置。  
  
-   签名没有延迟。  
  
 可为单个应用程序或计算机禁用此功能。  请参阅[如何：禁用强名称跳过功能](../../../docs/framework/app-domains/how-to-disable-the-strong-name-bypass-feature.md)。  
  
 [返回页首](#top)  
  
<a name="related_topics"></a>   
## 相关主题  
  
|标题|说明|  
|--------|--------|  
|[如何：创建公钥\/私钥对](../../../docs/framework/app-domains/how-to-create-a-public-private-key-pair.md)|描述如何创建加密密钥对以对程序集进行签名。|  
|[如何：使用强名称为程序集签名](../../../docs/framework/app-domains/how-to-sign-an-assembly-with-a-strong-name.md)|介绍如何创建具有强名称的程序集。|  
|[改进的强命名](../../../docs/framework/app-domains/enhanced-strong-naming.md)|描述 [!INCLUDE[net_v45](../../../includes/net-v45-md.md)] 中强名称的改进。|  
|[如何：引用具有强名称的程序集](../../../docs/framework/app-domains/how-to-reference-a-strong-named-assembly.md)|介绍如何在编译时或运行时引用具有强名称的程序集中的类型或资源。|  
|[如何：禁用强名称跳过功能](../../../docs/framework/app-domains/how-to-disable-the-strong-name-bypass-feature.md)|描述如何禁用跳过强名称签名验证的功能。  可为所有或特定应用程序禁用此功能。|  
|[创建程序集](../../../docs/framework/app-domains/create-assemblies.md)|提供单个文件和多文件程序集的概述。|  
|[NIB: How to: Delay Sign an Assembly \(Visual Studio\)](http://msdn.microsoft.com/zh-cn/cab63b7a-591e-4674-b236-d77cd29a79ea)|说明如何在创建程序集后对具有强名称的程序集进行签名。|  
|[Sn.exe（强名称工具）](../../../docs/framework/tools/sn-exe-strong-name-tool.md)|介绍 .NET Framework 中包含的可帮助创建具有强名称的程序集的工具。  此工具提供有关密钥管理、签名生成和签名验证的选项。|  
|[Al.exe（程序集链接器）](../../../docs/framework/tools/al-exe-assembly-linker.md)|介绍 .NET Framework 中包含的一种工具，该工具可生成具有模块或资源文件的程序集清单的文件。|