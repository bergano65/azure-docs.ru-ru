---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/05/2019
ms.author: alkohli
ms.openlocfilehash: b657ee32e76dd90671f7e91337ced01b925889a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "67185746"
---
Если возникли проблемы с устройством, можно создать пакет поддержки из системных журналов. Корпорация Майкрософт использует этот пакет для устранения неполадок. Выполните следующие действия, чтобы создать пакет поддержки:

1. [Подключитесь к интерфейсу PowerShell вашего устройства.](#connect-to-the-powershell-interface)
2. Используйте `Get-HcsNodeSupportPackage` команду для создания пакета поддержки. Использование cmdlet заключается в следующем:

    ```powershell
    Get-HcsNodeSupportPackage [-Path] <string> [-Zip] [-ZipFileName <string>] [-Include {None | RegistryKeys | EtwLogs
            | PeriodicEtwLogs | LogFiles | DumpLog | Platform | FullDumps | MiniDumps | ClusterManagementLog | ClusterLog |
            UpdateLogs | CbsLogs | StorageCmdlets | ClusterCmdlets | ConfigurationCmdlets | KernelDump | RollbackLogs |
            Symbols | NetworkCmdlets | NetworkCmds | Fltmc | ClusterStorageLogs | UTElement | UTFlag | SmbWmiProvider |
            TimeCmds | LocalUILogs | ClusterHealthLogs | BcdeditCommand | BitLockerCommand | DirStats | ComputeRolesLogs |
            ComputeCmdlets | DeviceGuard | Manifests | MeasuredBootLogs | Stats | PeriodicStatLogs | MigrationLogs |
            RollbackSupportPackage | ArchivedLogs | Default}] [-MinimumTimestamp <datetime>] [-MaximumTimestamp <datetime>]
            [-IncludeArchived] [-IncludePeriodicStats] [-Credential <pscredential>]  [<CommonParameters>]
    ```

    Cmdlet собирает журналы с вашего устройства и копирует эти журналы в указанную сеть или локальную долю.

    Используемые параметры следующие:

    - `-Path`- Укажите сеть или локальный путь для копирования пакета поддержки. (обязательно)
    - `-Credential`- Укажите учетные данные для доступа к защищенной траектории.
    - `-Zip`- Укажите для создания файла застежки-молнии.
    - `-Include`- Укажите, чтобы включить компоненты, которые будут включены в пакет поддержки. Если не `Default` указано, предполагается.
    - `-IncludeArchived`- Укажите, чтобы включить архивные журналы в пакет поддержки.
    - `-IncludePeriodicStats`- Укажите, чтобы включить периодические журналы статистики в пакет поддержки.

    
