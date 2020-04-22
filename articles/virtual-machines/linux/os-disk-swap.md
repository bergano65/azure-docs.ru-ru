---
title: Своп между дисками ОС с помощью CLI '
description: Изменение диска операционной системы, используемого виртуальной машиной Azure, с помощью интерфейса командной строки.
author: cynthn
ms.service: virtual-machines
ms.subservice: disks
ms.workload: infrastructure-services
ms.topic: article
ms.date: 04/24/2018
ms.author: cynthn
ms.openlocfilehash: a8c879ae56eb22d9817841667d33e37109a4f63d
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759404"
---
# <a name="change-the-os-disk-used-by-an-azure-vm-using-the-cli"></a>Изменение диска ОС виртуальной машины Azure с помощью интерфейса командной строки


Если у вас имеется виртуальная машина, но нужно заменить ее диск на резервный диск или другой диск ОС, для этого можно использовать Azure CLI. Нет необходимости удалять и повторно создавать виртуальную машину. Можно даже использовать управляемый диск в другой группе ресурсов, если он еще не используется.

Не нужно останавливать виртуальную машину или отменять ее распределение. Идентификатор ресурса управляемого диска может быть заменен идентификатором ресурса другого управляемого диска. 

Убедитесь, что тип и размер виртуальной машины совместимы с диском, который необходимо подключить. Например, если диск, который вы хотите использовать, размещен в хранилище уровня "Премиум", то виртуальная машина должна поддерживать это хранилище (например, она должна относиться к серии DS).

Для этой статьи требуется Azure CLI версии 2.0.25 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0]( /cli/azure/install-azure-cli). 


Выполните команду [az disk list](/cli/azure/disk) для получения списка дисков в группе ресурсов.

```azurecli-interactive
az disk list \
   -g myResourceGroupDisk \
   --query '[*].{diskId:id}' \
   --output table
```


Выполните команду [az vm stop](/cli/azure/vm), чтобы остановить виртуальную машину или отменить ее распределение, прежде чем заменить диск.

```azurecli-interactive
az vm stop \
   -n myVM \
   -g myResourceGroup
```


Выполните команду [az vm update](/cli/azure/vm#az-vm-update), указав полный идентификатор ресурса нового диска в параметре `--osdisk`. 

```azurecli-interactive 
az vm update \
   -g myResourceGroup \
   -n myVM \
   --os-disk /subscriptions/<subscription ID>/resourceGroups/swap/providers/Microsoft.Compute/disks/myDisk 
   ```
   
Перезапустите виртуальную машину, выполнив команду [az vm start](/cli/azure/vm).

```azurecli-interactive
az vm start \
   -n myVM \
   -g myResourceGroup
```

   
**Дальнейшие действия**

Создание копии диска описывается в разделе [Моментальный снимок диска](snapshot-copy-managed-disk.md).
