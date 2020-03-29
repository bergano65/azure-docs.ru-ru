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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "73523713"
---
## <a name="create-an-anomaly-detector-resource"></a>Создание ресурса "Детектор аномалий"

1. Войти на [портал Azure](https://portal.azure.com)
1. Нажмите Создать ресурс [ **детектор аномалий** ](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector)
1. Введите все необходимые настройки:

    |Параметр|Значение|
    |--|--|
    |name|Требуемое имя (от 2 до 64 символов)|
    |Подписка|Выберите соответствующую подписку|
    |Расположение|Выберите доступное поблизости расположение|
    |Ценовая категория|`F0` — минимальная ценовая категория|
    |Группа ресурсов|Выберите доступную группу ресурсов|
    |Проверка подтверждения предварительного просмотра (требуется)|Читали ли вы уведомление о **предварительном просмотре** или нет|

1. Щелкните **Создать** и дождитесь создания ресурса. После создания перейдите на страницу ресурса
1. Соберите `endpoint` настроенный и ключ API:

    |Вкладка ресурсов в портале|Параметр|Значение|
    |--|--|--|
    |**Обзор**|Конечная точка|Копируйте конечную точку. Это похоже на`https://westus2.api.cognitive.microsoft.com/`|
    |**Ключи**|Ключ API|Копируй 1 из двух ключей. Это 32 алфавитного символа строки без каких-либо пробелов или тире, `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`.|



