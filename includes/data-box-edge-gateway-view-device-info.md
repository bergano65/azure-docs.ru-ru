---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/04/2019
ms.author: alkohli
ms.openlocfilehash: d5af557a62f4bd35c242d334c28a38c3d632f7cf
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "67185747"
---
1. [Подключитесь к интерфейсу PowerShell](#connect-to-the-powershell-interface).
2. Используйте `Get-HcsApplianceInfo` для получения сведений об устройстве.

    В следующем примере показано использование этого командлета:

    ```
    [10.100.10.10]: PS>Get-HcsApplianceInfo
    
    Id                            : b2044bdb-56fd-4561-a90b-407b2a67bdfc
    FriendlyName                  : DBE-NBSVFQR94S6
    Name                          : DBE-NBSVFQR94S6
    SerialNumber                  : HCS-NBSVFQR94S6
    DeviceId                      : 40d7288d-cd28-481d-a1ea-87ba9e71ca6b
    Model                         : Virtual
    FriendlySoftwareVersion       : Data Box Gateway 1902
    HcsVersion                    : 1.4.771.324
    IsClustered                   : False
    IsVirtual                     : True
    LocalCapacityInMb             : 1964992
    SystemState                   : Initialized
    SystemStatus                  : Normal
    Type                          : DataBoxGateway
    CloudReadRateBytesPerSec      : 0
    CloudWriteRateBytesPerSec     : 0
    IsInitialPasswordSet          : True
    FriendlySoftwareVersionNumber : 1902
    UploadPolicy                  : All
    DataDiskResiliencySettingName : Simple
    ApplianceTypeFriendlyName     : Data Box Gateway
    IsRegistered                  : False
    ```

    Ниже приведена таблица с описанием некоторых важных сведений об устройстве.
    
    | Параметр                             | Описание                                                                                                                                                  |   |
    |--------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------|---|
    | FriendlyName                   | Понятное имя устройства, настроенное с помощью локального веб-интерфейса во время развертывания устройства. В качестве понятного имени по умолчанию используется серийный номер устройства.  |   |
    | SerialNumber                   | Серийный номер устройства — это уникальный номер, назначенный фабрике.                                                                             |   |
    | Модель                          | Модель для Data Box Edge или Шлюз Data Box устройства. Модель является виртуальной для Шлюз Data Box и физической для Data Box Edge.                   |   |
    | FriendlySoftwareVersion        | Понятная строка, соответствующая версии программного обеспечения устройства. Для системы, в которой используется предварительная версия, понятное программное обеспечение будет Data Box Edge 1902. |   |
    | хксверсион                     | Версия программного обеспечения HCS, работающего на устройстве. Например, версия программного обеспечения HCS, соответствующая Data Box Edge 1902, — 1.4.771.324.            |   |
    | локалкапаЦитинмб              | Общая локальная емкость устройства в мегабит.                                                                                                        |   |
    | С регистрацией                   | Это значение указывает, активировано ли устройство в службе.                                                                                         |   |


