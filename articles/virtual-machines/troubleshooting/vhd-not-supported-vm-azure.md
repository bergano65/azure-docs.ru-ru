---
title: Виртуальный жесткий диск не поддерживается при создании виртуальной машины в Azure | Документация Майкрософт
description: Эта статья поможет устранить ошибки виртуального жесткого диска при запуске виртуальной машины в Microsoft Azure.
services: virtual-machines
documentationCenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
ms.service: virtual-machines
ms.assetid: 5488aba9-c3da-435d-b4a5-63470f455b07
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure
ms.date: 06/29/2020
ms.author: genli
ms.openlocfilehash: ff4822b513ed2aea6a18ba45bffc1d060ee2410e
ms.sourcegitcommit: dee7b84104741ddf74b660c3c0a291adf11ed349
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85937574"
---
# <a name="vhd-is-not-supported-when-you-create-a-virtual-machine-in-azure"></a>Виртуальный жесткий диск не поддерживается при создании виртуальной машины в Azure

Эта статья поможет вам устранить ошибки VHD при работе виртуальной машины в Windows или Linux.

## <a name="symptoms"></a>Симптомы

При создании виртуальной машины в Microsoft Azure с помощью переданного виртуального жесткого диска происходит сбой развертывания и возвращается следующее сообщение об ошибке: 

```
New-AzureRmVM : Long running operation failed with status 'Failed'.
ErrorCode: InvalidVhd
ErrorMessage: The specified cookie value in VHD footer indicates that disk 'diskname' with blob https://xxxxxx.blob.core.windows.net/vhds/samplename.vhd is not a supported VHD. Disk is expected to have cookie value 'conectix'.
```

## <a name="cause"></a>Причина:

Эта ошибка возникает по одной из следующих причин:

- Виртуальный жесткий диск не соответствует выравниванию разделов со смещением на 1 МБ. Поддерживаемый размер диска должен быть равен 1 МБ × N (например, 102 401 МБ).
- Виртуальный жесткий диск поврежден или не поддерживается. 

## <a name="resolution"></a>Решение

> [!NOTE]
> Чтобы выполнить следующее исправление, клиенту необходимо выполнить эти действия перед отправкой виртуального жесткого диска в Azure.

Чтобы устранить эту проблему, измените размер диска в соответствии с выравниванием по 1 МБ:

- Чтобы устранить проблему в Windows, используйте [командлет PowerShell Resize-VHD](https://docs.microsoft.com/powershell/module/hyper-v/resize-vhd). Обратите внимание, что **resize-VHD** не является командлетом Azure PowerShell.

  1. [Установка роли Hyper-V в Windows Server](https://docs.microsoft.com/windows-server/virtualization/hyper-v/get-started/install-the-hyper-v-role-on-windows-server)
  1. [Преобразование виртуального диска в виртуальный жесткий диск фиксированного размера](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image#convert-the-virtual-disk-to-a-fixed-size-vhd)

- Чтобы устранить проблему в Linux, используйте [команду qemu-img](https://docs.microsoft.com/azure/virtual-machines/linux/create-upload-generic).

Дополнительные сведения о создании и передаче виртуального жесткого диска для создания виртуальной машины Azure см. в следующих статьях:

- [Отправка пользовательского образа диска и создание на его основе виртуальной машины Linux с помощью Azure CLI 1.0](https://docs.microsoft.com/azure/virtual-machines/linux/upload-vhd)
- [Создание и передача виртуального жесткого диска Windows Server в Azure](https://docs.microsoft.com/azure/virtual-machines/windows/upload-generalized-managed)

Продолжение проблемы может указывать на поврежденный виртуальный жесткий диск. В этом случае рекомендуется перестроить виртуальный жесткий диск с нуля.

Дополнительные сведения см. в следующих статьях:

- [О виртуальных жестких дисках Windows](https://docs.microsoft.com/azure/virtual-machines/windows/about-disks-and-vhds#about-vhds)
- [О виртуальных жестких дисках Linux](https://docs.microsoft.com/azure/virtual-machines/linux/about-disks-and-vhds#about-vhds)