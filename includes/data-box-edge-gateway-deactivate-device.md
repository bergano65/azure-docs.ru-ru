---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/05/2019
ms.author: alkohli
ms.openlocfilehash: aebb82690a7a49aba071ed64349d37d516208cca
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/23/2019
ms.locfileid: "67125610"
---
Сброс устройства, необходимо для безопасной очистки всех данных на диск с данными и загрузочного диска устройства. 

Используйте `Reset-HcsAppliance` командлет, чтобы очистить диски с данными и загрузочный диск или только диски данных. `ClearData` И `BootDisk` коммутаторов позволяют очистить диски с данными и загрузочный диск соответственно.

При использовании сброса в локальном веб-Интерфейсе устройства, безопасно очищены только дисков данных, но загрузочный диск остается без изменений. Загрузочный диск содержит конфигурацию устройства.

1. [Подключение к интерфейсу PowerShell](#connect-to-the-powershell-interface).
2. В командной строке выполните следующую команду:

    `Reset-HcsAppliance -ClearData -BootDisk`

    В следующем примере показано, как с помощью этого командлета:
    ```
    [10.128.24.33]: PS>Reset-HcsAppliance -ClearData -BootDisk
    
    Confirm
    Are you sure you want to perform this action?
    Performing the operation "Reset-HcsAppliance" on target "ShouldProcess appliance".
    [Y] Yes  [A] Yes to All  [N] No  [L] No to All  [?] Help (default is "Y"): N
    ```
