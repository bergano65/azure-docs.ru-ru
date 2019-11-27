---
title: Краткое руководство Azure. Резервное копирование виртуальной машины с помощью шаблона Resource Manager
description: Узнайте, как создавать резервные копии виртуальных машин с помощью шаблона Azure Resource Manager.
author: dcurwin
manager: carmonm
ms.service: backup
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 05/14/2019
ms.author: dacurwin
ms.custom: mvc
ms.openlocfilehash: 5f027926a7b90ac99b04456ad3635466a6e27655
ms.sourcegitcommit: b1c94635078a53eb558d0eb276a5faca1020f835
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/27/2019
ms.locfileid: "72968790"
---
# <a name="back-up-a-virtual-machine-in-azure-with-resource-manager-template"></a>Резервное копирование виртуальной машины в Azure с помощью шаблона Resource Manager

[Azure Backup](backup-overview.md) создает резервные копии локальных компьютеров, приложений и виртуальных машин Azure. В этой статье описывается резервное копирование виртуальной машины Azure с помощью шаблона Resource Manager и Azure PowerShell. В данном кратком руководстве рассматривается преимущественно процесс развертывания шаблона Resource Manager для создания хранилища Служб восстановления. Дополнительные сведения о разработке шаблонов Resource Manager см. в разделе [Документация по Azure Resource Manager](/azure/azure-resource-manager/) и в [справочнике по шаблонам](/azure/templates/microsoft.recoveryservices/allversions).

Кроме того, вы можете выполнить резервное копирование виртуальной машины с помощью [Azure PowerShell](./quick-backup-vm-powershell.md), [Azure CLI](quick-backup-vm-cli.md) или [портала Azure](quick-backup-vm-portal.md).

## <a name="create-a-vm-and-recovery-services-vault"></a>Создание виртуальной машины и хранилища Служб восстановления.

[Хранилище служб восстановления](backup-azure-recovery-services-vault-overview.md) — это логический контейнер, в котором хранятся данные резервного копирования защищенных ресурсов, например виртуальных машин Azure. Когда выполняется задание резервного копирования, в хранилище Служб восстановления создается точка восстановления. Позже вы сможете использовать одну из этих точек восстановления, чтобы восстановить данные до определенной точки во времени.

Шаблон, используемый в этом кратком руководстве, взят из [шаблонов быстрого запуска Azure](https://azure.microsoft.com/resources/templates/101-recovery-services-create-vm-and-configure-backup/). Этот шаблон позволяет развернуть простую виртуальную машину Windows и хранилище Служб восстановления с конфигурацией DefaultPolicy, имеющей значение "Protection" (Защита).

Для развертывания шаблона выберите **Попробовать**, чтобы открыть Azure Cloud Shell, а затем вставьте следующий сценарий PowerShell в окно оболочки. Чтобы вставить код, щелкните окно оболочки правой кнопкой мыши, а затем выберите **Вставить**.

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

В этом кратком руководстве для развертывания шаблона Resource Manager используется Azure PowerShell. [Портал Azure](../azure-resource-manager/resource-group-template-deploy-portal.md), [Azure CLI](../azure-resource-manager/resource-group-template-deploy-cli.md) и [REST API](../azure-resource-manager/resource-group-template-deploy-rest.md) также можно использовать для развертывания шаблонов.

## <a name="start-a-backup-job"></a>Запуск задания резервного копирования

Шаблон создает виртуальную машину и включает на ней резервное копирование. После развертывания шаблона необходимо запустить задание резервного копирования. Дополнительные сведения см. в разделе [Запуск задания резервного копирования](./quick-backup-vm-powershell.md#start-a-backup-job).

## <a name="monitor-the-backup-job"></a>Мониторинг задания резервного копирования

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

## <a name="next-steps"></a>Дополнительная информация

В этом кратком руководстве вы создали хранилище служб восстановления, включили защиту на виртуальной машине и создали начальную точку восстановления.

- [Узнайте, как](tutorial-backup-vm-at-scale.md) создать резервные копии виртуальных машин на портале Azure.
- [Узнайте, как](tutorial-restore-disk.md) быстро восстановить виртуальную машину.
