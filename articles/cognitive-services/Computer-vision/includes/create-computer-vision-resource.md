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
ms.openlocfilehash: cbf11c13bfb5c90739ea67fab92df08796a88e50
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/09/2019
ms.locfileid: "67711607"
---
## <a name="create-an-computer-vision-resource"></a>Создание компьютерного ресурса

1. Войдите на [портал Azure](https://portal.azure.com).
1. Нажмите кнопку [создать **компьютерного** ](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision) ресурсов
1. Введите все необходимые параметры:

    |Параметр|Значение|
    |--|--|
    |ИМЯ|Желаемое имя (2-64 символов)|
    |Подписка|Выберите соответствующую подписку|
    |Location|Выберите любое расположение рядом и доступность|
    |Ценовая категория|`F0` -Минимальный ценовой категории|
    |Группа ресурсов|Выберите группу доступных ресурсов|

1. Нажмите кнопку **создать** и ожидания ресурсов должен быть создан. После его создания, перейдите на страницу ресурсов
1. Сбор настроен `endpoint` и ключ API:

    |Вкладка ресурсов на портале|Параметр|Значение|
    |--|--|--|
    |**Обзор набора средств Visual Studio для Unity**|Конечная точка|Копирование конечной точки. Выглядит `https://computer-vision.cognitiveservices.azure.com/`|
    |**Ключи**|Ключ API|Скопируйте 1 из двух ключей. Это строка 32 буквенно-цифровых символов без пробелов и дефисов, `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`.|
