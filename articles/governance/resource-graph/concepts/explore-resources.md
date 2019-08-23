---
title: Просмотр ресурсов Azure
description: Узнайте, как использовать язык запросов Graph, чтобы исследовать ресурсы и узнать, как они подключены.
author: DCtheGeek
ms.author: dacoulte
ms.date: 08/22/2019
ms.topic: conceptual
ms.service: resource-graph
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: 7c6fdebad3cd84699e1ac7d06bb58a33d1522af1
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/22/2019
ms.locfileid: "69972313"
---
# <a name="explore-your-azure-resources-with-resource-graph"></a>Изучение ресурсов Azure с помощью Resource Graph

График ресурсов Azure дает возможность обнаруживать и изучать ресурсы Azure быстро и в любом масштабе. Это средство, при разработке которого ставилась цель обеспечить быстрый отклик, отлично подходит для анализа среды и характеристик ваших ресурсов Azure.

[!INCLUDE [az-powershell-update](../../../../includes/updated-for-az.md)]

## <a name="explore-virtual-machines"></a>Изучение виртуальных машин

Стандартным ресурсом в Azure являются виртуальные машины. Они имеют много свойств, которые можно запрашивать. Каждое свойство поддерживает фильтрацию и поиск нужных ресурсов.

### <a name="virtual-machine-discovery"></a>Обнаружение виртуальных машин

Давайте начнем с простого запроса для получения отдельной виртуальной машины из среды и рассмотрим возвращенные свойства.

```kusto
where type =~ 'Microsoft.Compute/virtualMachines'
| limit 1
```

```azurecli-interactive
az graph query -q "where type =~ 'Microsoft.Compute/virtualMachines' | limit 1"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type =~ 'Microsoft.Compute/virtualMachines' | limit 1" | ConvertTo-Json -Depth 100
```

> [!NOTE]
> Командлет `Search-AzGraph` Azure PowerShell по умолчанию возвращает **PSCustomObject**. Чтобы вывод выглядел так же, как и возвращаемый интерфейсом командной строки Azure, используется командлет `ConvertTo-Json`. Значение по умолчанию для **глубины** — _2_. При установке значения _100_ все возвращенные уровни должны преобразоваться.

Результаты в формате JSON имеют структуру, аналогичную представленной ниже.

```json
[
  {
    "id": "/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/virtualMachines/ContosoVM1",
    "kind": "",
    "location": "westus2",
    "managedBy": "",
    "name": "ContosoVM1",
    "plan": {},
    "properties": {
      "hardwareProfile": {
        "vmSize": "Standard_B2s"
      },
      "networkProfile": {
        "networkInterfaces": [
          {
            "id": "/subscriptions/<subscriptionId>/MyResourceGroup/providers/Microsoft.Network/networkInterfaces/contosovm1535",
            "resourceGroup": "MyResourceGroup"
          }
        ]
      },
      "osProfile": {
        "adminUsername": "localAdmin",
        "computerName": "ContosoVM1",
        "secrets": [],
        "windowsConfiguration": {
          "enableAutomaticUpdates": true,
          "provisionVMAgent": true
        }
      },
      "provisioningState": "Succeeded",
      "storageProfile": {
        "dataDisks": [],
        "imageReference": {
          "offer": "WindowsServer",
          "publisher": "MicrosoftWindowsServer",
          "sku": "2016-Datacenter",
          "version": "latest"
        },
        "osDisk": {
          "caching": "ReadWrite",
          "createOption": "FromImage",
          "diskSizeGB": 127,
          "managedDisk": {
            "id": "/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/disks/ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166",
            "resourceGroup": "MyResourceGroup",
            "storageAccountType": "Premium_LRS"
          },
          "name": "ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166",
          "osType": "Windows"
        }
      },
      "vmId": "bbb9b451-6dc7-4117-bec5-c971eb1118c6"
    },
    "resourceGroup": "MyResourceGroup",
    "sku": {},
    "subscriptionId": "<subscriptionId>",
    "tags": {},
    "type": "microsoft.compute/virtualmachines"
  }
]
```

Свойства указывают дополнительные сведения о самом ресурсе виртуальной машины, все от SKU, ОС, дисков, тегов, а также группу ресурсов и подписку, членом которых является.

### <a name="virtual-machines-by-location"></a>Виртуальные машины по расположению

С учетом того, что мы узнали о ресурсе виртуальной машины, давайте используем свойство **location**, чтобы подсчитать все виртуальные машины по расположениям. Изменим запрос, убрав предельное значение и суммировав количество машин по расположениям.

```kusto
where type =~ 'Microsoft.Compute/virtualMachines'
| summarize count() by location
```

