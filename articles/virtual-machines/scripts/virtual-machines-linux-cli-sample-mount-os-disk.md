---
title: Пример скрипта Azure CLI. Подключение диска операционной системы
description: Пример скрипта Azure CLI. Подключение диска операционной системы
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/27/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: f15beeee2c5843506f0bf89b4c6d66d77826e152
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81459896"
---
# <a name="troubleshoot-a-vms-operating-system-disk"></a>Устранение неполадок диска операционной системы виртуальных машин

Этот скрипт подключает диск операционной системы виртуальной машины, в которой произошел сбой или возникла проблема, в качестве диска данных ко второй виртуальной машине. Это может быть полезно при устранении неполадок с дисками или восстановлении данных.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Пример скрипта

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/mount-os-disk/mount-os-disk.sh "Quick Create VM")]

## <a name="script-explanation"></a>Описание скрипта

Для создания группы ресурсов, виртуальной машины и всех связанных ресурсов этот скрипт использует следующие команды. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Get-Help | Примечания |
|---|---|
| [az vm show](https://docs.microsoft.com/cli/azure/vm) | Возвращает список виртуальных машин. В этом случае параметр запроса используется для возвращения диска операционной системы виртуальной машины. Это значение добавляется к имени переменной uri. |
| [az vm delete](https://docs.microsoft.com/cli/azure/vm) | Удаляет виртуальную машину. |
| [az vm create](https://docs.microsoft.com/cli/azure/vm) | Создает виртуальную машину.  |
| [az vm disk attach](https://docs.microsoft.com/cli/azure/vm/disk) | Подключает диск к виртуальной машине. |
| [az vm list-ip-addresses](https://docs.microsoft.com/cli/azure/vm) | Возвращает IP-адреса виртуальной машины. |

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения об Azure CLI см. в [документации по Azure CLI](https://docs.microsoft.com/cli/azure).

Дополнительные примеры скриптов интерфейса командной строки для виртуальных машин см. в [документации по виртуальным машинам Azure под управлением Linux](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
