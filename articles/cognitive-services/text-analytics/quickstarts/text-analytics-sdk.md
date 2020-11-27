---
title: Краткое руководство. Интеллектуальный анализ текста с помощью клиентской библиотеки Анализа текста
titleSuffix: Azure Cognitive Services
description: В этом кратком руководстве описывается, как выполнить анализ тональности и другие действия с помощью API "Анализ текста" служб Azure Cognitive Services.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 10/07/2020
ms.author: aahi
keywords: Интеллектуальный анализ текста, анализ тональности, анализ текста
ms.custom: devx-track-python, devx-track-js, devx-track-csharp, cog-serv-seo-aug-2020
zone_pivot_groups: programming-languages-text-analytics
ms.openlocfilehash: 5a0856df71f87e49c1a7d627ba92419352c796d5
ms.sourcegitcommit: f311f112c9ca711d88a096bed43040fcdad24433
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2020
ms.locfileid: "94980951"
---
# <a name="quickstart-use-the-text-analytics-client-library"></a>Краткое руководство. Использование клиентской библиотеки Анализа текста

Изучите эту статью, чтобы начать работу с клиентской библиотекой Анализа текста. Выполните приведенные здесь действия, чтобы установить пакет и протестировать пример кода для интеллектуального анализа текста.

Используйте клиентскую библиотеку Анализа текста для выполнения следующих действий:

* Анализ мнений
* Определение языка
* Распознавание сущностей
* Извлечение ключевой фразы
* Интеллектуальный анализ данных

::: zone pivot="programming-language-csharp"

> [!IMPORTANT]
> * `3.0` — это последняя стабильная версия API Анализа текста.
>    * Обязательно следуйте указаниям по используемой версии.
> * В коде, приведенном в этой статье, для простоты используются синхронные методы и незащищенное хранилище учетных данных. Для рабочих сценариев рекомендуется использовать пакетные асинхронные методы для повышения производительности и масштабируемости. См. справочную документацию ниже.
> * Если вы намерены использовать решение "Анализ текста для здравоохранения" или асинхронные операции, ознакомьтесь с примерами на [C#](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics), [Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/) или [Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/textanalytics/azure-ai-textanalytics) на сайте GitHub.

[!INCLUDE [v3 region availability](../includes/v3-region-availability.md)]

[!INCLUDE [C# quickstart](../includes/quickstarts/csharp-sdk.md)]

::: zone-end

::: zone pivot="programming-language-java"

> [!IMPORTANT]
> * `3.0` — это последняя стабильная версия API Анализа текста.
> * В коде, приведенном в этой статье, для простоты используются синхронные методы и незащищенное хранилище учетных данных. Для рабочих сценариев рекомендуется использовать пакетные асинхронные методы для повышения производительности и масштабируемости. См. справочную документацию ниже.
Если вы намерены использовать решение "Анализ текста для здравоохранения" или асинхронные операции, ознакомьтесь с примерами на [C#](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics), [Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/) или [Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/textanalytics/azure-ai-textanalytics) на сайте GitHub.

[!INCLUDE [Java quickstart](../includes/quickstarts/java-sdk.md)]

::: zone-end

::: zone pivot="programming-language-javascript"

> [!IMPORTANT]
> * `3.0` — это последняя стабильная версия API Анализа текста.
>    * Обязательно следуйте указаниям по используемой версии.
> * В коде, приведенном в этой статье, для простоты используются синхронные методы и незащищенное хранилище учетных данных. Для рабочих сценариев рекомендуется использовать пакетные асинхронные методы для повышения производительности и масштабируемости. См. справочную документацию ниже.
> * Вы также можете запустить эту версию клиентской библиотеки службы "Анализ текста" [в браузере](https://github.com/Azure/azure-sdk-for-js/blob/master/documentation/Bundling.md).

[!INCLUDE [NodeJS quickstart](../includes/quickstarts/nodejs-sdk.md)]

::: zone-end

::: zone pivot="programming-language-python"

> [!IMPORTANT]
> * `3.0` — это последняя стабильная версия API Анализа текста.
>    * Обязательно следуйте указаниям по используемой версии.
> * В коде, приведенном в этой статье, для простоты используются синхронные методы и незащищенное хранилище учетных данных. Для рабочих сценариев рекомендуется использовать пакетные асинхронные методы для повышения производительности и масштабируемости. См. справочную документацию ниже. Если вы намерены использовать решение "Анализ текста для здравоохранения" или асинхронные операции, ознакомьтесь с примерами на [C#](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics), [Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/) или [Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/textanalytics/azure-ai-textanalytics) на сайте GitHub.

[!INCLUDE [Python quickstart](../includes/quickstarts/python-sdk.md)]

::: zone-end

::: zone pivot="programming-language-other"

## <a name="additional-language-support"></a>Поддержка дополнительных возможностей языка

Если вы щелкнули эту вкладку, вы, вероятно, не увидели краткого руководства для предпочитаемого языка программирования. Не беспокойтесь, у нас есть дополнительные краткие руководства. Используйте таблицу, чтобы найти правильный пример для своего языка программирования.

| Язык | Доступная версия | 
|----------|------------------------|
| Ruby     | [Версия 2.1](ruby-sdk.md) | 
| Go       | [Версия 2.1](go-sdk.md) | 

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
