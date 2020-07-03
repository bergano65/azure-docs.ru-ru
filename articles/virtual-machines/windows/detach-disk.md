---
title: Отключение диска данных от виртуальной машины Windows в Azure
description: Отключение диска данных от виртуальной машины в Azure c использованием модели развертывания Resource Manager.
author: cynthn
ms.service: virtual-machines-windows
ms.subservice: disks
ms.workload: infrastructure-services
ms.topic: article
ms.date: 01/08/2020
ms.author: cynthn
ms.openlocfilehash: c93bb5fd3e92c6a947fe997b58207b87b2717fd5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "82082770"
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
$VirtualMachine = Get-AzVM `
   -ResourceGroupName "myResourceGroup" `
   -Name "myVM"
Remove-AzVMDataDisk `
   -VM $VirtualMachine `
   -Name "myDisk"
Update-AzVM `
   -ResourceGroupName "myResourceGroup" `
   -VM $VirtualMachine
```

Диск остается в хранилище, но он уже не подключен к виртуальной машине.

## <a name="detach-a-data-disk-using-the-portal"></a>Отключение диска данных с помощью портала

Диск данных *можно удалить,* но убедитесь, что он активно не использует диск перед отсоединением от виртуальной машины.

1. В меню слева выберите **Виртуальные машины**.
1. Выберите виртуальную машину с диском данных, который необходимо отключить.
1. В разделе **Параметры** выберите **Диски**.
1. В верхней части области **Диски** щелкните **Изменить**.
1. В области **диски** в правой части диска данных, который нужно отключить, выберите **отсоединить**.
1. Выберите **сохранить** в верхней части страницы, чтобы сохранить изменения.

Диск остается в хранилище, но он уже не подключен к виртуальной машине.

## <a name="next-steps"></a>Дальнейшие шаги

Если вы хотите повторно использовать диск данных, то можете просто [подключить его к другой виртуальной машине](attach-managed-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
