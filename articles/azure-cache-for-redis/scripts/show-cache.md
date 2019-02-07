---
title: Пример скрипта Azure CLI. Получение сведений о кэше Redis для Azure | Документация Майкрософт
description: Пример скрипта Azure CLI. Получение сведений о кэше Redis для Azure
services: azure-cache-for-redis
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
tags: azure-service-management
ms.assetid: 155924e6-00d5-4a8c-ba99-5189f300464a
ms.service: cache-redis
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 08/30/2017
ms.author: wesmc
ms.openlocfilehash: 7bcdd999a4954766398800e6e6a0ddb8c9727a99
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/06/2019
ms.locfileid: "55749165"
---
# <a name="get-details-of-an-azure-cache-for-redis"></a>Получение сведений о кэше Redis для Azure

В этом сценарии описывается, как получить сведения об экземпляре кэша Redis для Azure, включая его состояние подготовки.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Пример скрипта

[!code-azurecli[main](../../../cli_scripts/redis-cache/show-cache/show-cache.sh "Azure Cache for Redis")]

## <a name="script-explanation"></a>Описание скрипта

Для получения сведений об экземпляре кэша Redis для Azure в этом сценарии используются приведенные ниже команды. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Команда | Примечания |
|---|---|
| [az redis show](https://docs.microsoft.com/cli/azure/redis) | Извлекает сведения об экземпляре кэша Redis для Azure. |


## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения об Azure CLI см. в [документации по Azure CLI](https://docs.microsoft.com/cli/azure).

Дополнительные примеры скриптов Azure CLI для кэша Azure для Redis см. в [документации по кэшу Azure для Redis](../cli-samples.md).
