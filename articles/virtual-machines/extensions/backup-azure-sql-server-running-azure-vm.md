---
title: Резервное копирование Azure для сервера S'L, работающего в Azure VM
description: В этой статье узнайте, как зарегистрировать резервное копирование Azure в сервере S'L, работая в виртуальной машине Azure.
services: backup
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: dacurwin
ms.openlocfilehash: b17e4031edaedc6b0a63d305d20a77e5b58f91ba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80247390"
---
# <a name="azure-backup-for-sql-server-running-in-azure-vm"></a>Резервное копирование Azure для сервера S'L, работающего в Azure VM

Резервное копирование Azure, среди других предложений, обеспечивает поддержку резервного копирования рабочих нагрузок, таких как сервер S'L, работающий в Azure VMs. Так как приложение S'L работает в azure VM, служба резервного копирования нуждается в разрешении на доступ к приложению и получение необходимых деталей.
Для этого Azure Backup устанавливает расширение **AzureBackupWindowsWorkload** на VM, в котором работает сервер S'L, во время процесса регистрации, инициированного пользователем.

## <a name="prerequisites"></a>Предварительные требования

Для списка поддерживаемых сценариев обратитесь к [матрице поддержки,](../../backup/sql-support-matrix.md#scenario-support) поддерживаемой резервным копированием Azure.

## <a name="network-connectivity"></a>Сетевое подключение

Резервное копирование Azure поддерживает теги NSG, развертывание прокси-сервера или перечисленных диапазонов IP; для получения подробной информации о каждом из методов, обратитесь в эту [статью](https://docs.microsoft.com/azure/backup/backup-sql-server-database-azure-vms#establish-network-connectivity).

## <a name="extension-schema"></a>Схема расширения

Схема расширения и значения свойств — это значения конфигурации (настройки времени выполнения), которые служба передает aPI CRP. Эти значения конфигурации используются во время регистрации и обновления. **Расширение AzureBackupWindowsWorkload** также использует эту схему. Схема предварительно установлена; новый параметр может быть добавлен в поле objectStr

  ```json
      "runtimeSettings": [{
      "handlerSettings": {
      "protectedSettingsCertThumbprint": "",
      "protectedSettings": {
      "objectStr": "",
      "logsBlobUri": "",
      "statusBlobUri": ""
      }
      },
      "publicSettings": {
      "locale": "en-us",
      "taskId": "1c0ae461-9d3b-418c-a505-bb31dfe2095d",
      "objectStr": "",
      "commandStartTimeUTCTicks": "636295005824665976",
      "vmType": "vmType"
      }
      }]
      }
  ```

Следующие JSON показывает схему для расширения WorkloadBackup.  

  ```json
  {
    "type": "extensions",
    "name": "WorkloadBackup",
    "location":"<myLocation>",
    "properties": {
      "publisher": "Microsoft.RecoveryServices",
      "type": "AzureBackupWindowsWorkload",
      "typeHandlerVersion": "1.1",
      "autoUpgradeMinorVersion": true,
      "settings": {
        "locale":"<location>",
        "taskId":"<TaskId used by Azure Backup service to communicate with extension>",

        "objectStr": "<The configuration passed by Azure Backup service to extension>",

        "commandStartTimeUTCTicks": "<Scheduled start time of registration or upgrade task>",
        "vmType": "<Type of VM where registration got triggered Eg. Compute or ClassicCompute>"
      },
      "protectedSettings": {
        "objectStr": "<The sensitive configuration passed by Azure Backup service to extension>",
        "logsBlobUri": "<blob uri where logs of command execution by extension are written to>",
        "statusBlobUri": "<blob uri where status of the command executed by extension is written>"
      }
    }
  }
  ```

### <a name="property-values"></a>Значения свойств

name | Значение/пример | Тип данных
 --- | --- | ---
локаль | ru-ru  |  строка
taskId | "1c0ae461-9d3b-418c-a505-bb31dfe2095d"  | строка
objectStr <br/> (публичныеПостановки)  | "eyJjb250YWlu-XJ-cm9w-XJ0aWzIjp7IkNbRhaW5lcklEjoiMzVjMj'xYTItItIRjNy00-GE5LWI4NTMtMjyTJHND-LM2ФПБСВСВЗИОйМ0НТИ3ОДГ5ЛКС-XNdXJ-UlkIjoiMDU5NWIwOGEtYzI4зи00-mFlLWE5ODItOTkwOWMyMGVJNjVhIiwiW3Vic2NyAXB0aW9KNGNGEzOTliNy1iYJAyLT-2MWMtODdmYS1jNTM5ODI3-TgzNT-iLCJVbmlxdWDb250YW-XJOYW1lIjoODM4MD-jODUtNT-4OS00NMjg3OTcyIn0sInN0YW1wTGlzdCI6W3siU2Видмлж-ЗУ5хбВУиОйОйИзИлНcn-ПЖи2ВТДГФКФВИБCI6Imh0dHA6XC9cL015V00xGYWJTdmM29tIndf ==" | строка
commandStartTimeUTCTicks | "636967192566036845"  | строка
vmType  | "Microsoft.compute/virtualmachines"  | строка
objectStr <br/> (защищенныеНастройки) | "eyJjb250YWlu-XJ-cm9w-XJ0aWzIjp7IkNbRhaW5lcklEjoiMzVjMj'xYTItItIRjNy00-GE5LWI4NTMtMjyTJHND-LM2ФПБСВСВЗИОйМ0НТИ3ОДГ5ЛКС-XNdXJ-UlkIjoiMDU5NWIwOGEtYzI4зи00-mFlLWE5ODItOTkwOWMyMGVJNjVhIiwiW3Vic2NyAXB0aW9KNGNGEzOTliNy1iYJAyLT-2MWMtODdmYS1jNTM5ODI3-TgzNT-iLCJVbmlxdWDb250YW-XJOYW1lIjoODM4MD-jODUtNT-4OS00NMjg3OTcyIn0sInN0YW1wTGlzdCI6W3siU2Видмлж-ЗУ5хбВУиОйОйИзИлНcn-ПЖи2ВТДГФКФВИБCI6Imh0dHA6XC9cL015V00xGYWJTdmM29tIndf ==" | строка
logsBlobUri | <https://seapod01coord1exsapk732.blob.core.windows.net/bcdrextensionlogs-d45d8a1c-281e-4bc8-9d30-3b25176f68ea/sopattna-vmubuntu1404ltsc.v2.Logs.txt?sv=2014-02-14&sr=b&sig=DbwYhwfeAC5YJzISgxoKk%2FEWQq2AO1vS1E0rDW%2FlsBw%3D&st=2017-11-09T14%3A33%3A29Z&se=2017-11-09T17%3A38%3A29Z&sp=rw> | строка
statusBlobUri | <https://seapod01coord1exsapk732.blob.core.windows.net/bcdrextensionlogs-d45d8a1c-281e-4bc8-9d30-3b25176f68ea/sopattna-vmubuntu1404ltsc.v2.Status.txt?sv=2014-02-14&sr=b&sig=96RZBpTKCjmV7QFeXm5IduB%2FILktwGbLwbWg6Ih96Ao%3D&st=2017-11-09T14%3A33%3A29Z&se=2017-11-09T17%3A38%3A29Z&sp=rw> | строка

## <a name="template-deployment"></a>Развертывание шаблона

Мы рекомендовали добавить расширение AzureBackupWindowsWorkLoad в виртуальную машину, включив резервное копирование сервера s'L на виртуальной машине. Это может быть достигнуто с помощью [шаблона Resource Manager,](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-vm-workload-backup) предназначенного для автоматизации резервного копирования на VM сервера S'L.

## <a name="powershell-deployment"></a>Развертывание с помощью PowerShell

Необходимо «зарегистрировать» VM Azure, содержащее приложение S'L с хранилищем служб восстановления. Во время регистрации на VM устанавливается расширение AzureBackupWindowsWorkload. Для регистрации VM используйте [Регистр-AzRecoveryServicesBackupContainerPS](https://docs.microsoft.com/powershell/module/az.recoveryservices/Register-AzRecoveryServicesBackupContainer?view=azps-1.5.0) cmdlet.

```powershell
$myVM = Get-AzVM -ResourceGroupName <VMRG Name> -Name <VMName>
Register-AzRecoveryServicesBackupContainer -ResourceId $myVM.ID -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetVault.ID -Force
```

Команда вернет **резервный контейнер** этого ресурса и статус будет **зарегистрирован.**

## <a name="next-steps"></a>Дальнейшие действия

- [Узнайте больше](https://docs.microsoft.com/azure/backup/backup-sql-server-azure-troubleshoot) о рекомендациях по устранению неполадок резервного копирования в Azure S'L Server VM
- [Общие вопросы](https://docs.microsoft.com/azure/backup/faq-backup-sql-server) о резервном копировании баз данных сервера S'L Server, которые работают на виртуальных машинах Azure (VM) и которые используют службу резервного копирования Azure.