```azurecli-interactive
az graph query -q "where type =~ 'Microsoft.Compute/virtualMachines' | summarize count() by location"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type =~ 'Microsoft.Compute/virtualMachines' | summarize count() by location"
```

Результаты в формате JSON имеют структуру, аналогичную представленной ниже.

```json
[
  {
    "count_": 386,
    "location": "eastus"
  },
  {
    "count_": 215,
    "location": "southcentralus"
  },
  {
    "count_": 59,
    "location": "westus"
  }
]
```

Теперь мы видим, сколько виртуальных машин имеется в каждом регионе Azure.

### <a name="virtual-machines-by-sku"></a>Виртуальные машины по номеру SKU

Давайте вернемся к исходным свойствам виртуальных машин и попробуем найти все виртуальные машины, имеющие размер SKU **Standard_B2s**. Если посмотреть на возвращенный результат в формате JSON, можно увидеть, что он хранится в **properties.hardwareprofile.vmsize**. Мы изменим запрос так, чтобы он нашел все виртуальные машины соответствующего размера и вернул для каждой из них только имя и регион.

```kusto
where type =~ 'Microsoft.Compute/virtualMachines' and properties.hardwareProfile.vmSize == 'Standard_B2s'
| project name, resourceGroup"
```

```azurecli-interactive
az graph query -q "where type =~ 'Microsoft.Compute/virtualMachines' and properties.hardwareProfile.vmSize == 'Standard_B2s' | project name, resourceGroup"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type =~ 'Microsoft.Compute/virtualMachines' and properties.hardwareProfile.vmSize == 'Standard_B2s' | project name, resourceGroup"
```

### <a name="virtual-machines-connected-to-premium-managed-disks"></a>Виртуальные машины, подключенные к управляемым дискам (цен. категория "Премиум")

Если нам нужно получить сведения об управляемых дисках (цен. категория "Премиум"), подключенных к виртуальным машинам размера **Standard_B2s**, мы можем расширить запрос, чтобы получить идентификаторы ресурсов этих дисков.

```kusto
where type =~ 'Microsoft.Compute/virtualmachines' and properties.hardwareProfile.vmSize == 'Standard_B2s'
| extend disk = properties.storageProfile.osDisk.managedDisk
| where disk.storageAccountType == 'Premium_LRS'
| project disk.id
```

