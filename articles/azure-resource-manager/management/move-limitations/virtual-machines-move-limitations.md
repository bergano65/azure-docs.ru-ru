---
title: Перемещение виртуальных машин Azure в новую подписку или группу ресурсов
description: Используйте Azure Resource Manager, чтобы переместить виртуальные машины в новую группу ресурсов или подписку.
ms.topic: conceptual
ms.date: 09/21/2020
ms.openlocfilehash: 219a8b438d2715f6e97085a527b386e51759ec2c
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91317112"
---
# <a name="move-guidance-for-virtual-machines"></a>Перемещение руководств для виртуальных машин

В этой статье описываются сценарии, которые сейчас не поддерживаются, и действия по перемещению виртуальных машин с помощью резервного копирования.

## <a name="scenarios-not-supported"></a>Сценарии не поддерживаются

Следующие сценарии пока не поддерживаются.

* Невозможно переместить масштабируемые наборы виртуальных машин с Load Balancer SKU "Стандартный" или общедоступный IP-адрес SKU "Стандартный".
* Виртуальные машины, созданные из ресурсов Marketplace с подключенными планами, нельзя перемещать между подписками. Отмените подготовку виртуальной машины в текущей подписке и повторите развертывание в новой подписке.
* Виртуальные машины в существующей виртуальной сети нельзя переместить в новую подписку, если вы не перемещаете все ресурсы в виртуальной сети.
* Виртуальные машины с низким приоритетом и масштабируемые наборы виртуальных машин с низким приоритетом нельзя перемещать между группами ресурсов или подписками.
* Виртуальные машины в группе доступности нельзя перемещать по отдельности.

## <a name="azure-disk-encryption"></a>Шифрование дисков Azure

Вы не можете переместить виртуальную машину, интегрированную с хранилищем ключей, для реализации [шифрования дисков Azure для виртуальных машин Linux](../../../virtual-machines/linux/disk-encryption-overview.md) или [шифрования дисков Azure для виртуальных машин Windows](../../../virtual-machines/windows/disk-encryption-overview.md). Чтобы переместить виртуальную машину, необходимо отключить шифрование.

```azurecli-interactive
az vm encryption disable --resource-group demoRG --name myVm1
```

```azurepowershell-interactive
Disable-AzVMDiskEncryption -ResourceGroupName demoRG -VMName myVm1
```

## <a name="virtual-machines-with-azure-backup"></a>Виртуальные машины с Azure Backup

Чтобы переместить виртуальные машины, настроенные с Azure Backup, необходимо удалить точки восстановления из хранилища.

Если для виртуальной машины включено [обратимое удаление](../../../backup/backup-azure-security-feature-cloud.md) , вы не сможете переместить виртуальную машину во время хранения этих точек восстановления. [Отключите обратимое удаление](../../../backup/backup-azure-security-feature-cloud.md#enabling-and-disabling-soft-delete) или подождите 14 дней после удаления точек восстановления.

### <a name="portal"></a>Портал

1. Временно остановить резервное копирование и хранить данные резервной копии.
2. Чтобы переместить виртуальные машины, настроенные с помощью Azure Backup, выполните следующие действия.

   1. Найдите расположение виртуальной машины.
   2. Найдите группу ресурсов со следующим шаблоном именования: `AzureBackupRG_<VM location>_1` . Например, имя имеет формат *AzureBackupRG_westus2_1*.
   3. В портал Azure установите флажок **Показывать скрытые типы**.
   4. Найдите ресурс с типом **Microsoft. COMPUTE/ресторепоинтколлектионс** , который имеет шаблон именования `AzureBackup_<VM name>_###########` .
   5. Удалите этот ресурс. Эта операция удаляет только точки мгновенного восстановления, но не данные резервных копий в хранилище.
   6. После завершения операции удаления можно переместить виртуальную машину.

3. Переместите виртуальную машину в целевую группу ресурсов.
4. Возобновите резервное копирование.

### <a name="powershell"></a>PowerShell

1. Найдите расположение виртуальной машины.

1. Найдите группу ресурсов с шаблоном именования `AzureBackupRG_<VM location>_1` . Например, имя может иметь значение `AzureBackupRG_westus2_1` .

1. Если вы перемещаете только одну виртуальную машину, получите коллекцию точек восстановления для этой виртуальной машины.

   ```azurepowershell-interactive
   $restorePointCollection = Get-AzResource -ResourceGroupName AzureBackupRG_<VM location>_1 -name AzureBackup_<VM name>* -ResourceType Microsoft.Compute/restorePointCollections
   ```

   Удалите этот ресурс. Эта операция удаляет только точки мгновенного восстановления, но не данные резервных копий в хранилище.

   ```azurepowershell-interactive
   Remove-AzResource -ResourceId $restorePointCollection.ResourceId -Force
   ```

1. Если вы перемещаете все виртуальные машины с помощью резервных копий в этом расположении, получите коллекции точек восстановления для этих виртуальных машин.

   ```azurepowershell-interactive
   $restorePointCollection = Get-AzResource -ResourceGroupName AzureBackupRG_<VM location>_1 -ResourceType Microsoft.Compute/restorePointCollections
   ```

   Удалите каждый ресурс. Эта операция удаляет только точки мгновенного восстановления, но не данные резервных копий в хранилище.

   ```azurepowershell-interactive
   foreach ($restorePoint in $restorePointCollection)
   {
     Remove-AzResource -ResourceId $restorePoint.ResourceId -Force
   }
   ```

### <a name="azure-cli"></a>Azure CLI

1. Найдите расположение виртуальной машины.

1. Найдите группу ресурсов с шаблоном именования `AzureBackupRG_<VM location>_1` . Например, имя может иметь значение `AzureBackupRG_westus2_1` .

1. Если вы перемещаете только одну виртуальную машину, получите коллекцию точек восстановления для этой виртуальной машины.

   ```azurecli-interactive
   RESTOREPOINTCOL=$(az resource list -g AzureBackupRG_<VM location>_1 --resource-type Microsoft.Compute/restorePointCollections --query "[?starts_with(name, 'AzureBackup_<VM name>')].id" --output tsv)
   ```

   Удалите этот ресурс. Эта операция удаляет только точки мгновенного восстановления, но не данные резервных копий в хранилище.

   ```azurecli-interactive
   az resource delete --ids $RESTOREPOINTCOL
   ```

1. Если вы перемещаете все виртуальные машины с помощью резервных копий в этом расположении, получите коллекции точек восстановления для этих виртуальных машин.

   ```azurecli-interactive
   RESTOREPOINTCOL=$(az resource list -g AzureBackupRG_<VM location>_1 --resource-type Microsoft.Compute/restorePointCollections)
   ```

   Удалите каждый ресурс. Эта операция удаляет только точки мгновенного восстановления, но не данные резервных копий в хранилище.

   ```azurecli-interactive
   az resource delete --ids $RESTOREPOINTCOL
   ```

## <a name="next-steps"></a>Дальнейшие действия

* Команды для перемещения ресурсов см. в статье [Перемещение ресурсов в новую группу ресурсов или подписку](../move-resource-group-and-subscription.md).

* Сведения о перемещении хранилищ служб восстановления для резервного копирования см. в разделе [Ограничения служб восстановления](../../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json).
