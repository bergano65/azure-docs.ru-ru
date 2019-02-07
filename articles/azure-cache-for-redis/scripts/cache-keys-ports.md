---
title: Пример сценария Azure CLI — получение имени узла, портов и ключей для кэша Redis для Azure | Документация Майкрософт
description: Пример сценария Azure CLI — получение имени узла, портов и ключей для экземпляра кэша Redis для Azure
services: azure-cache-for-redis
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
tags: azure-service-management
ms.assetid: 761eb24e-2ba7-418d-8fc3-431153e69a90
ms.service: cache-redis
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 08/30/2017
ms.author: wesmc
ms.openlocfilehash: fd69c3182f026996618fea5e26466e8434ea36f6
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/06/2019
ms.locfileid: "55754461"
---
# <a name="get-the-hostname-ports-and-keys-for-azure-cache-for-redis"></a>Получение имени узла, портов и ключей для кэша Redis для Azure

В этом сценарии вы узнаете, как получить имя узла, порты и ключи, используемые для подключения к экземпляру кэша Redis для Azure.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Пример скрипта

[!code-azurecli[main](../../../cli_scripts/redis-cache/cache-keys-ports/cache-keys-ports.sh "Azure Cache for Redis")]


## <a name="script-explanation"></a>Описание скрипта

Чтобы получить имя узла, порты и ключи для экземпляра кэша Redis для Azure, в этом сценарии используются приведенные ниже команды. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Команда | Примечания |
|---|---|
| [az redis show](https://docs.microsoft.com/cli/azure/redis) | Извлекает сведения об экземпляре кэша Redis для Azure. |
| [az redis list-keys](https://docs.microsoft.com/cli/azure/redis) | Извлекает ключи доступа для экземпляра кэша Redis для Azure. |


## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения об Azure CLI см. в [документации по Azure CLI](https://docs.microsoft.com/cli/azure).

Дополнительные примеры скриптов Azure CLI для кэша Azure для Redis см. в [документации по кэшу Azure для Redis](../cli-samples.md).
