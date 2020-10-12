---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/05/2019
ms.author: alkohli
ms.openlocfilehash: 880b630ae48eda086f6454f0d7108d27d3403b77
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "67185955"
---
При загрузке в среде без DHCP выполните следующие действия, чтобы развернуть виртуальную машину для Шлюз Data Box.

1. [Подключитесь к интерфейсу Windows PowerShell устройства](#connect-to-the-powershell-interface).
2. Используйте `Get-HcsIpAddress` командлет, чтобы вывести список сетевых интерфейсов, включенных на вашем виртуальном устройстве. Если на устройстве включен один сетевой интерфейс, по умолчанию ему назначается имя `Ethernet`.

    В следующем примере показано использование этого командлета:

    ```
    [10.100.10.10]: PS>Get-HcsIpAddress

    OperationalStatus : Up
    Name              : Ethernet
    UseDhcp           : True
    IpAddress         : 10.100.10.10
    Gateway           : 10.100.10.1
    ```

3. Для настройки сети используйте командлет `Set-HcsIpAddress` . См. следующий пример.

    ```
    Set-HcsIpAddress –Name Ethernet –IpAddress 10.161.22.90 –Netmask 255.255.255.0 –Gateway 10.161.22.1
    ```

