---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/05/2019
ms.author: alkohli
ms.openlocfilehash: 880b630ae48eda086f6454f0d7108d27d3403b77
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66161166"
---
Если загружается в среде без DHCP, выполните следующие действия для развертывания виртуальной машины для шлюза поле данных.

1. [Подключение к интерфейсу Windows PowerShell устройства](#connect-to-the-powershell-interface).
2. Используйте `Get-HcsIpAddress` командлет, чтобы получить список сетевых интерфейсов, включенных на виртуальном устройстве. Если на устройстве включен один сетевой интерфейс, по умолчанию ему назначается имя `Ethernet`.

    В следующем примере показано использование этого командлета:

    ```
    [10.100.10.10]: PS>Get-HcsIpAddress

    OperationalStatus : Up
    Name              : Ethernet
    UseDhcp           : True
    IpAddress         : 10.100.10.10
    Gateway           : 10.100.10.1
    ```

3. Для настройки сети используйте командлет `Set-HcsIpAddress` . Как в этом примере:

    ```
    Set-HcsIpAddress –Name Ethernet –IpAddress 10.161.22.90 –Netmask 255.255.255.0 –Gateway 10.161.22.1
    ```

