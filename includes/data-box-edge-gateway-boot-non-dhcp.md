---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/05/2019
ms.author: alkohli
ms.openlocfilehash: 880b630ae48eda086f6454f0d7108d27d3403b77
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "67185955"
---
Если вы загружаетесь в среде, не относящемся к DHCP, выполните следующие действия, чтобы развернуть виртуальную машину для шлюза Box Data.

1. [Подключитесь к интерфейсу устройства Windows PowerShell.](#connect-to-the-powershell-interface)
2. Используйте `Get-HcsIpAddress` cmdlet, чтобы перечислить сетевые интерфейсы, включенные на вашем виртуальном устройстве. Если на устройстве включен один сетевой интерфейс, по умолчанию ему назначается имя `Ethernet`.

    Следующий пример показывает использование этого cmdlet:

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

