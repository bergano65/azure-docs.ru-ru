---
title: Создание зоны и записи DNS для доменного имени — Azure CLI — Azure DNS
description: В этом примере скрипта Azure CLI показано, как создать зону и запись DNS для имени домена
services: dns
author: vhorne
ms.service: dns
ms.topic: sample
ms.date: 09/20/2019
ms.author: victorh
ms.openlocfilehash: 8ae4e8c515b658ac3f0bb4eb0d88706814993355
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/13/2019
ms.locfileid: "74037153"
---
# <a name="azure-cli-script-example-create-a-dns-zone-and-record"></a>Пример скрипта Azure CLI. Создание зоны и записи DNS

С помощью этого примера скрипта Azure CLI создается зона и запись DNS для имени домена. 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Пример скрипта

[!code-azurecli-interactive[main](../../../cli_scripts/dns/create-dns-zone-and-record/create-dns-zone-and-record.sh "Create DNS zone and record")]

## <a name="clean-up-deployment"></a>Очистка развертывания 

Выполните следующую команду, чтобы удалить группу ресурсов, зону DNS и все связанные с ними ресурсы.

```azurecli
az group delete -n myResourceGroup
```

## <a name="script-explanation"></a>Описание скрипта

Для создания группы ресурсов, виртуальной машины, группы доступности, балансировщика нагрузки и всех связанных ресурсов этот скрипт использует следующие команды. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Команда | Примечания |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Создает группу ресурсов, в которой хранятся все ресурсы. |
| [az network dns zone create](/cli/azure/network/dns/zone#az-network-dns-zone-create) | Создает зону Azure DNS. |
| [az network dns record-set a add-record](/cli/azure/network/dns/record-set) | Добавляет запись *A* в зону DNS. |
| [az network dns record-set list](/cli/azure/network/dns/record-set) | Выводит список всех наборов записей *A* в зоне DNS. |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#az-vm-extension-set) | Удаляет группу ресурсов со всеми вложенными ресурсами. |

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения об Azure CLI см. в [документации по Azure CLI](https://docs.microsoft.com/cli/azure).

