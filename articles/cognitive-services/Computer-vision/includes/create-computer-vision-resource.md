---
title: Поддержка контейнеров
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 10/07/2019
ms.author: dapine
ms.openlocfilehash: 2cb2cfbdfbac5d496f109d85977b41a050766ab0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "73499107"
---
## <a name="create-an-computer-vision-resource"></a>Создание ресурса компьютерного зрения

1. Войти на [портал Azure](https://portal.azure.com).
1. Нажмите Создать ресурс [ **компьютерного зрения.** ](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision)
1. Введите все необходимые настройки:

    |Параметр|Значение|
    |--|--|
    |name|Требуемое имя (от 2 до 64 символов)|
    |Подписка|Выберите соответствующую подписку|
    |Расположение|Выберите доступное поблизости расположение|
    |Ценовая категория|`F0` — минимальная ценовая категория|
    |Группа ресурсов|Выберите доступную группу ресурсов|

1. Щелкните **Создать** и дождитесь создания ресурса. После создания перейдите на страницу ресурсов.
1. Соберите `{ENDPOINT_URI}` настроенные и, `{API_KEY}`увидеть [сбор необходимых параметров](../computer-vision-how-to-install-containers.md#gathering-required-parameters) для деталей.
