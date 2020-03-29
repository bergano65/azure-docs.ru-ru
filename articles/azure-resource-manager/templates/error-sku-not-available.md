---
title: SKU недоступные ошибки
description: Описывает, как устранить ошибку SKU, недоступная при развертывании ресурсов с помощью менеджера ресурсов Azure.
ms.topic: troubleshooting
ms.date: 02/18/2020
ms.openlocfilehash: 3dcc26f2d74799a6d282ee4bd733d36bec7b05e4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78942726"
---
# <a name="resolve-errors-for-sku-not-available"></a>Устранение ошибок, связанных с недоступностью номера SKU

В этой статье объясняется, как устранить ошибку **SkuNotAvailable**. Если вы не можете найти подходящий SKU в этом регионе/зоне или альтернативный регион/зону, удовлетворяющие ваши бизнес-потребности, отправьте [запрос SKU](https://aka.ms/skurestriction) в службу поддержки Azure.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="symptom"></a>Симптом

При развертывании ресурса (как правило, виртуальной машины) появляются следующие код ошибки и сообщение об ошибке:

```
Code: SkuNotAvailable
Message: The requested tier for resource '<resource>' is currently not available in location '<location>'
for subscription '<subscriptionID>'. Please try another tier or deploy to a different location.
```

## <a name="cause"></a>Причина

Эта ошибка возникает, когда выбранный номер SKU ресурса (например, размер виртуальной машины) не доступен для указанного расположения.

При развертывании набора Azure Spot VM или Spot- масштаба нет какой-либо емкости для Azure Spot в этом местоположении. Для получения дополнительной [Spot error messages](../../virtual-machines/error-codes-spot.md)информации см.

## <a name="solution-1---powershell"></a>Решение 1 — PowerShell

Чтобы определить, какие Ски доступны в регионе/зоне, используйте команду [Get-AzComputeResourceSku.](/powershell/module/az.compute/get-azcomputeresourcesku) Отфильтруйте результаты по расположению. Эта команда поддерживается только в Azure PowerShell последней версии.

```azurepowershell-interactive
Get-AzComputeResourceSku | where {$_.Locations -icontains "centralus"}
```

Результаты включают список номеров SKU для расположения и имеющиеся ограничения для этого номера SKU. Обратите внимание, что номер SKU может отображаться как `NotAvailableForSubscription`.

```output
ResourceType          Name           Locations   Zone      Restriction                      Capability           Value
------------          ----           ---------   ----      -----------                      ----------           -----
virtualMachines       Standard_A0    centralus             NotAvailableForSubscription      MaxResourceVolumeMB   20480
virtualMachines       Standard_A1    centralus             NotAvailableForSubscription      MaxResourceVolumeMB   71680
virtualMachines       Standard_A2    centralus             NotAvailableForSubscription      MaxResourceVolumeMB  138240
virtualMachines       Standard_D1_v2 centralus   {2, 1, 3}                                  MaxResourceVolumeMB
```

Некоторые дополнительные образцы:

```azurepowershell-interactive
Get-AzComputeResourceSku | where {$_.Locations.Contains("centralus") -and $_.ResourceType.Contains("virtualMachines") -and $_.Name.Contains("Standard_DS14_v2")}
Get-AzComputeResourceSku | where {$_.Locations.Contains("centralus") -and $_.ResourceType.Contains("virtualMachines") -and $_.Name.Contains("v3")} | fc
```

Приложение "fc" в конце возвращает более подробную информацию.

## <a name="solution-2---azure-cli"></a>Решение 2 — Azure CLI

Чтобы определить, какие номера SKU доступны в регионе, используйте команду `az vm list-skus`. Параметр `--location` служит для фильтрации выходных данных с учетом используемого расположения. Параметр `--size` позволяет выполнить поиск по частичному названию размера.

```azurecli-interactive
az vm list-skus --location southcentralus --size Standard_F --output table
```

Результаты выполнения команды выглядят так:

```output
ResourceType     Locations       Name              Zones    Capabilities    Restrictions
---------------  --------------  ----------------  -------  --------------  --------------
virtualMachines  southcentralus  Standard_F1                ...             None
virtualMachines  southcentralus  Standard_F2                ...             None
virtualMachines  southcentralus  Standard_F4                ...             None
...
```


## <a name="solution-3---azure-portal"></a>Решение 3 — портал Azure

Чтобы определить, какие номера SKU доступны в регионе, используйте [портал](https://portal.azure.com). Войдите на портал и добавьте ресурс с помощью интерфейса. При настройке значений вы увидите доступные SKU для этого ресурса. Завершать развертывание нет необходимости.

Например, начните процесс создания виртуальной машины. Чтобы просмотреть другой доступный размер, выберите **Изменить размер**.

![Создание виртуальной машины](./media/error-sku-not-available/create-vm.png)

Можно фильтровать и прокручивать доступные размеры.

![Доступные номера SKU](./media/error-sku-not-available/available-sizes.png)

## <a name="solution-4---rest"></a>Решение 4 — REST

Чтобы определить, какие номера SKU доступны в регионе, используйте операцию [Resource Skus - List](/rest/api/compute/resourceskus/list) (перечисление номеров SKU ресурсов).

Он возвращает доступные номера SKU и регионы в приведенном ниже формате.

```json
{
  "value": [
    {
      "resourceType": "virtualMachines",
      "name": "Standard_A0",
      "tier": "Standard",
      "size": "A0",
      "locations": [
        "eastus"
      ],
      "restrictions": []
    },
    {
      "resourceType": "virtualMachines",
      "name": "Standard_A1",
      "tier": "Standard",
      "size": "A1",
      "locations": [
        "eastus"
      ],
      "restrictions": []
    },
    ...
  ]
}
```

