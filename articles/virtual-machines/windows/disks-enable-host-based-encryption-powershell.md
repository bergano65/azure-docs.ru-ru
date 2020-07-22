---
title: Azure PowerShell — Включение сквозного шифрования на узле виртуальной машины
description: Как включить сквозное шифрование для виртуальных машин Azure с помощью шифрования на узле.
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 07/10/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: a13c17b2fcd6a42947b2f7d97f087735a5b18204
ms.sourcegitcommit: f7e160c820c1e2eb57dc480b2a8fd6bef7053e91
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/10/2020
ms.locfileid: "86236029"
---
# <a name="enable-end-to-end-encryption-using-encryption-at-host---azure-powershell"></a>Включение сквозного шифрования с помощью шифрования на узле Azure PowerShell

При включении шифрования на узле данные, хранящиеся на узле виртуальной машины, шифруются в неактивных потоках и передаются в зашифрованном виде в службу хранилища. Концептуальные сведения о шифровании на узле, а также о других типах шифрования управляемых дисков см. [в статье шифрование на основе шифрования узла для данных виртуальной машины](disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data).

## <a name="restrictions"></a>Ограничения

[!INCLUDE [virtual-machines-disks-encryption-at-host-restrictions](../../../includes/virtual-machines-disks-encryption-at-host-restrictions.md)]

### <a name="supported-regions"></a>Поддерживаемые регионы

[!INCLUDE [virtual-machines-disks-encryption-at-host-regions](../../../includes/virtual-machines-disks-encryption-at-host-regions.md)]

### <a name="supported-vm-sizes"></a>Поддерживаемые размеры виртуальных машин

[!INCLUDE [virtual-machines-disks-encryption-at-host-suported-sizes](../../../includes/virtual-machines-disks-encryption-at-host-suported-sizes.md)]

Размеры виртуальных машин также можно найти программно. Чтобы узнать, как получить их программно, ознакомьтесь с разделом [Поиск поддерживаемых размеров виртуальных машин](#finding-supported-vm-sizes) .

## <a name="prerequisites"></a>Обязательные условия

Чтобы иметь возможность использовать шифрование на узле для виртуальных машин или масштабируемых наборов виртуальных машин, необходимо включить эту функцию в подписке. Отправьте сообщение электронной почты по адресу encryptionAtHost@microsoft . com с идентификаторами подписки, чтобы включить функцию для ваших подписок.

### <a name="create-an-azure-key-vault-and-diskencryptionset"></a>Создание Azure Key Vault и Дискенкриптионсет

После включения этой функции необходимо настроить Azure Key Vault и Дискенкриптионсет, если вы еще этого не сделали.

[!INCLUDE [virtual-machines-disks-encryption-create-key-vault-powershell](../../../includes/virtual-machines-disks-encryption-create-key-vault-powershell.md)]

## <a name="enable-encryption-at-host-for-disks-attached-to-vm-and-virtual-machine-scale-sets"></a>Включить шифрование на узле для дисков, подключенных к виртуальным машинам и масштабируемым наборам виртуальных машин

Вы можете включить шифрование на узле, задав новое свойство Енкриптионасост в Секуритипрофиле виртуальных машин или масштабируемых наборов виртуальных машин с помощью API-интерфейса **2020-06-01** и более поздних версий.

`"securityProfile": { "encryptionAtHost": "true" }`

## <a name="example-scripts"></a>Примеры скриптов

### <a name="enable-encryption-at-host-for-disks-attached-to-a-vm-with-customer-managed-keys"></a>Включить шифрование на узле для дисков, подключенных к виртуальной машине с помощью управляемых клиентом ключей

Создайте виртуальную машину с управляемыми дисками с помощью URI ресурса Дискенкриптионсет, созданного ранее.

Замените `<yourPassword>` , `<yourVMName>` , `<yourVMSize>` , `<yourDESName>` , `<yoursubscriptionID>` , `<yourResourceGroupName>` , и `<yourRegion>` , а затем запустите скрипт.

```PowerShell
$password=ConvertTo-SecureString -String "<yourPassword>" -AsPlainText -Force
New-AzResourceGroupDeployment -ResourceGroupName <yourResourceGroupName> `
-TemplateUri "https://raw.githubusercontent.com/Azure-Samples/managed-disks-powershell-getting-started/master/EncryptionAtHost/CreateVMWithDisksEncryptedAtHostWithCMK.json" `
-virtualMachineName "<yourVMName>" `
-adminPassword $password `
-vmSize "<yourVMSize>" `
-diskEncryptionSetId "/subscriptions/<yoursubscriptionID>/resourceGroups/<yourResourceGroupName>/providers/Microsoft.Compute/diskEncryptionSets/<yourDESName>" `
-region "<yourRegion>"
```

### <a name="enable-encryption-at-host-for-disks-attached-to-a-vm-with-platform-managed-keys"></a>Включить шифрование на узле для дисков, подключенных к виртуальной машине с помощью ключей, управляемых платформой

Замените `<yourPassword>` , `<yourVMName>` , `<yourVMSize>` , `<yourResourceGroupName>` и `<yourRegion>` , а затем запустите скрипт.

```PowerShell
$password=ConvertTo-SecureString -String "<yourPassword>" -AsPlainText -Force
New-AzResourceGroupDeployment -ResourceGroupName <yourResourceGroupName> `
-TemplateUri "https://raw.githubusercontent.com/Azure-Samples/managed-disks-powershell-getting-started/master/EncryptionAtHost/CreateVMWithDisksEncryptedAtHostWithPMK.json" `
-virtualMachineName "<yourVMName>" `
-adminPassword $password `
-vmSize "<yourVMSize>" `
-region "<yourRegion>"
```

## <a name="finding-supported-vm-sizes"></a>Поиск поддерживаемых размеров виртуальных машин

Размеры виртуальных машин прежних версий не поддерживаются. Список поддерживаемых размеров виртуальных машин можно найти одним из следующих форматов:

Вызов [API SKU ресурсов](https://docs.microsoft.com/rest/api/compute/resourceskus/list) и проверка `EncryptionAtHostSupported` возможности установки значения **true**.

```json
    {
        "resourceType": "virtualMachines",
        "name": "Standard_DS1_v2",
        "tier": "Standard",
        "size": "DS1_v2",
        "family": "standardDSv2Family",
        "locations": [
        "CentralUSEUAP"
        ],
        "capabilities": [
        {
            "name": "EncryptionAtHostSupported",
            "value": "True"
        }
        ]
    }
```

Либо вызовите командлет PowerShell [Get-азкомпутересаурцеску](https://docs.microsoft.com/powershell/module/az.compute/get-azcomputeresourcesku?view=azps-3.8.0) .

```powershell
$vmSizes=Get-AzComputeResourceSku | where{$_.ResourceType -eq 'virtualMachines' -and $_.Locations.Contains('CentralUSEUAP')} 

foreach($vmSize in $vmSizes)
{
    foreach($capability in $vmSize.capabilities)
    {
        if($capability.Name -eq 'EncryptionAtHostSupported' -and $capability.Value -eq 'true')
        {
            $vmSize

        }

    }
}
```

## <a name="next-steps"></a>Дальнейшие действия

Теперь, когда вы создали и настроили эти ресурсы, их можно использовать для защиты управляемых дисков. Следующая ссылка содержит примеры сценариев, каждый из которых имеет соответствующий сценарий, который можно использовать для защиты управляемых дисков.

[Примеры шаблонов Azure Resource Manager](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/tree/master/EncryptionAtHost)