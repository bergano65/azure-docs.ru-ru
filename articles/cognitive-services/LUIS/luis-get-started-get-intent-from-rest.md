---
title: Краткое руководство. Получение сведений о намерении с использованием API-интерфейсов REST в LUIS
description: Из этого краткого руководства по REST API вы узнаете, как с помощью общедоступного приложения LUIS определить намерение пользователя в разговоре.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 05/18/2020
ms.custom: devx-track-python, devx-track-js, devx-track-csharp
zone_pivot_groups: programming-languages-set-one
ms.openlocfilehash: d03ebc1ec5730f1905ce1ee5b9c484312f5aeee1
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/05/2020
ms.locfileid: "91316355"
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
