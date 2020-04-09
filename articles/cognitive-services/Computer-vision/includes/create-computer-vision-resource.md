---
title: Поддержка контейнеров
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 2647c604bebc2885f5c8516eb997792b65751e88
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80876102"
---
## <a name="create-an-computer-vision-resource"></a>Создание ресурса компьютерного зрения

1. Войти на [портал Azure](https://portal.azure.com).
1. Нажмите Создать ресурс [ **компьютерного зрения.** ](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision)
1. Введите все необходимые настройки:

    |Параметр|Значение|
    |--|--|
    |Имя|Требуемое имя (от 2 до 64 символов)|
    |Подписка|Выберите соответствующую подписку|
    |Местоположение|Выберите доступное поблизости расположение|
    |Ценовая категория|`F0` — минимальная ценовая категория|
    |Группа ресурсов|Выберите доступную группу ресурсов|

1. Щелкните **Создать** и дождитесь создания ресурса. После создания перейдите на страницу ресурсов.
1. Соберите `{ENDPOINT_URI}` настроенные и, `{API_KEY}`увидеть [сбор необходимых параметров](../computer-vision-how-to-install-containers.md#gathering-required-parameters) для деталей.
