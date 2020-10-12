---
title: Преобразование данных — LUIS
titleSuffix: Azure Cognitive Services
description: Узнайте, как изменить высказывания перед прогнозами в службе "Распознавание речи" (LUIS)
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/29/2019
ms.openlocfilehash: b305be693f59b65a62570f656a0132f4f03cf099
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91541804"
---
# <a name="convert-data-format-of-utterances"></a>Преобразование формата данных высказываний
LUIS предоставляет следующие преобразования пользователя utterance до прогноза "

* Преобразование речи в текст с помощью службы [Cognitive Services Speech](../Speech-Service/overview.md) .

## <a name="speech-to-text"></a>Преобразование речи в текст

Речь в тексте предоставляется как интеграция с LUIS.

### <a name="intent-conversion-concepts"></a>Основные понятия преобразования намерения
Преобразование речи в текст в LUIS позволяет отправить на конечную точку произнесенные высказывания и получить от LUIS ответ с прогнозом. Этот процесс выполняется за счет интеграции LUIS со службой [Речь](https://docs.microsoft.com/azure/cognitive-services/Speech). Дополнительные сведения о преобразовании речи в намерения см. в [руководстве](../speech-service/how-to-recognize-intents-from-speech-csharp.md).

### <a name="key-requirements"></a>Основные требования
Для этой интеграции нет необходимости создавать ключ **API распознавания речи Bing**. Здесь можно использовать ключ **службы "Распознавание речи"**, создаваемый на портале Azure. Не используйте начальный ключ LUIS.

### <a name="pricing-tier"></a>Ценовая категория
Для этой интеграции используется [модель ценообразования](luis-limits.md#key-limits), отличная от обычных ценовых категорий Распознавания речи.

### <a name="quota-usage"></a>Использование квоты
Информацию вы найдете в разделе об [ограничениях для ключей](luis-limits.md#key-limits).

## <a name="next-steps"></a>Дальнейшие шаги

> [!div class="nextstepaction"]
> [Извлечение данных](luis-concept-data-extraction.md)

