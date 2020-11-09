---
title: Клиентские библиотеки и REST API Помощника по метрикам
titleSuffix: Azure Cognitive Services
description: Используйте это краткое руководство для подключения приложений к API Помощника по метрикам в Azure Cognitive Services.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: quickstart
ms.date: 10/14/2020
ms.author: mbullwin
zone_pivot_groups: programming-languages-metrics-monitor
ms.openlocfilehash: 5b3df5f4b41b2beeec68b667863f6ca7715df47b
ms.sourcegitcommit: 7a7b6c7ac0aa9dac678c3dfd4b5bcbc45dc030ca
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/02/2020
ms.locfileid: "93186965"
---
# <a name="quickstart-use-the-client-libraries-or-rest-apis-to-customize-your-solution"></a>Краткое руководство. Использование клиентских библиотек и REST API для настройки собственного решения

Узнайте, как начать работу с REST API или клиентскими библиотеками Помощника по метрикам. Выполните приведенные здесь действия, чтобы установить пакет и протестировать пример кода для выполнения базовых задач.

Помощник по метрикам позволяет выполнять следующие действия:

* добавление веб-канала данных из источника данных;
* проверка состояния приема;
* настройка обнаружения и оповещений; 
* запрашивание результатов обнаружения аномалий;
* диагностика аномалий.


::: zone pivot="programming-language-csharp"

[!INCLUDE [REST API quickstart](../includes/quickstarts/csharp.md)]

::: zone-end

::: zone pivot="programming-language-java"

[!INCLUDE [REST API quickstart](../includes/quickstarts/java.md)]

::: zone-end

::: zone pivot="programming-language-javascript"

[!INCLUDE [REST API quickstart](../includes/quickstarts/javascript.md)]

::: zone-end

::: zone pivot="programming-language-python"

[!INCLUDE [REST API quickstart](../includes/quickstarts/python.md)]

::: zone-end

::: zone pivot="programming-language-rest-api"

[!INCLUDE [REST API quickstart](../includes/quickstarts/rest-api.md)]

::: zone-end

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