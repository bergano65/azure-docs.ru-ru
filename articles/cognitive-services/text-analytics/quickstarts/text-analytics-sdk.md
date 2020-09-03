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
ms.date: 07/27/2020
ms.author: aahi
ms.custom: devx-track-python, devx-track-javascript, devx-track-csharp
zone_pivot_groups: programming-languages-text-analytics
ms.openlocfilehash: 2d81175c73928d25c06acb396dac587c7cc73637
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/26/2020
ms.locfileid: "88931188"
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
> * `3.0` — это последняя стабильная версия API Анализа текста.
>    * Обязательно следуйте указаниям по используемой версии.
> * В коде, приведенном в этой статье, для простоты используются синхронные методы и незащищенное хранилище учетных данных. Для рабочих сценариев рекомендуется использовать пакетные асинхронные методы для повышения производительности и масштабируемости. См. справочную документацию ниже.

[!INCLUDE [C# quickstart](../includes/quickstarts/csharp-sdk.md)]

::: zone-end

::: zone pivot="programming-language-java"

> [!IMPORTANT]
> * `3.0` — это последняя стабильная версия API Анализа текста.
> * В коде, приведенном в этой статье, для простоты используются синхронные методы и незащищенное хранилище учетных данных. Для рабочих сценариев рекомендуется использовать пакетные асинхронные методы для повышения производительности и масштабируемости. См. справочную документацию ниже.

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
