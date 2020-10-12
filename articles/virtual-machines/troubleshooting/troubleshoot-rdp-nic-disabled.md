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
ms.openlocfilehash: 0e79efc9de43fc0a3044e9ae1e3959f63bb6e69f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "90090276"
---
#  <a name="cannot-remote-desktop-to-a-vm-because-the-network-interface-is-disabled"></a>Сбой подключения удаленного рабочего стола к виртуальной машине из-за отключенного сетевого интерфейса

В этой статье объясняется, как устранить проблему, когда невозможно установить подключение удаленного рабочего стола к Виртуальным машинам Azure под управлением Windows, если сетевой интерфейс отключен.


## <a name="symptoms"></a>Симптомы

Вы не можете установить подключение RDP или любое другое подключение к другим портам виртуальной машины в Azure, так как сетевой интерфейс на виртуальной машине отключен.

![Снимок экрана, на котором показана виртуальная машина, в которой сетевой интерфейс отключен.](./media/troubleshoot-rdp-nic-disabled/disconnected.png)

![Снимок экрана, на котором показана виртуальная машина, в которой отключен сетевой интерфейс.](./media/troubleshoot-rdp-nic-disabled/disabled.png)


## <a name="solution"></a>Решение

Прежде чем выполнять какие-либо действия, сделайте моментальный снимок диска ОС затронутой виртуальной машины в качестве резервной копии. Дополнительные сведения см. в статье [Создание моментального снимка](../windows/snapshot-copy-managed-disk.md).

Чтобы включить интерфейс для виртуальной машины, используйте последовательную консоль или [сбросьте сетевой интерфейс](#reset-network-interface) виртуальной машины.

### <a name="use-serial-control"></a>Использование последовательной консоли

1. Подключитесь к [последовательной консоли и откройте экземпляр командной строки](./serial-console-windows.md#use-cmd-or-powershell-in-serial-console). Если последовательное консоль не включена на виртуальной машине, см. раздел [Сброс сетевого интерфейса](#reset-network-interface).
2. Проверьте состояние сетевого интерфейса:

    ```console
    netsh interface show interface
    ```

    Запишите имя отключенного сетевого интерфейса.

3. Включите сетевой интерфейс:

    ```console
    netsh interface set interface name="interface Name" admin=enabled
    ```

    Например, если имя интерфейса взаимодействия — Ethernet 2, выполните команду ниже:

    ```console
    netsh interface set interface name="Ethernet 2" admin=enabled
    ```

4.  Снова проверьте состояние сетевого интерфейса, чтобы убедиться, что он включен.

    ```console
    netsh interface show interface
    ```

    На этом этапе не нужно перезапускать виртуальную машину. Она снова станет доступна.

5.  Подключитесь к виртуальной машине и проверьте, устранена ли проблема.

## <a name="reset-network-interface"></a>Сброс сетевого интерфейса

Чтобы сбросить сетевой интерфейс, измените IP-адрес на другой, который доступен в подсети. Это можно сделать на портале Azure или в Azure PowerShell. Дополнительные сведения см. в статье о [сбросе сетевого интерфейса](reset-network-interface.md).
