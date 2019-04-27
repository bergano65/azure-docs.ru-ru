---
title: Преобразование данных
titleSuffix: Language Understanding - Azure Cognitive Services
description: Узнайте, как изменить высказывания перед прогнозами в службе "Распознавание речи" (LUIS)
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 01/16/2019
ms.author: diberry
ms.openlocfilehash: bdf2d87e558726937fa4221a0d95179216c66051
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60813814"
---
# <a name="convert-data-format-of-utterances"></a>Преобразование формата данных высказываний
LUIS использует службу Cognitive Services Speech, чтобы преобразовать высказывания из произнесенной речи в текст перед получением прогноза. 

## <a name="speech-to-intent-conversion-concepts"></a>Принципы преобразования речи в намерения
Преобразование речи в текст в LUIS позволяет отправить на конечную точку произнесенные высказывания и получить от LUIS ответ с прогнозом. Этот процесс выполняется за счет интеграции LUIS со службой [Речь](https://docs.microsoft.com/azure/cognitive-services/Speech). Дополнительные сведения о преобразовании речи в намерения см. в [руководстве](../speech-service/how-to-recognize-intents-from-speech-csharp.md).

### <a name="key-requirements"></a>Основные требования
Для этой интеграции нет необходимости создавать ключ **API распознавания речи Bing**. Здесь можно использовать ключ **службы "Распознавание речи"**, создаваемый на портале Azure. Не используйте начальный ключ LUIS.

### <a name="pricing-tier"></a>Ценовая категория
Для этой интеграции используется [модель ценообразования](luis-boundaries.md#key-limits), отличная от обычных ценовых категорий Language Understanding. 

### <a name="quota-usage"></a>Использование квоты
Информацию вы найдете в разделе об [ограничениях для ключей](luis-boundaries.md#key-limits). 

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Использование преобразования речи в текст](luis-tutorial-speech-to-intent.md)

