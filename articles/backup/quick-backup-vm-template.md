---
title: Краткое руководство по резервному копированию виртуальной машины шаблона Resource Manager
description: Узнайте, как создавать резервные копии виртуальных машин с помощью шаблона Azure Resource Manager.
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 05/14/2019
ms.custom: mvc,subject-armqs
ms.openlocfilehash: d6fb73801f0f460daf2ed70f8dc88187e41ea887
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/29/2020
ms.locfileid: "81458851"
---
# <a name="back-up-a-virtual-machine-in-azure-with-resource-manager-template"></a>Резервное копирование виртуальной машины в Azure с помощью шаблона Resource Manager

[Azure Backup](backup-overview.md) создает резервные копии локальных компьютеров, приложений и виртуальных машин Azure. В этой статье описывается резервное копирование виртуальной машины Azure с помощью шаблона Resource Manager и Azure PowerShell. В данном кратком руководстве рассматривается преимущественно процесс развертывания шаблона Resource Manager для создания хранилища Служб восстановления. Дополнительные сведения о разработке шаблонов Resource Manager см. в разделе [Документация по Azure Resource Manager](/azure/azure-resource-manager/) и в [справочнике по шаблонам](/azure/templates/microsoft.recoveryservices/allversions).

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Кроме того, вы можете выполнить резервное копирование виртуальной машины с помощью [Azure PowerShell](./quick-backup-vm-powershell.md), [Azure CLI](quick-backup-vm-cli.md) или [портала Azure](quick-backup-vm-portal.md).

## <a name="create-a-vm-and-recovery-services-vault"></a>Создание виртуальной машины и хранилища Служб восстановления.

[Хранилище служб восстановления](backup-azure-recovery-services-vault-overview.md) — это логический контейнер, в котором хранятся данные резервного копирования защищенных ресурсов, например виртуальных машин Azure. Когда выполняется задание резервного копирования, в хранилище Служб восстановления создается точка восстановления. Позже вы сможете использовать одну из этих точек восстановления, чтобы восстановить данные до определенной точки во времени.

### <a name="review-the-template"></a>Изучение шаблона

Шаблон, используемый в этом кратком руководстве, взят из [шаблонов быстрого запуска Azure](https://azure.microsoft.com/resources/templates/101-recovery-services-create-vm-and-configure-backup/). Этот шаблон позволяет развернуть простую виртуальную машину Windows и хранилище Служб восстановления с конфигурацией DefaultPolicy, имеющей значение "Protection" (Защита).

:::code language="json" source="~/quickstart-templates/101-recovery-services-create-vm-and-configure-backup/azuredeploy.json" range="1-247" highlight="221-245":::

В шаблоне определены следующие ресурсы:

- [**Microsoft.Storage/storageAccounts**](/azure/templates/microsoft.storage/storageaccounts)
- [**Microsoft.Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses)
- [**Microsoft.Network/networkSecurityGroups;** ](/azure/templates/microsoft.network/networksecuritygroups)
- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks);
- [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces)
- [**Microsoft.Compute/virutalMachines**](/azure/templates/microsoft.compute/virtualmachines)
- [**Microsoft.RecoveryServices/vaults**](/azure/templates/microsoft.recoveryservices/2016-06-01/vaults)
- [**Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems**](/azure/templates/microsoft.recoveryservices/2016-06-01/vaults/backupfabrics/protectioncontainers/protecteditems)

### <a name="deploy-the-template"></a>Развертывание шаблона

Для развертывания шаблона щелкните **Попробовать**, чтобы открыть Azure Cloud Shell, а затем вставьте следующий скрипт PowerShell в окно оболочки. Чтобы вставить код, щелкните окно оболочки правой кнопкой мыши, а затем выберите **Вставить**.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name (limited to eight characters) that is used to generate Azure resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$adminUsername = Read-Host -Prompt "Enter the administrator username for the virtual machine"
$adminPassword = Read-Host -Prompt "Enter the administrator password for the virtual machine" -AsSecureString
$dnsPrefix = Read-Host -Prompt "Enter the unique DNS Name for the Public IP used to access the virtual machine"

$resourceGroupName = "${projectName}rg"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-recovery-services-create-vm-and-configure-backup/azuredeploy.json"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -projectName $projectName -adminUsername $adminUsername -adminPassword $adminPassword -dnsLabelPrefix $dnsPrefix
```

В этом кратком руководстве для развертывания шаблона Resource Manager используется Azure PowerShell. [Портал Azure](../azure-resource-manager/templates/deploy-portal.md), [Azure CLI](../azure-resource-manager/templates/deploy-cli.md) и [REST API](../azure-resource-manager/templates/deploy-rest.md) также можно использовать для развертывания шаблонов.

## <a name="validate-the-deployment"></a>Проверка развертывания

### <a name="start-a-backup-job"></a>Запуск задания резервного копирования

Шаблон создает виртуальную машину и включает на ней резервное копирование. После развертывания шаблона необходимо запустить задание резервного копирования. Дополнительные сведения см. в разделе [Запуск задания резервного копирования](./quick-backup-vm-powershell.md#start-a-backup-job).

### <a name="monitor-the-backup-job"></a>Мониторинг задания резервного копирования

Мониторинг задания резервного копирования описывается в разделе [Мониторинг задания резервного копирования](./quick-backup-vm-powershell.md#monitor-the-backup-job).

## <a name="clean-up-the-deployment"></a>Очистка развертывания

Если вам больше не требуется резервная копия виртуальной машины, ее можно удалить.

- Если вы хотите восстановить виртуальною машину, пропустите процедуру очистки.
- Если вы использовали имеющуюся виртуальную машину, можете пропустить последний командлет [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup), чтобы оставить группу ресурсов и виртуальную машину.

Отключите защиту, удалите точки восстановления и хранилище. Затем удалите группу ресурсов и связанные ресурсы виртуальной машины следующим образом:

```powershell
Disable-AzRecoveryServicesBackupProtection -Item $item -RemoveRecoveryPoints
$vault = Get-AzRecoveryServicesVault -Name "myRecoveryServicesVault"
Remove-AzRecoveryServicesVault -Vault $vault
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="next-steps"></a>Дальнейшие действия

В этом кратком руководстве вы создали хранилище служб восстановления, включили защиту на виртуальной машине и создали начальную точку восстановления.

- [Узнайте, как](tutorial-backup-vm-at-scale.md) создать резервные копии виртуальных машин на портале Azure.
- [Узнайте, как](tutorial-restore-disk.md) быстро восстановить виртуальную машину.
- Узнайте, как [создать шаблоны Resource Manager](../azure-resource-manager/templates/template-tutorial-create-first-template.md).
