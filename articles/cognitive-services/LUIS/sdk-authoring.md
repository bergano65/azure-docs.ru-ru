---
title: Краткое руководство. Клиентская библиотека для разработки Распознавания речи (LUIS)
description: В этом кратком руководстве описано, как приступить к работе с клиентской библиотекой LUIS. Выполните приведенные здесь действия, чтобы установить пакет и протестировать пример кода для выполнения базовых задач.
ms.topic: quickstart
ms.date: 01/14/2020
zone_pivot_groups: programming-languages-set-diberry-3core
ms.openlocfilehash: 16aa45ab0f0860b93815601a2f82eff34dbd1f6f
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/28/2020
ms.locfileid: "76774207"
---
# <a name="quickstart-language-understanding-luis-authoring-client-library"></a>Краткое руководство. Клиентская библиотека для разработки Распознавания речи (LUIS)

Начните работу с клиентской библиотекой LUIS. Выполните приведенные здесь действия, чтобы установить пакет SDK и протестировать пример кода для выполнения базовых задач.  Распознавание речи (LUIS) позволяет применять пользовательскую аналитику машинного обучения к тексту пользователя в разговорном стиле и на естественном языке, чтобы предсказать общий смысл и извлечь соответствующую подробную информацию.

::: zone pivot="programming-language-csharp"
[!INCLUDE [Get intent with C# SDK](./includes/sdk-csharp-authoring.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Get intent with Python SDK](./includes/sdk-python-authoring.md)]
::: zone-end


::: zone pivot="programming-language-nodejs"
[!INCLUDE [Get intent with Node.js SDK](./includes/sdk-nodejs-authoring.md)]
::: zone-end

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
>[Краткое руководство. Запрос конечной точки прогнозирования версии 3 с помощью пакета SDK .NET для C#](sdk-query-prediction-endpoint.md)

* [Что такое служба "Распознавание речи" (LUIS)?](what-is-luis.md)
* [Новые возможности](whats-new.md)
* [Намерения](luis-concept-intent.md), [сущности](luis-concept-entity-types.md), [примеры речевых фрагментов](luis-concept-utterance.md) и [готовые сущности](luis-reference-prebuilt-entities.md).
* Исходный код для этого шаблона можно найти на портале [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/documentation-samples/quickstarts/LUIS/LUIS.cs).
