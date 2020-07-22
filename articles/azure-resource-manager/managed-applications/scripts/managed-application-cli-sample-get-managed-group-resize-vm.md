---
title: Получение управляемой группы ресурсов и изменение размера виртуальных машин с помощью Azure CLI
description: Здесь представлен пример скрипта Azure CLI, с помощью которого можно получить управляемую группу ресурсов в управляемом приложении Azure. Скрипт позволяет изменять размеры виртуальных машин.
author: tfitzmac
ms.devlang: azurecli
ms.topic: sample
ms.date: 10/25/2017
ms.author: tomfitz
ms.openlocfilehash: f119fe4b4547bda8249a3620baf938dd8b83c235
ms.sourcegitcommit: bcb962e74ee5302d0b9242b1ee006f769a94cfb8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/07/2020
ms.locfileid: "86056052"
---
# <a name="get-resources-in-a-managed-resource-group-and-resize-vms-with-azure-cli"></a>Получение ресурсов из управляемой группы ресурсов и изменение размера виртуальных машин с помощью Azure CLI

С помощью этого скрипта можно получить ресурсы из управляемой группы ресурсов и изменить размер виртуальных машин в этой группе.


[!INCLUDE [sample-cli-install](../../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Пример скрипта

[!code-azurecli[main](../../../../cli_scripts/managed-applications/get-application/get-application.sh "Get application")]


## <a name="script-explanation"></a>Описание скрипта

В этом скрипте используются следующие команды для развертывания управляемого приложения. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Get-Help | Примечания |
|---|---|
| [az managedapp list](/cli/azure/managedapp#az-managedapp-list) | Перечисление управляемых приложений Azure. Укажите значения запроса для поиска результатов. |
| [az resource list](/cli/azure/resource#az-resource-list) | Перечисление ресурсов. Укажите группу ресурсов и значения запроса для поиска результатов. |
| [az vm resize](/cli/azure/vm#az-vm-resize) | Обновление размера виртуальной машины. |


## <a name="next-steps"></a>Дальнейшие действия

* Общие сведения об управляемых приложениях Azure см. в [этой статье](../overview.md).
* Дополнительные сведения об Azure CLI см. в [документации по Azure CLI](/cli/azure).
