---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/05/2019
ms.author: alkohli
ms.openlocfilehash: 21c19027d21a87e199d74644cfc5c8f3cd52ba4c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79129161"
---
Чтобы сбросить устройство, необходимо надежно уничтожить все данные на диске данных и загрузочном диске вашего устройства. 

Используйте `Reset-HcsAppliance` cmdlet, чтобы уничтожить как диски данных и загрузочный диск или только диски данных. `ClearData` Переключатели `BootDisk` и переключатели позволяют протирать диски данных и загрузочный диск соответственно.

Переключатель `BootDisk` вытирает загрузочный диск и делает устройство непригодным для угона. Он должен использоваться только тогда, когда устройство должно быть возвращено в корпорацию Майкрософт. Для получения дополнительной [информации см.](https://docs.microsoft.com/azure/databox-online/data-box-edge-return-device)

При использовании сбросить устройство в локальном веб-файле, только диски данных надежно протираются, но загрузочный диск остается нетронутым. Загрузочный диск содержит конфигурацию устройства.

1. [Подключение к интерфейсу PowerShell.](#connect-to-the-powershell-interface)
2. В командной строке введите:

    `Reset-HcsAppliance -ClearData -BootDisk`

    Ниже приводится следующий пример, как использовать этот cmdlet:

    ```powershell
    [10.128.24.33]: PS>Reset-HcsAppliance -ClearData -BootDisk

    Confirm
    Are you sure you want to perform this action?
    Performing the operation "Reset-HcsAppliance" on target "ShouldProcess appliance".
    [Y] Yes  [A] Yes to All  [N] No  [L] No to All  [?] Help (default is "Y"): N
    ```
