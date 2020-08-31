---
title: Краткое руководство. Клиентская библиотека Распознавателя документов | Документация Майкрософт
description: Приступите к работе с клиентской библиотекой [название продукта].
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 08/17/2020
ms.author: pafarley
ms.custom: devx-track-javascript
zone_pivot_groups: programming-languages-set-ten
ms.openlocfilehash: eb44456815642989a6f2b36cb15e159318018644
ms.sourcegitcommit: 5b6acff3d1d0603904929cc529ecbcfcde90d88b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/21/2020
ms.locfileid: "88723339"
---
# <a name="quickstart-use-the-form-recognizer-client-library"></a>Краткое руководство. Использование клиентской библиотеки Распознавателя документов

Здесь приведены сведения о том, как начать работу с клиентской библиотекой Распознавателя документов. Распознаватель документов — это служба когнитивных вычислений, использующая технологию машинного обучения для идентификации и извлечения пар "ключ — значение" и данных таблиц из документов-форм. Затем эта служба выводит структурированные данные, которые включают в себя связи в исходном файле. Выполните приведенные здесь действия, чтобы установить пакет SDK и протестировать пример кода для выполнения базовых задач.

Используйте клиентскую библиотеку Распознавателя документов для следующих целей:

* [распознавание содержимого формы;](#recognize-form-content)
* [распознавание квитанций](#recognize-receipts);
* [обучение пользовательской модели](#train-a-custom-model);
* [анализ документов с помощью пользовательской модели](#analyze-forms-with-a-custom-model);
* [управление пользовательскими моделями](#manage-your-custom-models).

> [!NOTE]
> Клиентские библиотеки поддерживают последнюю версию Распознавателя документов общедоступной версии 2.0. 


::: zone pivot="programming-language-csharp"

[!INCLUDE [C# SDK quickstart](../includes/quickstarts/csharp-sdk.md)]

::: zone-end

::: zone pivot="programming-language-java"

[!INCLUDE [Java SDK quickstart](../includes/quickstarts/java-sdk.md)]

::: zone-end

::: zone pivot="programming-language-javascript"

[!INCLUDE [NodeJS SDK quickstart](../includes/quickstarts/javascript-sdk.md)]

::: zone-end

::: zone pivot="programming-language-python"

[!INCLUDE [Python SDK quickstart](../includes/quickstarts/python-sdk.md)]

::: zone-end