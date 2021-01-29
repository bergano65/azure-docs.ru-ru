---
title: Создание пула в разных зонах доступности
description: Узнайте, как создать пул пакетной службы с политикой зональные для защиты от сбоев.
ms.topic: how-to
ms.date: 01/28/2021
ms.openlocfilehash: 98109e1b74106bc636eaa715575e4b30ab29f9e2
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/29/2021
ms.locfileid: "99056352"
---
# <a name="create-an-azure-batch-pool-across-availability-zones"></a>Создание пула пакетной службы Azure на Зоны доступности

Регионы Azure, которые поддерживают [зоны доступности](https://azure.microsoft.com/global-infrastructure/availability-zones/) , имеют как минимум три отдельные зоны, каждая из которых имеет собственный независимые источники питания, сеть и система охлаждения. При создании пула пакетной службы Azure с помощью конфигурации виртуальной машины можно выбрать подготовку пула пакетной службы в Зоны доступности. Создание пула с помощью этой политики зональные помогает защитить расчетные узлы пакетной службы от сбоев на уровне центра обработки данных Azure.

Например, можно создать пул с политикой зональные в регионе Azure, который поддерживает три Зоны доступности. Если в центре обработки данных Azure в одной зоне доступности произошла ошибка инфраструктуры, пул пакетной службы по-прежнему будет иметь работоспособные узлы в двух других Зоны доступности, поэтому пул останется доступным для планирования задач.

## <a name="regional-support-and-other-requirements"></a>Региональная поддержка и другие требования

Пакетная служба поддерживает четность в Azure на поддержку Зоны доступности. Чтобы использовать параметр зональные, необходимо создать пул в [поддерживаемом регионе Azure](../availability-zones/az-region.md).

Чтобы пул пакетной службы выделился между зонами доступности, регион Azure, в котором создается пул, должен поддерживать запрошенный номер SKU виртуальной машины в нескольких зонах. Это можно проверить путем вызова [API списка SKU ресурсов](/rest/api/compute/resourceskus/list) и проверки поля **локатионинфо** в [ресаурцеску](/rest/api/compute/resourceskus/list#resourcesku). Убедитесь, что для запрошенного номера SKU виртуальной машины поддерживается более одной зоны.

Для [учетных записей пакетной службы в пользовательском режиме подписки](accounts.md#batch-accounts)убедитесь, что в подписке, в которой создается пул, не предусмотрено ограничение на использование зоны для ЗАПРОШЕННОГО номера SKU виртуальной машины. Чтобы подтвердить это, вызовите [API списка SKU ресурсов](/rest/api/compute/resourceskus/list) и проверьте [ресаурцескурестриктионс](/rest/api/compute/resourceskus/list#resourceskurestrictions). Если существует ограничение для зоны, можно отправить запрос в [службу поддержки](../azure-portal/supportability/sku-series-unavailable.md) , чтобы удалить ограничение зоны.

Также обратите внимание, что нельзя создать пул с политикой зональные, если включено взаимодействие между узлами и используется [номер SKU виртуальной машины, поддерживающий InfiniBand](../virtual-machines/workloads/hpc/enable-infiniband.md).

## <a name="create-a-batch-pool-across-availability-zones"></a>Создание пула пакетной службы в Зоны доступности

В следующих примерах показано, как создать пул пакетной службы в Зоны доступности.

> [!NOTE]
> При создании пула с помощью политики зональные Пакетная Служба попытается выделить пул во всех Зоны доступности в выбранном регионе. Вы не можете указать конкретное распределение между зонами.

### <a name="batch-management-client-net-sdk"></a>Пакет SDK .NET для клиента управления пакетной службой

```csharp
pool.DeploymentConfiguration.VirtualMachineConfiguration.NodePlacementConfiguration = new NodePlacementConfiguration()
    {
        Policy = NodePlacementPolicyType.Zonal
    };

```

### <a name="batch-rest-api"></a>REST API пакетной службы

URL-АДРЕС REST API

```
POST {batchURL}/pools?api-version=2021-01-01.13.0
client-request-id: 00000000-0000-0000-0000-000000000000
```

Тело запроса

```
"pool": {
    "id": "pool2",
    "vmSize": "standard_a1",
    "virtualMachineConfiguration": {
        "imageReference": {
            "publisher": "Canonical",
            "offer": "UbuntuServer",
            "sku": "16.040-LTS"
        },
        "nodePlacementConfiguration": {
            "policy": "Zonal"
        }
        "nodeAgentSKUId": "batch.node.ubuntu 16.04"
    },
    "resizeTimeout": "PT15M",
    "targetDedicatedNodes": 5,
    "targetLowPriorityNodes": 0,
    "maxTasksPerNode": 3,
    "enableAutoScale": false,
    "enableInterNodeCommunication": false
}
```

## <a name="next-steps"></a>Дальнейшие действия

- Узнайте подробнее о [рабочем процессе и основных ресурсах пакетной службы](batch-service-workflow-features.md), таких как пулы, узлы, задания и задачи.
- Узнайте [, как создать пул в подсети виртуальной сети Azure](batch-virtual-network.md).
- Узнайте [, как создать пул пакетной службы Azure без общедоступных IP-адресов](./batch-pool-no-public-ip-address.md).

