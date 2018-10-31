---
title: Обучение версии приложения LUIS
titleSuffix: Azure Cognitive Services
description: Обучение версии приложения Интеллектуальной службы распознавания речи (LUIS) позволяет улучшить распознавание естественного языка. Следует обучать приложение LUIS после обновления модели, например добавления, изменения, пометки или удаления сущностей, намерений или фраз.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 10/18/2018
ms.author: diberry
ms.openlocfilehash: 362c5e2e7216d584a9858ace5fb607dc0ee126d5
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/19/2018
ms.locfileid: "49426110"
---
# <a name="train-your-luis-app-version"></a>Обучение версии приложения LUIS

Обучение приложения службы "Распознавание речи" (LUIS) позволяет улучшить распознавание естественного языка. Следует обучать приложение LUIS после обновления модели, например добавления, изменения, пометки или удаления сущностей, намерений или фраз. 

<!--
When you train a LUIS app by example, LUIS generalizes from the examples you have labeled, and it learns to recognize the relevant intents and entities. This teaches LUIS to improve classification accuracy in the future. -->

Обучение и [тестирование](luis-concept-test.md) приложения — это итеративный процесс. Обученное приложение LUIS следует протестировать с использованием примеров фраз, чтобы проверить правильность распознавания намерений и сущностей. Если распознавание выполняется неправильно, внесите изменения в приложение, а затем обучите его и протестируйте еще раз. 

Обучение применяется к активной версии на портале LUIS. 

## <a name="how-to-train-interactively"></a>Обучение в интерактивном режиме

Чтобы начать итеративный процесс на [портале LUIS](https://www.luis.ai), необходимо сначала хотя бы один раз обучить приложение LUIS. Перед обучением убедитесь, что для каждого намерения имеется минимум одно высказывание.

1. Откройте приложение, выбрав его имя на странице **Мои приложения**. 

2. В приложении выберите **Обучить** на панели сверху. 

3. После завершения обучения в верхней части браузера появится зеленая строка уведомлений.

<!-- The following note refers to what might cause the error message "Training failed: FewLabels for model: <ModelName>" -->

>[!NOTE]
>Если в приложении есть одно или несколько намерений, которые не содержат примеров фраз, такое приложение обучить нельзя. Добавьте фразы для всех намерений. Дополнительные сведения см. в статье о [добавлении примеров фраз](luis-how-to-add-example-utterances.md).

## <a name="train-with-all-data"></a>Обучение на основе всех данных

При обучении используется небольшой процент отрицательных выборок. Если нужно использовать все данные вместо небольшой отрицательной выборки, используйте [API настройки версий](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) с заданным для `UseAllTrainingData` значением true, чтобы отключить эту функцию. 

## <a name="unnecessary-training"></a>Необязательное обучение

Нет необходимости проводить обучение после каждого отдельного изменения. Обучение должно выполняться после группы изменений, применяемых к модели, а также когда следующим этапом будет тестирование или публикация. Если тестирование или публикация не требуются, проводить обучение необязательно. 

## <a name="training-with-the-rest-apis"></a>Обучение с помощью REST API

Обучение на портале LUIS выполняется в одно действие — нажатием кнопки **Обучить**. Обучение с помощью REST API — это процесс из двух этапов. Первый этап — [запрос обучения](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c45) с помощью HTTP POST. Второй этап — запрос [состояния обучения](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c46) с помощью HTTP GET. 

Чтобы узнать о завершении обучения, необходимо выполнять опрос состояния до тех пор, пока все модели не будут успешно обучены. 

## <a name="next-steps"></a>Дополнительная информация

* [Label suggested utterances with LUIS](luis-how-to-review-endoint-utt.md) (Добавление меток к предлагаемым фразам в LUIS) 
* [Use features to improve your LUIS app's performance](luis-how-to-add-features.md) (Использование функций для повышения производительности приложения LUIS) 