> [!NOTE]
> Еще один способ получить номер SKU — воспользоваться свойством **Microsoft.Compute/virtualMachines/sku.name** из раздела **aliases**. См. примеры для [отображения псевдонимов](../samples/starter.md#show-aliases) и [отображения различных значений псевдонимов](../samples/starter.md#distinct-alias-values) .

```azurecli-interactive
az graph query -q "where type =~ 'Microsoft.Compute/virtualmachines' and properties.hardwareProfile.vmSize == 'Standard_B2s' | extend disk = properties.storageProfile.osDisk.managedDisk | where disk.storageAccountType == 'Premium_LRS' | project disk.id"
```

```azurepowershell-interactive
  Search-AzGraph -Query "where type =~ 'Microsoft.Compute/virtualmachines' and properties.hardwareProfile.vmSize == 'Standard_B2s' | extend disk = properties.storageProfile.osDisk.managedDisk | where disk.storageAccountType == 'Premium_LRS' | project disk.id"
```

Результатом будет список идентификаторов дисков.

### <a name="managed-disk-discovery"></a>Обнаружение управляемых дисков

С помощью первой записи из предыдущего запроса мы изучим свойства управляемого диска, подключенного к первой виртуальной машине. Обновленный запрос использует идентификатор диска и изменяет тип.

Пример выходных данных предыдущего запроса:

```json
[
  {
    "disk_id": "/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/disks/ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166"
  }
]
```

```kusto
where type =~ 'Microsoft.Compute/disks' and id == '/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/disks/ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166'
```

Прежде чем выполнять запрос, давайте ответим на вопрос, как мы узнали, что свойство **type** теперь должно иметь значение **Microsoft.Compute/disks**.
Если посмотреть на полный идентификатор, то в его строке можно увидеть фрагмент **/providers/Microsoft.Compute/disks/** . Он подсказывает нам, какой тип нужно искать. В качестве альтернативы можно было бы снять ограничение по типу и выполнить поиск только по полю идентификатора. Так как идентификатор уникален, была бы возвращена только одна запись и определить тип можно было бы по свойству **type** в ней.

> [!NOTE]
> Чтобы этот пример можно было выполнить, необходимо заменить значение в поле идентификатора результатом из своей среды.

```azurecli-interactive
az graph query -q "where type =~ 'Microsoft.Compute/disks' and id == '/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/disks/ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166'"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type =~ 'Microsoft.Compute/disks' and id == '/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/disks/ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166'"
```

Результаты в формате JSON имеют структуру, аналогичную представленной ниже.

```json
[
  {
    "id": "/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/disks/ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166",
    "kind": "",
    "location": "westus2",
    "managedBy": "",
    "name": "ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166",
    "plan": {},
    "properties": {
      "creationData": {
        "createOption": "Empty"
      },
      "diskSizeGB": 127,
      "diskState": "ActiveSAS",
      "provisioningState": "Succeeded",
      "timeCreated": "2018-09-14T12:17:32.2570000Z"
    },
    "resourceGroup": "MyResourceGroup",
    "sku": {
      "name": "Premium_LRS",
      "tier": "Premium"
    },
    "subscriptionId": "<subscriptionId>",
    "tags": {
      "environment": "prod"
    },
    "type": "microsoft.compute/disks"
  }
]
```

## <a name="explore-virtual-machines-to-find-public-ip-addresses"></a>Определение общедоступных IP-адресов виртуальных машин

Сначала этот набор запросов находит и сохраняет все сетевые ресурсы (NIC), подключенные к виртуальным машинам. Затем запросы используют список сетевых адаптеров для поиска каждого ресурса IP-адреса, который является общедоступным IP-адресом, и сохраняет эти значения. Наконец, запросы предоставляют список общедоступных IP-адресов.

```azurecli-interactive
# Use Resource Graph to get all NICs and store in the 'nics.txt' file
az graph query -q "where type =~ 'Microsoft.Compute/virtualMachines' | project nic = tostring(properties['networkProfile']['networkInterfaces'][0]['id']) | where isnotempty(nic) | distinct nic | limit 20" --output table | tail -n +3 > nics.txt

# Review the output of the query stored in 'nics.txt'
cat nics.txt
```

```azurepowershell-interactive
# Use Resource Graph to get all NICs and store in the $nics variable
$nics = Search-AzGraph -Query "where type =~ 'Microsoft.Compute/virtualMachines' | project nic = tostring(properties['networkProfile']['networkInterfaces'][0]['id']) | where isnotempty(nic) | distinct nic | limit 20"

# Review the output of the query stored in the variable
$nics.nic
```

Используйте файл (Azure CLI) или переменную (Azure PowerShell) в следующем запросе, чтобы получить сведения о связанных ресурсах сетевого интерфейса, где есть общедоступный IP-адрес, подключенный к СЕТЕВому интерфейсу.

```azurecli-interactive
# Use Resource Graph with the 'nics.txt' file to get all related public IP addresses and store in 'publicIp.txt' file
az graph query -q="where type =~ 'Microsoft.Network/networkInterfaces' | where id in ('$(awk -vORS="','" '{print $0}' nics.txt | sed 's/,$//')') | project publicIp = tostring(properties['ipConfigurations'][0]['properties']['publicIPAddress']['id']) | where isnotempty(publicIp) | distinct publicIp" --output table | tail -n +3 > ips.txt

# Review the output of the query stored in 'ips.txt'
cat ips.txt
```

```azurepowershell-interactive
# Use Resource Graph  with the $nics variable to get all related public IP addresses and store in $ips variable
$ips = Search-AzGraph -Query "where type =~ 'Microsoft.Network/networkInterfaces' | where id in ('$($nics.nic -join "','")') | project publicIp = tostring(properties['ipConfigurations'][0]['properties']['publicIPAddress']['id']) | where isnotempty(publicIp) | distinct publicIp"

# Review the output of the query stored in the variable
$ips.publicIp
```

Наконец, используйте список ресурсов общедоступного IP-адреса, хранящихся в файле (Azure CLI) или переменной (Azure PowerShell), чтобы получить фактический общедоступный IP-адрес из связанного объекта и отобразить его.

```azurecli-interactive
# Use Resource Graph with the 'ips.txt' file to get the IP address of the public IP address resources
az graph query -q="where type =~ 'Microsoft.Network/publicIPAddresses' | where id in ('$(awk -vORS="','" '{print $0}' ips.txt | sed 's/,$//')') | project ip = tostring(properties['ipAddress']) | where isnotempty(ip) | distinct ip" --output table
```

```azurepowershell-interactive
# Use Resource Graph with the $ips variable to get the IP address of the public IP address resources
Search-AzGraph -Query "where type =~ 'Microsoft.Network/publicIPAddresses' | where id in ('$($ips.publicIp -join "','")') | project ip = tostring(properties['ipAddress']) | where isnotempty(ip) | distinct ip"
```

## <a name="next-steps"></a>Следующие шаги

- Дополнительные сведения о [языке запросов](query-language.md)
- См. описание используемого языка в разделе [Запросы для начинающих](../samples/starter.md)
- См. описание расширенных вариантов использования в разделе [Расширенные запросы](../samples/advanced.md)