---
title: Создание приложения-функции с помощью развертывания GitHub — Azure CLI
description: Создайте приложение-функцию и разверните код функции из репозитория GitHub с помощью решения "Функции Azure".
ms.date: 07/03/2018
ms.topic: sample
ms.custom: mvc
ms.openlocfilehash: e424737b67be58ac0c3880ad2454c87fab803a15
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/24/2020
ms.locfileid: "75922733"
---
# <a name="create-a-function-app-in-azure-that-is-deployed-from-github"></a>Создание в Azure приложения-функции, которое развертывается из GitHub

В этом примере скрипта, написанного при помощи решения "Функции Azure", создается приложение-функция с использованием [плана потребления](../functions-scale.md#consumption-plan) и связанные с ним ресурсы. Код функции настраивается для непрерывного развертывания из репозитория GitHub. 

Для этого примера вам потребуются следующие компоненты:

* Репозиторий GitHub с кодом функции, для которого у вас есть права администратора.
* [Личный маркер доступа](https://help.github.com/articles/creating-an-access-token-for-command-line-use) для учетной записи GitHub.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Если вы решили использовать Azure CLI локально, установите версию 2.0 или более позднюю. Чтобы определить версию Azure CLI, выполните `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Пример скрипта

Этот пример создает приложения-функцию Azure и развертывает код функции из GitHub.

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/deploy-function-app-with-function-github-continuous/deploy-function-app-with-function-github-continuous.sh?highlight=3-4 "Azure Service")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Описание скрипта

Для каждой команды в таблице приведены ссылки на соответствующую документацию. Этот сценарий использует следующие команды:

| Get-Help | Примечания |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Создает группу ресурсов, в которой хранятся все ресурсы. |
| [az storage account create](/cli/azure/storage/account#az-storage-account-create) | Создает учетную запись хранения, необходимую для приложения-функции. |
| [az functionapp create](/cli/azure/functionapp#az-functionapp-create) | Создает приложение-функцию в бессерверном [плане потребления](../functions-scale.md#consumption-plan) и связывает его с репозиторием Git или Mercurial. |

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения об Azure CLI см. в [документации по Azure CLI](/cli/azure).

Дополнительные примеры сценариев Azure CLI для Функций Azure см. в [документации по Функциям Azure](../functions-cli-samples.md).
