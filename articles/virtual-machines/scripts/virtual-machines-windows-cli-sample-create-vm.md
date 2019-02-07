---
title: Пример скрипта Azure CLI. Создание виртуальной машины Windows Server | Документация Майкрософт
description: Пример скрипта Azure CLI. Создание виртуальной машины Windows Server
services: virtual-machines-Windows
documentationcenter: virtual-machines
author: rickstercdn
manager: jeconnoc
editor: tysonn
tags: ''
ms.assetid: ''
ms.service: virtual-machines-Windows
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-Windows
ms.workload: infrastructure
ms.date: 02/23/2017
ms.author: rclaus
ms.openlocfilehash: 3262816f3a71bd672cf502ebe65eaab8a748035c
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/04/2019
ms.locfileid: "55692565"
---
# <a name="create-a-virtual-machine-with-the-azure-cli"></a>Создание виртуальной машины с помощью Azure CLI

Этот сценарий создает виртуальную машину Azure под управлением Windows Server 2016. После выполнения сценария можно получить доступ к виртуальной машине через подключение к удаленному рабочему столу.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Пример скрипта

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/create-vm-detailed/create-windows-vm-detailed.sh "Quick Create VM")]

## <a name="clean-up-deployment"></a>Очистка развертывания 

Выполните следующую команду, чтобы удалить группу ресурсов, виртуальную машину и все связанные с ней ресурсы.

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="script-explanation"></a>Описание скрипта

Для создания группы ресурсов, виртуальной машины и всех связанных ресурсов этот скрипт использует следующие команды. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Команда | Примечания |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group) | Создает группу ресурсов, в которой хранятся все ресурсы. |
| [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet) | Создает виртуальную сеть и подсеть Azure. |
| [az network public-ip create](https://docs.microsoft.com/cli/azure/network/public-ip) | Создает общедоступный IP-адрес со статическим IP-адресом и связанным DNS-именем. |
| [az network nsg create](https://docs.microsoft.com/cli/azure/network/nsg) | Создает группу безопасности сети (NSG), которая выполняет роль периметра безопасности между Интернетом и виртуальной машиной. |
| [az network nic create](https://docs.microsoft.com/cli/azure/network/nic) | Создает виртуальную сетевую карту и подключает ее к виртуальной сети, подсети и группе безопасности сети. |
| [az vm create](https://docs.microsoft.com/cli/azure/vm) | Создает виртуальную машину и подключает ее к сетевой карте, виртуальной сети, подсети и группе безопасности сети. Эта команда также указывает образ виртуальной машины, который будет использоваться, и учетные данные администратора.  |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension) | Удаляет группу ресурсов со всеми вложенными ресурсами. |

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения об Azure CLI см. в [документации по Azure CLI](https://docs.microsoft.com/cli/azure).

Дополнительные примеры скриптов интерфейса командной строки для виртуальных машин см. в [документации по виртуальным машинам Azure под управлением Windows](../windows/cli-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
