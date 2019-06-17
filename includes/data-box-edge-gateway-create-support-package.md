---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/05/2019
ms.author: alkohli
ms.openlocfilehash: b657ee32e76dd90671f7e91337ced01b925889a1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66161145"
---
Если возникают проблемы устройства, можно создать пакет поддержки из системных журналов. Поддержка Microsoft использует этот пакет для устранения неполадок. Выполните следующие действия, чтобы создать пакет поддержки.

1. [Подключение к в интерфейсе PowerShell устройства](#connect-to-the-powershell-interface).
2. Используйте `Get-HcsNodeSupportPackage` команду, чтобы создать пакет поддержки. Использование командлета выглядит следующим образом:

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

    Командлет собирает журналы с устройства и копирует эти журналы для указанной сети или локальную папку.

    Параметры, используемые это следующим образом:

    - `-Path` -Укажите сети или локальный путь для копирования пакета поддержки в. (требуется)
    - `-Credential` — Укажите учетные данные для доступа к защищенным пути.
    - `-Zip` — Укажите для создания ZIP-файл.
    - `-Include` -Укажите включать компоненты должны быть включены в пакет поддержки. Если не указан, `Default` предполагается, что.
    - `-IncludeArchived` — Укажите Включение архивированные журналы в пакет поддержки.
    - `-IncludePeriodicStats` — Укажите для включения периодических stat журналы в пакет поддержки.

    
