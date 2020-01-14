---
title: Пример скрипта Azure CLI. Развертывание управляемого приложения
description: Здесь представлен пример скрипта Azure CLI, с помощью которого можно развернуть определение управляемого приложения Azure в подписке.
author: tfitzmac
ms.devlang: azurecli
ms.topic: sample
ms.date: 10/25/2017
ms.author: tomfitz
ms.openlocfilehash: 346ea59209bc2f74970e708c947f5caa158a0338
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/03/2020
ms.locfileid: "75648791"
---
# <a name="deploy-a-managed-application-for-service-catalog-with-azure-cli"></a>Развертывание управляемого приложения для каталога службы с помощью Azure CLI

С помощью этого скрипта определение управляемого приложения развертывается из каталога служб. 


[!INCLUDE [sample-cli-install](../../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Пример скрипта

[!code-azurecli[main](../../../../cli_scripts/managed-applications/create-application/create-application.sh "Create application")]


## <a name="script-explanation"></a>Описание скрипта

В этом скрипте используется следующая команда для развертывания управляемого приложения. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Get-Help | Примечания |
|---|---|
| [az managedapp create](https://docs.microsoft.com/cli/azure/managedapp#az-managedapp-create) | Позволяет создать управляемое приложение. Укажите идентификатор определения и параметры для шаблона. |


## <a name="next-steps"></a>Дальнейшие действия

* Общие сведения об управляемых приложениях Azure см. в [этой статье](../overview.md).
* Дополнительные сведения об Azure CLI см. в [документации по Azure CLI](https://docs.microsoft.com/cli/azure).
