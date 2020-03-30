---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/04/2019
ms.author: alkohli
ms.openlocfilehash: d5af557a62f4bd35c242d334c28a38c3d632f7cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "67185747"
---
1. [Подключение к интерфейсу PowerShell.](#connect-to-the-powershell-interface)
2. `Get-HcsApplianceInfo` Используйте, чтобы получить информацию для вашего устройства.

    Следующий пример показывает использование этого cmdlet:

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

    Вот таблица, обобщающая некоторые важные сведения об устройстве:
    
    | Параметр                             | Описание                                                                                                                                                  |   |
    |--------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------|---|
    | FriendlyName                   | Дружественное название устройства, настроенное через локальный веб-миг во время развертывания устройства. В качестве понятного имени по умолчанию используется серийный номер устройства.  |   |
    | SerialNumber                   | Серийный номер устройства является уникальным номером, присвоенным на заводе.                                                                             |   |
    | Модель                          | Модель для устройства Data Box Edge или Data Box Gateway. Модель является виртуальной для шлюза Box Data и физически для Data Box Edge.                   |   |
    | FriendlySoftwareVersion        | Понятная строка, соответствующая версии программного обеспечения устройства. Для системы, работая предварительный просмотр, дружественной версии программного обеспечения будет Data Box Edge 1902. |   |
    | HcsVersion                     | Версия программного обеспечения HCS, работающего на устройстве. Например, версия программного обеспечения HCS, соответствующая Data Box Edge 1902, радо 1.4.771.324.            |   |
    | Местнаяемкость              | Общая локальная емкость устройства в Megabits.                                                                                                        |   |
    | Зарегистрировано                   | Это значение указывает, активировано ли устройство службой.                                                                                         |   |


