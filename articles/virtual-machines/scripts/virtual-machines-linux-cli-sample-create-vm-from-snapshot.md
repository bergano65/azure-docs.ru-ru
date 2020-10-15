---
title: Пример для CLI. Создание виртуальной машины из моментального снимка
description: Создание виртуальной машины из моментального снимка с помощью скрипта Azure CLI.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: ramankumarlive
manager: kavithag
editor: ramankum
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/10/2017
ms.author: ramankum
ms.custom: mvc
ms.openlocfilehash: a6110ba2787cb99e20c099eb466e2dbd0c3df28e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "87085305"
---
# <a name="create-a-virtual-machine-from-a-snapshot-with-cli"></a>Создание виртуальной машины из моментального снимка с помощью интерфейса командной строки

Этот скрипт создает виртуальную машину из моментального снимка диска ОС.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Пример скрипта

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/create-vm-from-snapshot/create-vm-from-snapshot.sh "Create VM from snapshot")]

## <a name="clean-up-deployment"></a>Очистка развертывания 

Выполните следующую команду, чтобы удалить группу ресурсов, виртуальную машину и все связанные с ней ресурсы.

```azurecli-interactive 
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Описание скрипта

Для создания управляемого диска, виртуальной машины и всех связанных ресурсов этот скрипт использует указанные ниже команды. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Get-Help | Примечания |
|---|---|
| [az snapshot show](/cli/azure/snapshot) | Возвращает моментальный снимок на основе имени моментального снимка и группы ресурсов. Для создания управляемого диска используется свойство идентификатора возвращаемого объекта.  |
| [az disk create](/cli/azure/disk) | Создает управляемые диски из моментального снимка на основе идентификатора моментального снимка, имени диска, типа хранилища и размера.  |
| [az vm create](/cli/azure/vm) | Создает виртуальную машину с помощью управляемого диска ОС. |

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения об Azure CLI см. в [документации по Azure CLI](/cli/azure).

Дополнительные примеры скриптов интерфейса командной строки для виртуальных машин см. в [документации по виртуальным машинам Azure под управлением Linux](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
