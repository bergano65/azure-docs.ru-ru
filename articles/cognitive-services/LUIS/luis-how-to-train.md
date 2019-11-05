---
title: Обучение App-LUIS
titleSuffix: Azure Cognitive Services
description: Обучение версии приложения Интеллектуальной службы распознавания речи (LUIS) позволяет улучшить распознавание естественного языка. Следует обучать приложение LUIS после обновления модели, например добавления, изменения, пометки или удаления сущностей, намерений или фраз.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: 259ea23c05f0c0a138ad54b6efd11aad2061cf7a
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73500231"
---
# <a name="train-your-active-version-of-the-luis-app"></a>Обучение активной версии приложения LUIS 

Обучение приложения службы "Распознавание речи" (LUIS) позволяет улучшить распознавание естественного языка. Следует обучать приложение LUIS после обновления модели, например добавления, изменения, пометки или удаления сущностей, намерений или фраз. 

Обучение и [тестирование](luis-concept-test.md) приложения — это итеративный процесс. Обученное приложение LUIS следует протестировать с использованием примеров фраз, чтобы проверить правильность распознавания намерений и сущностей. Если распознавание выполняется неправильно, внесите изменения в приложение, а затем обучите его и протестируйте еще раз. 

Обучение применяется к активной версии на портале LUIS. 

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

## <a name="how-to-train-interactively"></a>Обучение в интерактивном режиме

Чтобы начать итеративный процесс на [портале LUIS](https://www.luis.ai), необходимо сначала хотя бы один раз обучить приложение LUIS. Перед обучением убедитесь, что для каждого намерения имеется минимум одно высказывание.

1. Откройте приложение, выбрав его имя на странице **Мои приложения**. 

2. В приложении выберите **Обучить** на панели сверху. 

3. После завершения обучения в верхней части браузера появится зеленая строка уведомлений.

<!-- The following note refers to what might cause the error message "Training failed: FewLabels for model: <ModelName>" -->

>[!NOTE]
>Если в приложении есть одно или несколько намерений, которые не содержат примеров фраз, такое приложение обучить нельзя. Добавьте фразы для всех намерений. Дополнительные сведения см. в статье о [добавлении примеров фраз](luis-how-to-add-example-utterances.md).

## <a name="training-date-and-time"></a>Дата и время обучения

Дата и время обучения — GMT + 2. 

## <a name="train-with-all-data"></a>Обучение на основе всех данных

При обучении используется небольшой процент отрицательных выборок. 

Если вы хотите использовать все данные вместо небольшой отрицательной выборки, используйте [API](#version-settings-api-use-of-usealltrainingdata).

<!--

 or the [LUIS portal setting](#luis-portal-setting-to-use-all-training-data)

### LUIS portal setting to use all training data

!!!IGNITE


-->

### <a name="version-settings-api-use-of-usealltrainingdata"></a>Параметры версии API использования Усеаллтраинингдата

Используйте [API параметров версии](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) , указав для параметра `UseAllTrainingData` значение true, чтобы отключить эту функцию. 

## <a name="unnecessary-training"></a>Необязательное обучение

Нет необходимости проводить обучение после каждого отдельного изменения. Обучение должно выполняться после группы изменений, применяемых к модели, а также когда следующим этапом будет тестирование или публикация. Если тестирование или публикация не требуются, проводить обучение необязательно. 

## <a name="training-with-the-rest-apis"></a>Обучение с помощью REST API

Обучение на портале LUIS выполняется в одно действие — нажатием кнопки **Обучить**. Обучение с помощью REST API — это процесс из двух этапов. Первый этап — [запрос обучения](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c45) с помощью HTTP POST. Второй этап — запрос [состояния обучения](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c46) с помощью HTTP GET. 

Чтобы узнать о завершении обучения, необходимо выполнять опрос состояния до тех пор, пока все модели не будут успешно обучены. 

## <a name="next-steps"></a>Дальнейшие действия

* я[Label suggested utterances with LUIS](luis-how-to-review-endpoint-utterances.md) (Пометка предлагаемых высказываний в LUIS) 
* [Use features to improve your LUIS app's performance](luis-how-to-add-features.md) (Использование функций для повышения производительности приложения LUIS) 
