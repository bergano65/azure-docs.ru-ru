---
title: Пример сценария Azure CLI. Получение ресурсов из управляемой группы ресурсов и изменение размера виртуальных машин | Документация Майкрософт
description: Пример сценария Azure CLI. Получение ресурсов из управляемой группы ресурсов и изменение размера виртуальных машин
services: managed-applications
documentationcenter: na
author: tfitzmac
manager: timlt
ms.service: managed-applications
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/25/2017
ms.author: tomfitz
ms.openlocfilehash: bbf03a0d53769c93a8aab304d3128ae0cc875a8f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "61365900"
---
# <a name="get-resources-in-a-managed-resource-group-and-resize-vms-with-azure-cli"></a>Получение ресурсов из управляемой группы ресурсов и изменение размера виртуальных машин с помощью Azure CLI

С помощью этого скрипта можно получить ресурсы из управляемой группы ресурсов и изменить размер виртуальных машин в этой группе.


[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Пример скрипта

[!code-azurecli[main](../../../cli_scripts/managed-applications/get-application/get-application.sh "Get application")]


## <a name="script-explanation"></a>Описание скрипта

В этом скрипте используются следующие команды для развертывания управляемого приложения. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Команда | Примечания |
|---|---|
| [az managedapp list](https://docs.microsoft.com/cli/azure/managedapp#az-managedapp-list) | Перечисление управляемых приложений Azure. Укажите значения запроса для поиска результатов. |
| [az resource list](https://docs.microsoft.com/cli/azure/resource#az-resource-list) | Перечисление ресурсов. Укажите группу ресурсов и значения запроса для поиска результатов. |
| [az vm resize](https://docs.microsoft.com/cli/azure/vm#az-vm-resize) | Обновление размера виртуальной машины. |


## <a name="next-steps"></a>Дополнительная информация

* Общие сведения об управляемых приложениях Azure см. в [этой статье](../overview.md).
* Дополнительные сведения об Azure CLI см. в [документации по Azure CLI](https://docs.microsoft.com/cli/azure).
