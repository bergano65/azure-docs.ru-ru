---
title: Поддержка контейнеров
titleSuffix: Azure Cognitive Services
description: Узнайте, как создать ресурс экземпляра контейнера Azure.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 7/3/2019
ms.author: dapine
ms.openlocfilehash: 38addf4651373ba0f4df411325218a255c835508
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/09/2019
ms.locfileid: "67711409"
---
## <a name="create-an-azure-container-instance-resource"></a>Создайте ресурс экземпляра контейнера Azure

1. Перейдите к [создать](https://ms.portal.azure.com/#create/Microsoft.ContainerInstances) страницы для экземпляров контейнеров.

2. На **основы** введите следующие сведения:

    |Параметр|Значение|
    |--|--|
    |Подписка|Выберите свою подписку.|
    |Группа ресурсов|Выберите группу доступных ресурсов или создайте новую, такие как `cognitive-services`.|
    |Имя контейнера|Введите имя, такое как `cognitive-container-instance`. Имя должно быть в нижнем caps.|
    |Location|Выберите регион для развертывания.|
    |Тип образа|`Public`|
    |Имя образа|Укажите расположение контейнера Cognitive Services. Расположение может быть такой же в `docker pull` команды, обратитесь к [репозиториев контейнера и образов](../../cognitive-services-container-support.md#container-repositories-and-images) имена доступных образов и их соответствующие репозитории.|
    |Тип ОС|`Linux`|
    |Size|Измените размер Предлагаемые рекомендации для конкретного контейнера Cognitive Service:<br>2 ядра ЦП<br>4 ГБ

3. На **сети** введите следующие сведения:

    |Параметр|Значение|
    |--|--|
    |порты;|Значение TCP-порт `5000`. Предоставляет контейнер с портом 5000.|

4. На **Дополнительно** введите необходимые **переменные среды** для контейнера [выставления счетов параметры](https://docs.microsoft.com/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-install-containers#billing-arguments) ACI ресурса:

    | Ключ | Значение |
    |--|--|
    |`apikey`|Копируются из **ключи** страницу ресурса анализа текста. Это строка 32 буквенно-цифровых символов без пробелов и дефисов, `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`.|
    |`billing`|Копируются из **Обзор** страницу ресурса анализа текста. Пример: `https://northeurope.api.cognitive.microsoft.com/text/analytics/v2.0`|
    |`eula`|`accept`|

1. Нажмите кнопку **проверка и создание**
1. После успешной проверке, нажмите кнопку **создать** чтобы завершить процесс создания
1. Если ресурс успешно развернут, он готов