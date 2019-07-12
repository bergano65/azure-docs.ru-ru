---
title: Служба архивации Azure для SQL Server, запущенный на виртуальной Машине Azure
description: Как зарегистрировать Azure резервного копирования SQL Server, запущенный на виртуальной Машине Azure
services: backup
author: swatisachdeva
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: swatisachdeva
ms.openlocfilehash: 8710242e04156c8af6e5882a3cb4d42cc31e3677
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/05/2019
ms.locfileid: "67607615"
---
# <a name="azure-backup-for-sql-server-running-in-azure-vm"></a>Служба архивации Azure для SQL Server, запущенный на виртуальной Машине Azure

Служба архивации Azure, среди других предложений поддерживает резервное копирование рабочих нагрузок, таких как SQL Server на виртуальных машинах Azure. Так как приложение SQL выполняется на виртуальной Машине Azure, службы резервного копирования необходимо предоставить разрешение на доступ к приложению и получить необходимые сведения.
Чтобы сделать это, Azure Backup устанавливает **AzureBackupWindowsWorkload** расширение на виртуальной Машине, в котором выполняется SQL Server, во время процесса регистрации, запущенными пользователем.

## <a name="prerequisites"></a>предварительные требования

Список поддерживаемых сценариев, см. в разделе [Матрица поддержки](https://docs.microsoft.com/azure/backup/backup-azure-sql-database#scenario-support) поддерживается службой Azure Backup.

## <a name="network-connectivity"></a>Сетевое подключение

Служба архивации Azure поддерживает теги группы безопасности сети, развертывание прокси-сервер или список диапазонов IP-адрес; Дополнительные сведения о каждом методе, ссылки на эти [статье](https://docs.microsoft.com/azure/backup/backup-sql-server-database-azure-vms#establish-network-connectivity).

## <a name="extension-schema"></a>Схема расширения

Расширение схемы и значения свойств являются значения конфигурации (параметры среды выполнения), служба передает CRP API. Эти значения конфигурации используются во время регистрации и обновления. **AzureBackupWindowsWorkload** расширения также использовать эту схему. Предварительно набора схем; можно добавить новый параметр в поле objectStr

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

Приведенный ниже код JSON показана схема для расширения WorkloadBackup.  

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

Имя | Значение/example | Тип данных
 --- | --- | ---
локаль | en-us  |  строка
taskId | "1c0ae461-9d3b-418c-a505-bb31dfe2095d"  | строка
objectStr <br/> (publicSettings)  | "eyJjb250YWluZXJQcm9wZXJ0aWVzIjp7IkNvbnRhaW5lcklEIjoiMzVjMjQxYTItOGRjNy00ZGE5LWI4NTMtMjdjYTJhNDZlM2ZkIiwiSWRNZ210Q29udGFpbmVySWQiOjM0NTY3ODg5LCJSZXNvdXJjZUlkIjoiMDU5NWIwOGEtYzI4Zi00ZmFlLWE5ODItOTkwOWMyMGVjNjVhIiwiU3Vic2NyaXB0aW9uSWQiOiJkNGEzOTliNy1iYjAyLTQ2MWMtODdmYS1jNTM5O DI3ZTgzNTQiLCJVbmlxdWVDb250YWluZXJOYW1lIjoiODM4MDZjODUtNTQ4OS00NmNhLWEyZTctNWMzNzNhYjg3OTcyIn0sInN0YW1wTGlzdCI6W3siU2VydmljZU5hbWUiOjUsIlNlcnZpY2VTdGFtcFVybCI6Imh0dHA6XC9cL015V0xGYWJTdmMuY29tIn1dfQ ==» | строка
commandStartTimeUTCTicks | "636967192566036845"  | строка
vmType  | "microsoft.compute/virtualmachines"  | строка
objectStr <br/> (protectedSettings) | "eyJjb250YWluZXJQcm9wZXJ0aWVzIjp7IkNvbnRhaW5lcklEIjoiMzVjMjQxYTItOGRjNy00ZGE5LWI4NTMtMjdjYTJhNDZlM2ZkIiwiSWRNZ210Q29udGFpbmVySWQiOjM0NTY3ODg5LCJSZXNvdXJjZUlkIjoiMDU5NWIwOGEtYzI4Zi00ZmFlLWE5ODItOTkwOWMyMGVjNjVhIiwiU3Vic2NyaXB0aW9uSWQiOiJkNGEzOTliNy1iYjAyLTQ2MWMtODdmYS1jNTM5O DI3ZTgzNTQiLCJVbmlxdWVDb250YWluZXJOYW1lIjoiODM4MDZjODUtNTQ4OS00NmNhLWEyZTctNWMzNzNhYjg3OTcyIn0sInN0YW1wTGlzdCI6W3siU2VydmljZU5hbWUiOjUsIlNlcnZpY2VTdGFtcFVybCI6Imh0dHA6XC9cL015V0xGYWJTdmMuY29tIn1dfQ ==» | строка
logsBlobUri | https://seapod01coord1exsapk732.blob.core.windows.net/bcdrextensionlogs-d45d8a1c-281e-4bc8-9d30-3b25176f68ea/sopattna-vmubuntu1404ltsc.v2.Logs.txt?sv=2014-02-14&sr=b&sig=DbwYhwfeAC5YJzISgxoKk%2FEWQq2AO1vS1E0rDW%2FlsBw%3D&st=2017-11-09T14%3A33%3A29Z&se=2017-11-09T17%3A38%3A29Z&sp=rw | строка
statusBlobUri | https://seapod01coord1exsapk732.blob.core.windows.net/bcdrextensionlogs-d45d8a1c-281e-4bc8-9d30-3b25176f68ea/sopattna-vmubuntu1404ltsc.v2.Status.txt?sv=2014-02-14&sr=b&sig=96RZBpTKCjmV7QFeXm5IduB%2FILktwGbLwbWg6Ih96Ao%3D&st=2017-11-09T14%3A33%3A29Z&se=2017-11-09T17%3A38%3A29Z&sp=rw | строка


## <a name="template-deployment"></a>Развертывание шаблона

Мы рекомендуем, добавление AzureBackupWindowsWorkload расширения к виртуальной машине выполняется путем включения резервного копирования SQL Server на виртуальной машине. Это делается с помощью [шаблона Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-vm-workload-backup) предназначены для автоматизации резервного копирования на виртуальную Машину SQL Server.


## <a name="powershell-deployment"></a>Развертывание с помощью PowerShell

Необходимо зарегистрировать виртуальную Машину Azure, содержащий приложение SQL в хранилище служб восстановления. Во время регистрации AzureBackupWindowsWorkload расширение устанавливается на виртуальной Машине. Используйте [Register AzRecoveryServicesBackupContainerPS](https://docs.microsoft.com/powershell/module/az.recoveryservices/Register-AzRecoveryServicesBackupContainer?view=azps-1.5.0) командлет, чтобы зарегистрировать виртуальную Машину.
 
```powershell
$myVM = Get-AzVM -ResourceGroupName <VMRG Name> -Name <VMName>
Register-AzRecoveryServicesBackupContainer -ResourceId $myVM.ID -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetVault.ID -Force
```
 
Команда вернет **контейнер резервного копирования** этого ресурса и состояние будут **зарегистрирован**.


## <a name="next-steps"></a>Следующие шаги

- [Дополнительные сведения](https://docs.microsoft.com/azure/backup/backup-sql-server-azure-troubleshoot) о резервном копировании виртуальных Машин сервера SQL Azure рекомендации по устранению неполадок.
- [Часто задаваемые вопросы](https://docs.microsoft.com/azure/backup/faq-backup-sql-server) о резервном копировании баз данных SQL Server, выполняемых на виртуальных машинах (ВМ) Azure и используют службу архивации Azure.
