---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/04/2019
ms.author: alkohli
ms.openlocfilehash: 757d6aeecc6c97726e890076b0e27a82a1a45d27
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57556706"
---
1. [Подключение к интерфейсу PowerShell](#connect-to-the-powershell-interface).
2. Используйте `Get-HcsApplianceInfo` для получения информации для вашего устройства.

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

    Ниже приведена таблица, котором кратко описаны некоторые из сведений о важных устройств:
    
    | Параметр                             | ОПИСАНИЕ                                                                                                                                                  |   |
    |--------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------|---|
    | FriendlyName                   | Понятное имя устройства, настроенного через локальный пользовательский Интерфейс во время развертывания устройства. В качестве понятного имени по умолчанию используется серийный номер устройства.  |   |
    | SerialNumber                   | Серийный номер устройства — уникальный номер, назначенный на заводе.                                                                             |   |
    | Модель                          | Модель для устройства Edge поле данных или шлюз поле данных. Модель является виртуальным для шлюза поля данных и физическим для Edge поле данных.                   |   |
    | FriendlySoftwareVersion        | Понятная строка, соответствующая версии программного обеспечения устройства. Для системы с предварительной версии понятная версия программного обеспечения будет 1902 Edge поле данных. |   |
    | HcsVersion                     | Версия программного обеспечения HCS, работающего на устройстве. Например версия программного обеспечения HCS, соответствующая 1902 Edge поле данных — 1.4.771.324.            |   |
    | LocalCapacityInMb              | Общая емкость локального устройства в мегабитах.                                                                                                        |   |
    | IsRegistered                   | Это значение указывает, активирована ли устройство в службе.                                                                                         |   |
    ```

