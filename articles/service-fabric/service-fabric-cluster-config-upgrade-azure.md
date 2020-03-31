---
title: Обновление конфигурации кластера Azure Service Fabric
description: Узнайте, как обновить конфигурацию, которая работает в кластере Service Fabric в Azure, с помощью шаблона Resource Manager.
author: dkkapur
ms.topic: conceptual
ms.date: 11/09/2018
ms.author: dekapur
ms.openlocfilehash: 476a2d910b916ea29132b108478d06f756454813
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75463283"
---
# <a name="upgrade-the-configuration-of-a-cluster-in-azure"></a>Обновление конфигурации кластера в Azure 

В этой статье описывается, как настроить различные параметры структуры для кластера Service Fabric. Для кластеров, размещенных в Azure, можно настроить параметры на [портале Azure](https://portal.azure.com) или использовать шаблон Azure Resource Manager.

> [!NOTE]
> Не все параметры доступны на портале, поэтому [рекомендуется выполнять настройку с помощью шаблона Azure Resource Manager](https://docs.microsoft.com/azure/service-fabric/service-fabric-best-practices-infrastructure-as-code). Портал используется только для сценариев разработки и тестирования Service Fabric.
> 


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="customize-cluster-settings-using-resource-manager-templates"></a>Настройка параметров кластера с помощью шаблонов Resource Manager
Кластеры Azure можно настроить с помощью JSON-шаблона Resource Manager. Дополнительные сведения см. в статье о [параметрах конфигурации для кластеров](service-fabric-cluster-fabric-settings.md). В качестве примера ниже приведены инструкции, с помощью которых можно добавить новый параметр *MaxDiskQuotaInMB* в раздел *Diagnostics*, используя обозреватель ресурсов Azure.

1. Перейдите на сайт https://resources.azure.com.
2. Перейдите к подписке, расширив **подписку** -> **\<>**  ->  **ресурсГруппы** -> **\<ресурса ** -> > ->  **поставщиков****Microsoft.ServiceFabric** -> **кластеров** -> **\<Ваше название кластера>**
3. В правом верхнем углу выберите **Read/Write.**
4. Выберите **исделять** и обновлять `fabricSettings` элемент JSON и добавлять новый элемент:

```json
      {
        "name": "Diagnostics",
        "parameters": [
          {
            "name": "MaxDiskQuotaInMB",
            "value": "65536"
          }
        ]
      }
```

Вы также можете настроить параметры кластера с помощью Azure Resource Manager одним из следующих способов:

- Экспортируйте и обновите шаблон Resource Manager с помощью [портала Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template).
- Экспортируйте и обновите шаблон Resource Manager с помощью [PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template-powershell).
- Экспортируйте и обновите шаблон Resource Manager с помощью [Azure CLI](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template-cli).
- Для непосредственного изменения настройки и настройки и команды Azure PowerShell [Set-AzServiceFabricSetting](https://docs.microsoft.com/powershell/module/az.servicefabric/Set-azServiceFabricSetting) и [Remove-AzServiceFabricSetting](https://docs.microsoft.com/powershell/module/az.servicefabric/Remove-azServiceFabricSetting) должны изменять настройки.
- чтобы изменить параметр напрямую, используйте команды Azure CLI [az sf cluster setting](https://docs.microsoft.com/cli/azure/sf/cluster/setting).

## <a name="next-steps"></a>Дальнейшие действия
* Изучите сведения о [параметрах кластера Service Fabric](service-fabric-cluster-fabric-settings.md).
* Ознакомьтесь с концепцией [масштабирования кластера](service-fabric-cluster-scale-up-down.md).
