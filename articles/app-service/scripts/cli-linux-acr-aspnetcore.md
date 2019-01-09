---
title: Пример скрипта Azure CLI. Создание веб-приложения ASP.NET Core в Реестре контейнеров Azure | Документация Майкрософт
description: Пример сценария Azure CLI. Создание приложения ASP.NET Core в контейнере Docker в Службе приложений из Реестра контейнеров Azure
services: appservice
documentationcenter: appservice
author: msangapu
manager: jeconnoc
editor: ''
tags: azure-service-management
ms.assetid: 3a2d1983-ff7b-476a-ac44-49ec2aabb31a
ms.service: app-service
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 12/13/2018
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: c05572d4962eca042a807be3eec94462d0830e1b
ms.sourcegitcommit: e68df5b9c04b11c8f24d616f4e687fe4e773253c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/20/2018
ms.locfileid: "53653379"
---
# <a name="create-an-aspnet-core-app-in-a-docker-container-in-app-service-from-azure-container-registry"></a>Создание приложения ASP.NET Core в контейнере Docker в Службе приложений из Реестра контейнеров Azure

При помощи этого примера сценария создается группа ресурсов, план службы приложений Linux и приложение. Затем приложение ASP.NET Core развертывается с использованием контейнера Docker из реестра контейнеров Azure.


[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

Вам понадобится Azure CLI версии 2.0.52 или более поздней. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI]( /cli/azure/install-azure-cli).

## <a name="sample-script"></a>Пример скрипта

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/deploy-linux-acr/deploy-linux-acr.sh "Linux Azure Container Registry")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Описание скрипта

Для создания группы ресурсов, приложения Службы приложений и всех связанных с ними ресурсов этот сценарий использует следующие команды. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Get-Help | Примечания |
|---|---|
| [`az group create`](/cli/azure/group?view=azure-cli-latest#az-group-create) | Создает группу ресурсов, в которой хранятся все ресурсы. |
| [`az appservice plan create`](/cli/azure/appservice/plan?view=azure-cli-latest#az-appservice-plan-create) | Создает план службы приложений. |
| [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) | Создает приложение Службы приложений. |
| [`az webapp config container set`](/cli/azure/webapp/config/container?view=azure-cli-latest#az-webapp-config-container-set) | Настраивает контейнер Docker для приложения Службы приложений. |

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения об Azure CLI см. в [документации по Azure CLI](https://docs.microsoft.com/cli/azure).

Дополнительные примеры скриптов Azure CLI для службы приложений см. в [документации по службе приложений Azure](../samples-cli.md).
