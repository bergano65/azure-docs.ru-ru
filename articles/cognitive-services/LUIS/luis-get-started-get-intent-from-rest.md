---
title: Краткое руководство. Получение сведений о намерении с использованием API-интерфейсов REST в LUIS
description: Из этого краткого руководства по REST API вы узнаете, как с помощью общедоступного приложения LUIS определить намерение пользователя в разговоре.
ms.topic: quickstart
ms.date: 05/18/2020
ms.custom: devx-track-python, devx-track-javascript
zone_pivot_groups: programming-languages-set-one
ms.openlocfilehash: 2fd52011ed0d139e98740c8de077987edfae2c32
ms.sourcegitcommit: dea88d5e28bd4bbd55f5303d7d58785fad5a341d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/06/2020
ms.locfileid: "87873166"
---
# <a name="quickstart-get-intent-with-rest-apis"></a>Краткое руководство. Получение сведений о намерении с использованием API-интерфейсов REST

Из этого краткого руководства вы узнаете, как использовать приложение LUIS для определения намерений пользователя в разговоре. Вы отправите намерение пользователя в виде текста в конечную точку прогнозирования HTTP для приложения для заказа пиццы. В конечной точке LUIS применяет модель приложения для заказа пиццы, чтобы проанализировать смысл текста на естественном языке, определить общее намерение и извлечь данные, релевантные для предметной области приложения.

Для работы с этой статьей вам понадобится бесплатная учетная запись [LUIS](https://www.luis.ai).

<a name="create-luis-subscription-key"></a>

::: zone pivot="programming-language-csharp"
[!INCLUDE [Get intent with C# and REST](./includes/get-started-get-intent-rest-csharp.md)]
::: zone-end

::: zone pivot="programming-language-go"
[!INCLUDE [Get intent with Go and REST](./includes/get-started-get-intent-rest-go.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Get intent with Java and REST](./includes/get-started-get-intent-rest-java.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [Get intent with Node.js and REST](./includes/get-started-get-intent-rest-nodejs.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Get intent with Python and REST](./includes/get-started-get-intent-rest-python.md)]
::: zone-end
