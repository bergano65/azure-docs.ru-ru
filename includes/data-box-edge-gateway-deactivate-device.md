---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/05/2019
ms.author: alkohli
ms.openlocfilehash: 21c19027d21a87e199d74644cfc5c8f3cd52ba4c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "79129161"
---
Чтобы сбросить устройство, необходимо безопасно очистить все данные на диске данных и загрузочном диске устройства. 

Используйте командлет `Reset-HcsAppliance` , чтобы очистить диски данных и загрузочный диск или только диски данных. Параметры `ClearData` и `BootDisk` позволяют очистить диски данных и загрузочный диск соответственно.

`BootDisk` Параметр очищает загрузочный диск и делает его непригодным для использования. Его следует использовать только в том случае, если устройство должно быть возвращено в корпорацию Майкрософт. Дополнительные сведения см. в [статье Возврат устройства в корпорацию Майкрософт](https://docs.microsoft.com/azure/databox-online/data-box-edge-return-device).

Если вы используете сброс устройства в локальном веб-ИНТЕРФЕЙСе, только диски данных безопасно очищаются, но загрузочный диск остается неизменным. Загрузочный диск содержит конфигурацию устройства.

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
