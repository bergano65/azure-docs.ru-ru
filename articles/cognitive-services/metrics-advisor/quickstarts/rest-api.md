---
title: Краткое руководство. REST API Помощника по метрикам
titleSuffix: Azure Cognitive Services
description: Используйте это краткое руководство для подключения приложений к API Помощника по метрикам в Azure Cognitive Services.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: quickstart
ms.date: 09/23/2020
ms.author: mbullwin
ms.openlocfilehash: cf6c0ba43bc7bb725d9df4707a1a6f931ad9347e
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/14/2020
ms.locfileid: "92047474"
---
# <a name="quickstart-use-the-rest-apis-to-customize-your-solution"></a>Краткое руководство. Настройка решения с помощью REST API

Начните работу с REST API Помощника по метрикам. Выполните приведенные здесь действия, чтобы установить пакет и протестировать пример кода для выполнения базовых задач.

Помощник по метрикам позволяет выполнять следующие действия:

* добавление веб-канала данных из источника данных;
* проверка состояния приема;
* настройка обнаружения и оповещений; 
* запрашивание результатов обнаружения аномалий;
* диагностика аномалий.

[!INCLUDE [REST API quickstart](../includes/quickstarts/rest-api.md)]

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вы хотите очистить и удалить подписку Cognitive Services, вы можете удалить ресурс или группу ресурсов. При этом удаляются все ресурсы, связанные с ней.

* [Портал](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Дальнейшие действия

- [Использование веб-портала](web-portal.md)
- [Подключение веб-каналов данных](../how-tos/onboard-your-data.md)
    - [Управление веб-каналами данных](../how-tos/manage-data-feeds.md)
    - [Конфигурации для разных источников данных](../data-feeds-from-different-sources.md)
- [Настройка метрик и детальная настройка конфигурации обнаружения](../how-tos/configure-metrics.md)
- [Настройка обнаружения аномалий с помощью обратной связи](../how-tos/anomaly-feedback.md)