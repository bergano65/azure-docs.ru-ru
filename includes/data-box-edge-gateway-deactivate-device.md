---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/05/2019
ms.author: alkohli
ms.openlocfilehash: 21c19027d21a87e199d74644cfc5c8f3cd52ba4c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "79129161"
---
Чтобы сбросить настройки устройства, необходимо безопасно очистить все данные на диске данных и загрузочном диске устройства. 

Используйте `Reset-HcsAppliance` командлет, чтобы очистить диски данных и загрузочный диск или только диски данных. `ClearData`Параметры и `BootDisk` позволяют очистить диски данных и загрузочный диск соответственно.

`BootDisk`Параметр очищает загрузочный диск и делает его непригодным для использования. Этот параметр следует применять, только если вы собираетесь вернуть устройство в Майкрософт. Дополнительные сведения см. в [статье Возврат устройства в корпорацию Майкрософт](https://docs.microsoft.com/azure/databox-online/data-box-edge-return-device).

Если воспользоваться сбросом настроек устройства в локальном пользовательском веб-интерфейсе, безопасно очистятся только диски данных, но загрузочный диск останется нетронутым. На загрузочном диске содержится конфигурация устройства.

1. [Подключитесь к интерфейсу PowerShell](#connect-to-the-powershell-interface).
2. В командной строке введите следующее:

    `Reset-HcsAppliance -ClearData -BootDisk`

    В следующем примере показано, как использовать этот командлет:

    ```powershell
    [10.128.24.33]: PS>Reset-HcsAppliance -ClearData -BootDisk

    Confirm
    Are you sure you want to perform this action?
    Performing the operation "Reset-HcsAppliance" on target "ShouldProcess appliance".
    [Y] Yes  [A] Yes to All  [N] No  [L] No to All  [?] Help (default is "Y"): N
    ```
