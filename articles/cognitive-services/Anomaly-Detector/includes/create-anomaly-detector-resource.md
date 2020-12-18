---
title: Поддержка контейнеров
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/10/2020
ms.author: mbullwin
ms.openlocfilehash: 037b9746571678e24e80bd76e7e2ed173ab8eb90
ms.sourcegitcommit: d79513b2589a62c52bddd9c7bd0b4d6498805dbe
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/18/2020
ms.locfileid: "97677649"
---
## <a name="create-an-anomaly-detector-resource"></a>Создание ресурса "Детектор аномалий"

1. Войдите в <a href="https://portal.azure.com" target="_blank">портал Azure <span class="docon docon-navigate-external x-hidden-focus"></span> </a>.
1. Выберите <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector" target="_blank">создать ресурс детектора <span class="docon docon-navigate-external x-hidden-focus"></span> аномалий</a> .
1. Введите все необходимые параметры:

    |Параметр|Значение|
    |--|--|
    |Имя|Требуемое имя (от 2 до 64 символов)|
    |Подписка|Выберите соответствующую подписку|
    |Расположение|Выберите доступное поблизости расположение|
    |Ценовая категория|`F0` -10 вызовов в секунду, 20 000 транзакций в месяц. <br> Или сделайте так:<br> `S0` -80 вызовов в секунду|
    |Группа ресурсов|Выберите доступную группу ресурсов|

1. Щелкните **Создать** и дождитесь создания ресурса. После создания перейдите на страницу ресурсов.
1. Собираются настройки `endpoint` и ключ API:

    |Вкладка "ключи и конечные точки" на портале|Параметр|Значение|
    |--|--|--|
    |**Обзор**|Конечная точка|Скопируйте конечную точку. Он выглядит примерно так. ` https://<your-resource-name>.cognitiveservices.azure.com/`|
    |**Ключи**|Ключ API|Скопируйте 1 из двух ключей. Это строка, состоящая из 32 буквенно-цифровых символов без пробелов или дефисов `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` .|



