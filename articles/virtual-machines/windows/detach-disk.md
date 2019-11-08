---
title: Отключение диска данных от виртуальной машины Windows в Azure | Документация Майкрософт
description: Отключение диска данных от виртуальной машины в Azure c использованием модели развертывания Resource Manager.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-service-management
ms.assetid: 13180343-ac49-4a3a-85d8-0ead95e2028c
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 07/17/2018
ms.author: cynthn
ms.subservice: disks
ms.openlocfilehash: de33ed80055c26870a29b4c4393a9072ec8dda5e
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/07/2019
ms.locfileid: "73749487"
---
# <a name="how-to-detach-a-data-disk-from-a-windows-virtual-machine"></a>Отключение диска от виртуальной машины Windows

Когда диск данных, подключенный к виртуальной машине, больше не нужен, его можно легко отключить. При данной операции происходит удаление диска из виртуальной машины, но не из хранилища.

> [!WARNING]
> Если отключить диск, он не удаляется автоматически. Если вы подписаны на хранилище уровня "Премиум", с вас по-прежнему будет взиматься плата за хранение этого диска. Дополнительные сведения см. в разделе о [ценах и выставлении счетов при использовании хранилища класса Premium](disks-types.md#billing).

Если вы хотите снова использовать существующие данные на диске, его можно легко повторно подключить как к той же самой, так и к другой виртуальной машине.

 

## <a name="detach-a-data-disk-using-powershell"></a>Отключение диска данных с помощью PowerShell

Можно в *горячем* режиме отсоединить диск данных с помощью PowerShell. Но убедитесь, что диск больше не используется никакими средствами, прежде чем отключать его от виртуальной машины.

В этом примере мы отключим диск с именем **myDisk** от виртуальной машины **myVM** в группе ресурсов **myResourceGroup**. Сначала удалите диск с помощью командлета [Remove-AzVMDataDisk](https://docs.microsoft.com/powershell/module/az.compute/remove-azvmdatadisk). Затем обновите состояние виртуальной машины с помощью командлета [Update-AzVM](https://docs.microsoft.com/powershell/module/az.compute/update-azvm), чтобы завершить процесс удаления диска данных.

```azurepowershell-interactive
$VirtualMachine = Get-AzVM -ResourceGroupName "myResourceGroup" -Name "myVM"
Remove-AzVMDataDisk -VM $VirtualMachine -Name "myDisk"
Update-AzVM -ResourceGroupName "myResourceGroup" -VM $VirtualMachine
```

Диск остается в хранилище, но он уже не подключен к виртуальной машине.

## <a name="detach-a-data-disk-using-the-portal"></a>Отключение диска данных с помощью портала

1. В меню слева выберите **Виртуальные машины**.
2. Выберите виртуальную машину, диск данных которой необходимо отключить, и щелкните **Остановить**, чтобы отменить ее распределение.
3. В области виртуальной машины выберите **Диски**.
4. В верхней части области **Диски** щелкните **Изменить**.
5. В области **Диски** справа от диска данных, который требуется отключить, нажмите кнопку отключения ![Изображение кнопки отключения](./media/detach-disk/detach.png).
5. Удалив диск, в верхней части области нажмите кнопку **Сохранить**.
6. В области виртуальной машины щелкните **Обзор** и нажмите кнопку **Запустить** (вверху области), чтобы перезапустить виртуальную машину.

Диск остается в хранилище, но он уже не подключен к виртуальной машине.

## <a name="next-steps"></a>Дальнейшие действия

Если вы хотите повторно использовать диск данных, то можете просто [подключить его к другой виртуальной машине](attach-managed-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)