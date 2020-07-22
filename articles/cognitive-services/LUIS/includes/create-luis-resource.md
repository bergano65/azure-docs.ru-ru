---
title: Создание ресурсов LUIS
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/07/2020
ms.author: aahi
ms.openlocfilehash: ebfe7e80577b163218a7bc501fa4e3e9b206fd62
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80879222"
---
<a name="create-luis-resources"></a>

## <a name="create-luis-resources-in-azure-portal"></a>Создание ресурсов LUIS на портале Azure

1. Используйте [эту ссылку](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne), чтобы начать создание ресурсов LUIS на портале Azure.

1. Введите все необходимые параметры:

    |Имя|Назначение|
    |--|--|
    |имя подписки;| Подписка, для которой будет выставлен счет за ресурс.|
    |Группа ресурсов| Выбранное или созданное пользователем имя группы ресурсов. Группы ресурсов позволяют группировать ресурсы Azure для осуществления доступа и управления.|
    |Имя| Выбранное пользователем имя, используемое в качестве пользовательского поддомена для запросов конечной точки разработки и прогнозирования.|
    |Место разработки|Регион, связанный с вашей моделью.|
    |Ценовая категория для разработки|Ценовая категория определяет максимальное количество транзакций в секунду за месяц.|
    |Расположение среды выполнения|Регион, связанный с опубликованной средой выполнения конечной точки прогнозирования.|
    |Ценовая категория среды выполнения|Ценовая категория определяет максимальное количество транзакций в секунду за месяц.|

    > [!div class="mx-imgBorder"]
    > [![Создание ресурса для распознавания речи](../media/luis-how-to-azure-subscription/create-resource-in-azure-small.png)](../media/luis-how-to-azure-subscription/create-resource-in-azure-small.png#lightbox)

1. Щелкните **Просмотр и создание** и дождитесь создания ресурса.
1. После создания обоих ресурсов выберите на портале Azure новый ресурс для разработки, а затем щелкните **Быстрое начало**, чтобы получить **URL-адрес конечной точки** разработки и **ключ** для разработки программным способом.

> [!TIP]
> Чтобы использовать ресурсы, [назначьте их](../luis-how-to-azure-subscription.md#assign-an-authoring-resource-in-the-luis-portal-for-all-apps) на портале LUIS.