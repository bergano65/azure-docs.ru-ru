---
title: Обновление конфигурации кластера Azure Service Fabric
description: Узнайте, как обновить конфигурацию, которая работает в кластере Service Fabric в Azure, с помощью шаблона Resource Manager.
author: dkkapur
ms.topic: conceptual
ms.date: 11/09/2018
ms.author: dekapur
ms.openlocfilehash: 476a2d910b916ea29132b108478d06f756454813
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75463283"
---
# <a name="upgrade-the-configuration-of-a-cluster-in-azure"></a>Обновление конфигурации кластера в Azure 

В этой статье описывается, как настроить различные параметры структуры для кластера Service Fabric. Для кластеров, размещенных в Azure, можно настроить параметры на [портале Azure](https://portal.azure.com) или использовать шаблон Azure Resource Manager.

> [!NOTE]
> Не все параметры доступны на портале, и рекомендуется [настроить его с помощью шаблона Azure Resource Manager](https://docs.microsoft.com/azure/service-fabric/service-fabric-best-practices-infrastructure-as-code). Портал предназначен только для Service Fabric сценария Дев\тест.
> 


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="customize-cluster-settings-using-resource-manager-templates"></a>Настройка параметров кластера с помощью шаблонов Resource Manager
Кластеры Azure можно настроить с помощью JSON-шаблона Resource Manager. Дополнительные сведения см. в статье о [параметрах конфигурации для кластеров](service-fabric-cluster-fabric-settings.md). В качестве примера ниже приведены инструкции, с помощью которых можно добавить новый параметр *MaxDiskQuotaInMB* в раздел *Diagnostics*, используя обозреватель ресурсов Azure.

1. Перейдите на сайт https://resources.azure.com.
2. Перейдите к подписке, развернув **subscriptions** ->  **\<Ваша подписка>**  -> **resourceGroups** ->  **\<Ваша группа ресурсов >**  -> **providers** -> **Microsoft.ServiceFabric** -> **clusters** ->  **\<Имя вашего кластера>**
3. В правом верхнем углу выберите пункт **Чтение и запись**.
4. Выберите **Изменить** и обновите элемент JSON `fabricSettings`, а затем добавьте новый элемент.

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
- Чтобы изменить параметр напрямую, используйте команды Azure PowerShell [Set-азсервицефабриксеттинг](https://docs.microsoft.com/powershell/module/az.servicefabric/Set-azServiceFabricSetting) и [Remove-азсервицефабриксеттинг](https://docs.microsoft.com/powershell/module/az.servicefabric/Remove-azServiceFabricSetting) .
- чтобы изменить параметр напрямую, используйте команды Azure CLI [az sf cluster setting](https://docs.microsoft.com/cli/azure/sf/cluster/setting).

## <a name="next-steps"></a>Дальнейшие действия
* Изучите сведения о [параметрах кластера Service Fabric](service-fabric-cluster-fabric-settings.md).
* Ознакомьтесь с концепцией [масштабирования кластера](service-fabric-cluster-scale-up-down.md).
