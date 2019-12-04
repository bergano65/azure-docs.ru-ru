---
title: Ошибки недоступности SKU
description: В этой статье описывается, как устранить ошибку недоступности номера SKU при развертывании ресурсов с помощью Azure Resource Manager.
ms.topic: troubleshooting
ms.date: 10/19/2018
ms.openlocfilehash: 0b3696d3207a88d87b11e65f4697473963f960d5
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74769155"
---
# <a name="resolve-errors-for-sku-not-available"></a>Устранение ошибок, связанных с недоступностью номера SKU

В этой статье объясняется, как устранить ошибку **SkuNotAvailable**. Если вам не удалось найти подходящий номер SKU в этом или любом другом регионе, который соответствует потребностям вашей компании, отправьте [запрос на получение SKU](https://aka.ms/skurestriction) в службу поддержки Azure.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="symptom"></a>Симптом

При развертывании ресурса (как правило, виртуальной машины) появляются следующие код ошибки и сообщение об ошибке:

```
Code: SkuNotAvailable
Message: The requested tier for resource '<resource>' is currently not available in location '<location>' 
for subscription '<subscriptionID>'. Please try another tier or deploy to a different location.
```

## <a name="cause"></a>Причина:

Эта ошибка возникает, когда выбранный номер SKU ресурса (например, размер виртуальной машины) не доступен для указанного расположения.

Если вы развертываете экземпляр виртуальной машины Azure или экземпляра смесевой масштабируемого набора, в этом расположении нет никакой емкости для точки Azure. Дополнительные сведения см. в разделе [сообщения об ошибках](../virtual-machines/error-codes-spot.md).

## <a name="solution-1---powershell"></a>Решение 1 — PowerShell

Чтобы определить, какие номера SKU доступны в регионе, используйте команду [Get-AzComputeResourceSku](/powershell/module/az.compute/get-azcomputeresourcesku). Отфильтруйте результаты по расположению. Эта команда поддерживается только в Azure PowerShell последней версии.

```azurepowershell-interactive
Get-AzComputeResourceSku | where {$_.Locations -icontains "centralus"}
```

Результаты включают список номеров SKU для расположения и имеющиеся ограничения для этого номера SKU. Обратите внимание, что номер SKU может отображаться как `NotAvailableForSubscription`.

```powershell
ResourceType          Name        Locations   Restriction                      Capability           Value
------------          ----        ---------   -----------                      ----------           -----
virtualMachines       Standard_A0 centralus   NotAvailableForSubscription      MaxResourceVolumeMB   20480
virtualMachines       Standard_A1 centralus   NotAvailableForSubscription      MaxResourceVolumeMB   71680
virtualMachines       Standard_A2 centralus   NotAvailableForSubscription      MaxResourceVolumeMB  138240
```

## <a name="solution-2---azure-cli"></a>Решение 2 — Azure CLI

Чтобы определить, какие номера SKU доступны в регионе, используйте команду `az vm list-skus`. Параметр `--location` служит для фильтрации выходных данных с учетом используемого расположения. Параметр `--size` позволяет выполнить поиск по частичному названию размера.

```azurecli-interactive
az vm list-skus --location southcentralus --size Standard_F --output table
```

Результаты выполнения команды выглядят так:

```azurecli
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

![Создание виртуальной машины](./media/resource-manager-sku-not-available-errors/create-vm.png)

Можно фильтровать и прокручивать доступные размеры.

![Доступные номера SKU](./media/resource-manager-sku-not-available-errors/available-sizes.png)

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

