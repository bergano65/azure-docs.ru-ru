---
title: Непрерывное развертывание из Azure Repos
description: Сведения об использовании Azure CLI для автоматизации развертывания приложения Службы приложений и управления им. В этом примере показано, как настроить CI/CD из Azure Repos.
author: msangapu-msft
tags: azure-service-management
ms.assetid: 389d3bd3-cd8e-4715-a3a1-031ec061d385
ms.devlang: azurecli
ms.topic: sample
ms.date: 12/11/2017
ms.author: msangapu
ms.custom: mvc, seodec18, devx-track-azurecli
ms.openlocfilehash: 01855fa7920a4e04cff9fa178083d094142d59d7
ms.sourcegitcommit: 648c8d250106a5fca9076a46581f3105c23d7265
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/27/2020
ms.locfileid: "88960927"
---
# <a name="create-an-app-service-app-with-continuous-deployment-using-azure-cli"></a>Создание приложения Службы приложений с непрерывным развертыванием с помощью Azure CLI

С помощью этого примера скрипта создается приложение в Службе приложений со связанными ресурсами, а затем настраивается непрерывное развертывание из репозитория Azure DevOps. Для этого примера вам потребуются следующие компоненты:

* репозиторий Azure DevOps с кодом приложения, для которого у вас есть права администратора;
* [личный маркер доступа (PAT)](/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate?view=vsts) для организации Azure DevOps.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Если вы решили установить и использовать CLI локально, вам понадобится Azure CLI 2.0 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI]( /cli/azure/install-azure-cli).

## <a name="sample-script"></a>Пример скрипта

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/deploy-vsts-continuous/deploy-vsts-continuous.sh?highlight=3-4 "Create an app with continuous deployment from Azure DevOps")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Описание скрипта

Этот скрипт использует следующие команды. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Get-Help | Примечания |
|---|---|
| [`az group create`](/cli/azure/group?view=azure-cli-latest#az-group-create) | Создает группу ресурсов, в которой хранятся все ресурсы. |
| [`az appservice plan create`](/cli/azure/appservice/plan?view=azure-cli-latest#az-appservice-plan-create) | Создает план службы приложений. |
| [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) | Создает приложение Службы приложений. |
| [`az webapp deployment source config`](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config) | Связывает приложение Службы приложений с репозиторием Git или Mercurial. |

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения об Azure CLI см. в [документации по Azure CLI](/cli/azure).

Дополнительные примеры скриптов Azure CLI для службы приложений см. в [документации по службе приложений Azure](../samples-cli.md).