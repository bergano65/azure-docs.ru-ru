---
title: Краткое руководство. Получение сведений о намерении с использованием API-интерфейсов REST в LUIS
titleSuffix: Azure Cognitive Services
description: Из этого краткого руководства по REST API вы узнаете, как с помощью общедоступного приложения LUIS определить намерение пользователя в разговоре.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 10/17/2019
ms.author: diberry
zone_pivot_groups: programming-languages-set-one
ms.openlocfilehash: 3a8badb74bb8919876f3c0670d785f44fbcbb397
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73499707"
---
# <a name="quickstart-get-intent-with-rest-apis"></a>Краткое руководство. Получение сведений о намерении с использованием API-интерфейсов REST

Из этого краткого руководства вы узнаете, как использовать общедоступное приложение LUIS для определения намерений пользователя в разговоре. Вы отправите намерение пользователя в виде текста в конечную точку прогнозирования HTTP для общедоступного приложения. В конечной точке LUIS применяет модель общедоступного приложения, чтобы проанализировать смысл текста на естественном языке, определить общее намерение и извлечь данные, релевантные для предметной области приложения. 

В этом кратком руководстве используется REST API конечной точки. Дополнительные сведения см. в [документации по API конечной точки](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee78).

Для работы с этой статьей вам понадобится бесплатная учетная запись [LUIS](https://www.luis.ai). 

<a name="create-luis-subscription-key"></a>

::: zone pivot="programming-language-csharp"

[!INCLUDE [Get intent with C# and REST](./includes/get-started-get-intent-rest-csharp.md)]

::: zone-end

::: zone pivot="programming-language-java"

[!INCLUDE [Get intent with Java and REST](./includes/get-started-get-intent-rest-java.md)]

::: zone-end

::: zone pivot="programming-language-python"

[!INCLUDE [Get intent with Python and REST](./includes/get-started-get-intent-rest-python.md)]

::: zone-end

::: zone pivot="programming-language-nodejs"

[!INCLUDE [Get intent with Node.js and REST](./includes/get-started-get-intent-rest-nodejs.md)]

::: zone-end

::: zone pivot="programming-language-go"

[!INCLUDE [Get intent with Go and REST](./includes/get-started-get-intent-rest-go.md)]

::: zone-end
