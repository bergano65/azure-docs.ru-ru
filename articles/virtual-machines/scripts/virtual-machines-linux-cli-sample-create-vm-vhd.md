---
title: Пример скрипта Azure CLI. Создание виртуальной машины с помощью виртуального жесткого диска
description: Пример скрипта Azure CLI. Создание виртуальной машины с помощью виртуального жесткого диска.
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
ms.date: 03/09/2017
ms.author: cynthn
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 6f5e30b0d6a072f9a40ae57ebe325461cdfe5bb1
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/31/2020
ms.locfileid: "87479642"
---
# <a name="create-a-vm-with-a-virtual-hard-disk"></a>Создание виртуальной машины с помощью виртуального жесткого диска

Этот пример создает виртуальную машину с помощью виртуального жесткого диска.
Он создает группу ресурсов, учетную запись хранение и контейнер, а затем создает виртуальную машину путем отправки виртуального жесткого диска в контейнер.
Он заменяет открытый ключ SSH вашим открытым ключом, чтобы предоставить вам доступ к виртуальной машине.

Потребуется загрузочный виртуальный жесткий диск. Скрипт ищет `~/sample.vhd`.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Пример скрипта

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/create-vm-vhd/create-vm-vhd.sh "Create VM using a VHD")]

## <a name="clean-up-deployment"></a>Очистка развертывания 

Выполните следующую команду, чтобы удалить группу ресурсов, виртуальную машину и все связанные с ней ресурсы.

```azurecli-interactive 
az group delete -n az-cli-vhd
```

## <a name="script-explanation"></a>Описание скрипта

Для создания группы ресурсов, виртуальной машины, группы доступности, балансировщика нагрузки и всех связанных ресурсов этот скрипт использует следующие команды. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Get-Help | Примечания |
|---|---|
| [az group create](/cli/azure/group) | Создает группу ресурсов, в которой хранятся все ресурсы. |
| [az storage account list](/cli/azure/storage/account) | Выводит список учетных записей хранения. |
| [az storage account check-name](/cli/azure/storage/account) | Проверяет допустимость имени учетной записи хранения и что она еще не существует. |
| [az storage account keys list](/cli/azure/storage/account/keys) | Выводит список ключей для учетных записей хранения. |
| [az storage blob exists](/cli/azure/storage/blob) | Проверяет, существует ли BLOB-объект. |
| [az storage container create](/cli/azure/storage/container) | Создает контейнер в учетной записи хранения. |
| [az storage blob upload](/cli/azure/storage/blob) | Создает BLOB-объект в контейнере путем отправки виртуального жесткого диска. |
| [az vm list](/cli/azure/vm) | Используется с `--query`, чтобы проверить, используется ли имя виртуальной машины. | 
| [az vm create](/cli/azure/vm/availability-set) | Создает виртуальные машины. |
| [az vm list-ip-addresses](/cli/azure/vm#az-vm-list-ip-addresses) | Возвращает IP-адрес созданной виртуальной машины. |

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения об Azure CLI см. в [документации по Azure CLI](/cli/azure).

Дополнительные примеры скриптов интерфейса командной строки для виртуальных машин см. в [документации по виртуальным машинам Azure под управлением Linux](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
