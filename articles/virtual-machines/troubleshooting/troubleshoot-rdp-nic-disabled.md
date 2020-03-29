---
title: Сбой удаленного подключения к виртуальным машинам Azure из-за отключенного адаптера NIC | Документация Майкрософт
description: Сведения об устранении проблемы, когда подключение протокола удаленного рабочего стола завершается сбоем из-за отключенного сетевого интерфейса в виртуальной машине Azure | Документация Майкрософт
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/12/2018
ms.author: genli
ms.openlocfilehash: 315974e4995630eb3af055ac0e1c44f7d8dd0737
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77918246"
---
#  <a name="cannot-remote-desktop-to-a-vm-because-the-network-interface-is-disabled"></a>Сбой подключения удаленного рабочего стола к виртуальной машине из-за отключенного сетевого интерфейса

В этой статье объясняется, как устранить проблему, когда невозможно установить подключение удаленного рабочего стола к Виртуальным машинам Azure под управлением Windows, если сетевой интерфейс отключен.


## <a name="symptoms"></a>Симптомы

Вы не можете установить подключение RDP или любое другое подключение к другим портам виртуальной машины в Azure, так как сетевой интерфейс на виртуальной машине отключен.

## <a name="solution"></a>Решение

Прежде чем выполнять какие-либо действия, сделайте моментальный снимок диска ОС затронутой виртуальной машины в качестве резервной копии. Дополнительные сведения см. в статье [Создание моментального снимка](../windows/snapshot-copy-managed-disk.md).

Чтобы включить интерфейс для виртуальной машины, используйте последовательную консоль или [сбросьте сетевой интерфейс](#reset-network-interface) виртуальной машины.

### <a name="use-serial-control"></a>Использование последовательной консоли

1. Подключитесь к [последовательной консоли и откройте экземпляр командной строки](./serial-console-windows.md#use-cmd-or-powershell-in-serial-console
). Если серийная консоль не включена [reset network interface](#reset-network-interface)на вашем VM, см.
2. Проверьте состояние сетевого интерфейса:

        netsh interface show interface

    Запишите имя отключенного сетевого интерфейса.

3. Включите сетевой интерфейс:

        netsh interface set interface name="interface Name" admin=enabled

    Например, если имя интерфейса взаимодействия — Ethernet 2, выполните команду ниже:

        netsh interface set interface name="Ethernet 2" admin=enabled

4.  Снова проверьте состояние сетевого интерфейса, чтобы убедиться, что он включен.

        netsh interface show interface

    На этом этапе не нужно перезапускать виртуальную машину. Она снова станет доступна.

5.  Подключитесь к виртуальной машине и проверьте, устранена ли проблема.

## <a name="reset-network-interface"></a>Сброс сетевого интерфейса

Чтобы сбросить сетевой интерфейс, измените IP-адрес на другой, который доступен в подсети. Это можно сделать на портале Azure или в Azure PowerShell. Дополнительные сведения см. в статье о [сбросе сетевого интерфейса](reset-network-interface.md).
