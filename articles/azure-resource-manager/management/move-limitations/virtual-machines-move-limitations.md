---
title: Перемещение виртуальных машин Azure в новую подписку или группу ресурсов
description: Используйте Azure Resource Manager, чтобы переместить виртуальные машины в новую группу ресурсов или подписку.
ms.topic: conceptual
ms.date: 08/31/2020
ms.openlocfilehash: 3878113f6874c40953bec87518a89519bdc6cb1a
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/01/2020
ms.locfileid: "89230965"
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
   4. Найдите ресурс с типом **Microsoft. COMPUTE/ресторепоинтколлектионс** , который имеет шаблон именования `AzureBackup_<name of your VM that you're trying to move>_###########` .
   5. Удалите этот ресурс. Эта операция удаляет только точки мгновенного восстановления, но не данные резервных копий в хранилище.
   6. После завершения операции удаления можно переместить виртуальную машину.

3. Переместите виртуальную машину в целевую группу ресурсов.
4. Возобновите резервное копирование.

### <a name="powershell"></a>PowerShell

1. Найдите расположение виртуальной машины.

1. Найдите группу ресурсов с шаблоном именования `AzureBackupRG_<VM location>_1` . Например, имя может иметь значение `AzureBackupRG_westus2_1` .

1. Используйте следующую команду для получения коллекции точек восстановления.

   ```azurepowershell
   $RestorePointCollection = Get-AzResource -ResourceGroupName AzureBackupRG_<VM location>_1 -ResourceType Microsoft.Compute/restorePointCollections
   ```

1. Удалите этот ресурс. Эта операция удаляет только точки мгновенного восстановления, но не данные резервных копий в хранилище.

   ```azurepowershell
   Remove-AzResource -ResourceId $RestorePointCollection.ResourceId -Force
   ```

### <a name="azure-cli"></a>Azure CLI

1. Найдите расположение виртуальной машины.

1. Найдите группу ресурсов с шаблоном именования `AzureBackupRG_<VM location>_1` . Например, имя может иметь значение `AzureBackupRG_westus2_1` .

1. Используйте следующую команду, чтобы получить коллекцию точек восстановления.

   ```azurecli
   az resource list -g AzureBackupRG_<VM location>_1 --resource-type Microsoft.Compute/restorePointCollections
   ```

1. Поиск идентификатора ресурса для ресурса с помощью шаблона именования `AzureBackup_<VM name>_###########`

1. Удалите этот ресурс. Эта операция удаляет только точки мгновенного восстановления, но не данные резервных копий в хранилище.

   ```azurecli
   az resource delete --ids /subscriptions/<sub-id>/resourceGroups/<resource-group>/providers/Microsoft.Compute/restorePointCollections/<name>
   ```

## <a name="next-steps"></a>Дальнейшие действия

* Команды для перемещения ресурсов см. в статье [Перемещение ресурсов в новую группу ресурсов или подписку](../move-resource-group-and-subscription.md).

* Сведения о перемещении хранилищ служб восстановления для резервного копирования см. в разделе [Ограничения служб восстановления](../../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json).
