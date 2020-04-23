---
title: 'Пример CLI: создание двух виртуальных машин с внутренней и внешней группой безопасности сети'
description: Создайте две виртуальные машины с внутренними и внешними группами безопасности сети для защиты сетевого трафика с помощью Azure CLI.
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
ms.openlocfilehash: b4339ab2c512a96614158f673cb07b60184f5f71
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81459956"
---
# <a name="secure-network-traffic-between-virtual-machines-using-an-nsg"></a>Защита сетевого трафика между виртуальными машинами с помощью группы безопасности сети

Этот скрипт создает две виртуальные машины и защищает их входящий трафик. Одна виртуальная машина доступна через Интернет. Ее группа безопасности сети настроена таким образом, чтобы разрешать трафик на портах 22 и 80. Вторая виртуальная машина недоступна через Интернет. Ее группа безопасности сети настроена таким образом, чтобы разрешать трафик только из первой виртуальной машины.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Пример скрипта

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/create-vm-nsg/create-vm-nsg.sh "Create VM with NSG")]

## <a name="clean-up-deployment"></a>Очистка развертывания

Выполните следующую команду, чтобы удалить группу ресурсов, виртуальную машину и все связанные с ней ресурсы.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Описание скрипта

Для создания группы ресурсов, виртуальной машины и всех связанных ресурсов этот скрипт использует следующие команды. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Get-Help | Примечания |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group) | Создает группу ресурсов, в которой хранятся все ресурсы. |
| [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet) | Создает виртуальную сеть и подсеть Azure. |
| [az network vnet subnet create](https://docs.microsoft.com/cli/azure/network/vnet/subnet) | Создает подсеть. |
| [az vm create](https://docs.microsoft.com/cli/azure/vm) | Создает виртуальную машину и подключает ее к сетевой карте, виртуальной сети, подсети и группе безопасности сети. Эта команда также указывает образ виртуальной машины, который будет использоваться, и учетные данные администратора.  |
| [az network nsg rule list](https://docs.microsoft.com/cli/azure/network/nsg/rule) | Возвращает сведения о правиле группы безопасности сети. В этом примере имя правила хранится в переменной для дальнейшего использования в скрипте. |
| [az network nsg rule update](https://docs.microsoft.com/cli/azure/network/nsg/rule) | Обновляет правило NSG. В этом примере внутреннее правило обновляется, чтобы разрешить передачу трафика только из интерфейсной подсети. |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension) | Удаляет группу ресурсов со всеми вложенными ресурсами. |

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения об Azure CLI см. в [документации по Azure CLI](https://docs.microsoft.com/cli/azure).

Дополнительные примеры скриптов интерфейса командной строки для виртуальных машин см. в [документации по виртуальным машинам Azure под управлением Linux](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
