---
title: Создание бессерверного приложения-функции Python с помощью Azure CLI
description: Создание бессерверного приложения-функции Python с помощью Azure CLI
ms.assetid: 0e221db6-ee2d-4e16-9bf6-a456cd05b6e7
ms.topic: sample
ms.date: 11/23/2019
ms.openlocfilehash: c55934c42db6bb7248db107b97fb8998163fae8b
ms.sourcegitcommit: f34165bdfd27982bdae836d79b7290831a518f12
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/13/2020
ms.locfileid: "75922625"
---
# <a name="create-a-serverless-python-function-app-using-azure-cli"></a>Создание бессерверного приложения-функции Python с помощью Azure CLI

При помощи этого примера скрипта в решении "Функции Azure" создается приложение-функция Azure, которое является контейнером для ваших функций. 

>[!NOTE]
>Созданное приложение-функция выполняется в Python версии 3.6. Версия Python 3.7 также поддерживается Функциями Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Если вы решили установить и использовать интерфейс командной строки локально, для работы с этой статьей вам понадобится Azure CLI 2.0 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Пример скрипта

Этот скрипт создает приложение-функцию Azure с помощью [плана потребления](../functions-scale.md#consumption-plan).

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/create-function-app-consumption-python/create-function-app-consumption-python.sh "Create an Azure Function on a Consumption plan")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Описание скрипта

Для каждой команды в таблице приведены ссылки на соответствующую документацию. Этот сценарий использует следующие команды:

| Get-Help | Примечания |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Создает группу ресурсов, в которой хранятся все ресурсы. |
| [az storage account create](/cli/azure/storage/account#az-storage-account-create) | Создает учетную запись хранения Azure. |
| [az functionapp create](/cli/azure/functionapp#az-functionapp-create) | Создает приложение-функцию. |

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения об Azure CLI см. в [документации по Azure CLI](/cli/azure).

Дополнительные примеры сценариев Azure CLI для Функций Azure см. в [документации по Функциям Azure](../functions-cli-samples.md).
