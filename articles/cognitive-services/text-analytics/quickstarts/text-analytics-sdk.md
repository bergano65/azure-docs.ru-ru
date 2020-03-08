---
title: Краткое руководство. Клиентская библиотека Анализа текста версии 3 | Документация Майкрософт
titleSuffix: Azure Cognitive Services
description: Используйте это краткое руководство для подключения приложений к API Анализа текста в Azure Cognitive Services.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 02/24/2020
ms.author: aahi
zone_pivot_groups: programming-languages-text-analytics
ms.openlocfilehash: c6883b9b79adfc4f91af354147d45ea27d806d78
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78396135"
---
# <a name="quickstart-use-the-text-analytics-client-library"></a>Краткое руководство. Использование клиентской библиотеки Анализа текста

Начните работу с клиентской библиотекой Анализа текста. Выполните приведенные здесь действия, чтобы установить пакет и протестировать пример кода для выполнения базовых задач.

Используйте клиентскую библиотеку Анализа текста для выполнения следующих действий:

* Анализ мнений
* Определение языка
* Распознавание сущностей
* Извлечение ключевой фразы

::: zone pivot="programming-language-csharp"

> [!IMPORTANT]
> * Последняя предварительная версия API Анализа текста — `3.0-preview`, которая включает в себя общедоступную предварительную версию улучшенных функций [анализа тональности](../how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features) и [распознавания именованных сущностей (NER)](../how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features). Последняя стабильная версия — `2.1`.
>    * Обязательно следуйте указаниям по используемой версии.
> * В коде, приведенном в этой статье, для простоты используются синхронные методы и незащищенное хранилище учетных данных. Для рабочих сценариев рекомендуется использовать пакетные асинхронные методы для повышения производительности и масштабируемости. См. справочную документацию ниже.

[!INCLUDE [C# quickstart](../includes/quickstarts/csharp-sdk.md)]

::: zone-end

::: zone pivot="programming-language-java"

> [!IMPORTANT]
> * В этом кратком руководстве используется только версия `3.0-preview` клиентской библиотеки Анализа текста, которая включает в себя общедоступную предварительную версию улучшенных функций [анализа тональности](../how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features) и [распознавания именованных сущностей (NER)](../how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features).
>    * Обязательно следуйте указаниям по используемой версии.
> * В коде, приведенном в этой статье, для простоты используются синхронные методы и незащищенное хранилище учетных данных. Для рабочих сценариев рекомендуется использовать пакетные асинхронные методы для повышения производительности и масштабируемости. См. справочную документацию ниже.

[!INCLUDE [Java quickstart](../includes/quickstarts/java-sdk.md)]

::: zone-end

::: zone pivot="programming-language-javascript"

> [!IMPORTANT]
> * Последняя предварительная версия API Анализа текста — `3.0-preview`, которая включает в себя общедоступную предварительную версию улучшенных функций [анализа тональности](../how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features) и [распознавания именованных сущностей (NER)](../how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features). Последняя стабильная версия — `2.1`.
>    * Обязательно следуйте указаниям по используемой версии.
> * В коде, приведенном в этой статье, для простоты используются синхронные методы и незащищенное хранилище учетных данных. Для рабочих сценариев рекомендуется использовать пакетные асинхронные методы для повышения производительности и масштабируемости. См. справочную документацию ниже.

[!INCLUDE [NodeJS quickstart](../includes/quickstarts/nodejs-sdk.md)]

::: zone-end

::: zone pivot="programming-language-python"

> [!IMPORTANT]
> * Последняя предварительная версия API Анализа текста — `3.0-preview`, которая включает в себя общедоступную предварительную версию улучшенных функций [анализа тональности](../how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features) и [распознавания именованных сущностей (NER)](../how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features). Последняя стабильная версия — `2.1`.
>    * Обязательно следуйте указаниям по используемой версии.
> * В коде, приведенном в этой статье, для простоты используются синхронные методы и незащищенное хранилище учетных данных. Для рабочих сценариев рекомендуется использовать пакетные асинхронные методы для повышения производительности и масштабируемости. См. справочную документацию ниже. 

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
