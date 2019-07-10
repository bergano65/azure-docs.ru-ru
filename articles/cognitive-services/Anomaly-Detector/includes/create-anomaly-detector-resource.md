---
title: Поддержка контейнеров
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 7/5/2019
ms.author: dapine
ms.openlocfilehash: b40f1833f08074cb0a8d45fe3afc6bac7cbac7f0
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/10/2019
ms.locfileid: "67717102"
---
## <a name="create-an-anomaly-detector-resource"></a>Создание ресурса обнаружения аномалий

1. Войдите на [портал Azure](https://portal.azure.com).
1. Нажмите кнопку [создать **обнаружения аномалий** ](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector) ресурсов
1. Введите все необходимые параметры:

    |Параметр|Значение|
    |--|--|
    |Имя|Желаемое имя (2-64 символов)|
    |Подписка|Выберите соответствующую подписку|
    |Местоположение|Выберите любое расположение рядом и доступность|
    |Ценовая категория|`F0` -Минимальный ценовой категории|
    |Группа ресурсов|Выберите группу доступных ресурсов|
    |Флажок подтверждения предварительной версии (обязательно)|Ли вы прочитали **предварительной версии** Обратите внимание,|

1. Нажмите кнопку **создать** и ожидания ресурсов должен быть создан. После его создания, перейдите на страницу ресурсов
1. Сбор настроен `endpoint` и ключ API:

    |Вкладка ресурсов на портале|Параметр|Значение|
    |--|--|--|
    |**Обзор**|Конечная точка|Копирование конечной точки. Выглядит `https://westus2.api.cognitive.microsoft.com/`|
    |**Ключи**|Ключ API|Скопируйте 1 из двух ключей. Это строка 32 буквенно-цифровых символов без пробелов и дефисов, `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`.|



