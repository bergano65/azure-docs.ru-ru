---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/05/2019
ms.author: alkohli
ms.openlocfilehash: 21c19027d21a87e199d74644cfc5c8f3cd52ba4c
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/11/2020
ms.locfileid: "79129161"
---
Чтобы сбросить настройки устройства, необходимо безопасно очистить все данные на диске данных и загрузочном диске устройства. 

Чтобы очистить загрузочный диск и диски данных либо только диск данных, используйте командлет `Reset-HcsAppliance`. Параметры `ClearData` и `BootDisk` позволяют очистить диски данных и загрузочный диск соответственно.

Параметр `BootDisk` позволяет очистить загрузочный диск, после чего устройство станет непригодным для использования. Этот параметр следует применять, только если вы собираетесь вернуть устройство в Майкрософт. Дополнительные сведения см. в статье о [возврате устройства в Майкрософт](https://docs.microsoft.com/azure/databox-online/data-box-edge-return-device).

Если воспользоваться сбросом настроек устройства в локальном пользовательском веб-интерфейсе, безопасно очистятся только диски данных, но загрузочный диск останется нетронутым. На загрузочном диске содержится конфигурация устройства.

1. [Подключитесь к интерфейсу PowerShell](#connect-to-the-powershell-interface).
2. В командной строке введите:

    `Reset-HcsAppliance -ClearData -BootDisk`

    В следующем примере показано, как использовать этот командлет.

    ```powershell
    [10.128.24.33]: PS>Reset-HcsAppliance -ClearData -BootDisk

    Confirm
    Are you sure you want to perform this action?
    Performing the operation "Reset-HcsAppliance" on target "ShouldProcess appliance".
    [Y] Yes  [A] Yes to All  [N] No  [L] No to All  [?] Help (default is "Y"): N
    ```
