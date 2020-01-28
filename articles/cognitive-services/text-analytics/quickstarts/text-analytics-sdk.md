---
title: Краткое руководство. Клиентская библиотека Анализа текста версии 3 | Документация Майкрософт
titleSuffix: Azure Cognitive Services
description: В этом кратком руководстве описывается, как определить язык с помощью API Анализа текста из Azure Cognitive Services.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 01/15/2020
ms.author: aahi
zone_pivot_groups: programming-languages-set-six
ms.openlocfilehash: 41e6828f03533bfd239f6408a580150e614eb5e7
ms.sourcegitcommit: d9ec6e731e7508d02850c9e05d98d26c4b6f13e6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/20/2020
ms.locfileid: "76281192"
---
# <a name="quickstart-use-the-text-analytics-client-library-v3"></a>Краткое руководство. Использование клиентской библиотеки Анализа текста версии 3

Начните работу с клиентской библиотекой Анализа текста. Выполните приведенные здесь действия, чтобы установить пакет и протестировать пример кода для выполнения базовых задач.

Используйте клиентскую библиотеку Анализа текста версии `3.0-preview` для выполнения следующих действий.

* Анализ тональности (общедоступная предварительная версия)
* Определение языка
* Распознавание именованных сущностей (общедоступная предварительная версия)
* Извлечение ключевой фразы

::: zone pivot="programming-language-csharp"

[!INCLUDE [C# quickstart](../includes/quickstarts/v3/csharp.md)]

::: zone-end

::: zone pivot="programming-language-python"

[!INCLUDE [Python quickstart](../includes/quickstarts/v3/python-sdk.md)]

::: zone-end

::: zone pivot="programming-language-java"

[!INCLUDE [Java quickstart](../includes/quickstarts/v3/java-sdk.md)]

::: zone-end

::: zone pivot="programming-language-nodejs"

[!INCLUDE [NodeJS quickstart](../includes/quickstarts/v3/nodejs-sdk.md)]

::: zone-end

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вы хотите очистить и удалить подписку Cognitive Services, вы можете удалить ресурс или группу ресурсов. При этом удаляются все ресурсы, связанные с ней.

* [Портал](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Изучение решения](../text-analytics-user-scenarios.md#analyze-recorded-inbound-customer-calls)

* [Text Analytics overview](../overview.md) (Общие сведения об анализе текста)
* [Пример. Как определить тональность с помощью Анализа текста](../how-tos/text-analytics-how-to-sentiment-analysis.md)
* [Распознавание сущностей](../how-tos/text-analytics-how-to-entity-linking.md)
* [Пример. Как определить язык с помощью Анализа текста](../how-tos/text-analytics-how-to-keyword-extraction.md)
* [Распознавание языка](../how-tos/text-analytics-how-to-language-detection.md)
