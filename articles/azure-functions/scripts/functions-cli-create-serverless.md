---
title: Создание бессерверного приложения-функции с помощью Azure CLI
description: Создание приложения-функции для бессерверного выполнения в Azure с помощью Azure CLI
ms.assetid: 0e221db6-ee2d-4e16-9bf6-a456cd05b6e7
ms.topic: sample
ms.date: 07/03/2018
ms.author: glenga
ms.custom: mvc
ms.openlocfilehash: 4bf0e43132662f6f5322485a59f356f872559092
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/26/2019
ms.locfileid: "74532507"
---
# <a name="create-a-function-app-for-serverless-code-execution"></a>Создание приложения-функции для выполнения кода без сервера 

При помощи этого примера скрипта в решении "Функции Azure" создается приложение-функция Azure, которое является контейнером для ваших функций. Приложение-функция создается с помощью [плана потребления](../functions-scale.md#consumption-plan), который идеально подходит для рабочих нагрузок, выполняемых на основе событий без использования сервера.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Если вы решили установить и использовать интерфейс командной строки локально, для работы с этой статьей вам понадобится Azure CLI 2.0 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Пример скрипта

Этот скрипт создает приложение-функцию Azure с помощью [плана потребления](../functions-scale.md#consumption-plan).

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/create-function-app-consumption/create-function-app-consumption.sh "Create an Azure Function on a consumption plan")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Описание скрипта

Для каждой команды в таблице приведены ссылки на соответствующую документацию. Этот сценарий использует следующие команды:

| Команда | Примечания |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Создает группу ресурсов, в которой хранятся все ресурсы. |
| [az storage account create](/cli/azure/storage/account#az-storage-account-create) | Создает учетную запись хранения Azure. |
| [az functionapp create](/cli/azure/functionapp#az-functionapp-create) | Создает приложение-функцию. |

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения об Azure CLI см. в [документации по Azure CLI](/cli/azure).

Дополнительные примеры сценариев Azure CLI для Функций Azure см. в [документации по Функциям Azure](../functions-cli-samples.md).
