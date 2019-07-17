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
ms.openlocfilehash: 05284d434e6bd22fd50957f7cc5ec966f88a4fd4
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/15/2019
ms.locfileid: "68229301"
---
## <a name="create-an-azure-container-instance-resource"></a>Создайте ресурс экземпляра контейнера Azure

1. Перейдите к [создать](https://ms.portal.azure.com/#create/Microsoft.ContainerInstances) страницы для экземпляров контейнеров.

2. На **основы** введите следующие сведения:

    |Параметр|Значение|
    |--|--|
    |Subscription|Выберите свою подписку.|
    |Resource group|Выберите группу доступных ресурсов или создайте новую, такие как `cognitive-services`.|
    |Имя контейнера|Введите имя, такое как `cognitive-container-instance`. Имя должно быть в нижнем caps.|
    |Местоположение|Выберите регион для развертывания.|
    |Тип образа|`Public`|
    |Имя образа|Укажите расположение контейнера Cognitive Services. Расположение может быть такой же в `docker pull` команды, обратитесь к [репозиториев контейнера и образов](../../cognitive-services-container-support.md#container-repositories-and-images) имена доступных образов и их соответствующие репозитории.|
    |Тип ОС|`Linux`|
    |Size|Измените размер Предлагаемые рекомендации для конкретного контейнера Cognitive Service:<br>2 ядра ЦП<br>4 ГБ

3. На **сети** введите следующие сведения:

    |Параметр|Значение|
    |--|--|
    |порты;|Значение TCP-порт `5000`. Предоставляет контейнер с портом 5000.|

4. На **Дополнительно** введите необходимые **переменные среды** для контейнера выставления счетов параметры ACI ресурса:

    | Ключ | Значение |
    |--|--|
    |`apikey`|Копируются из **ключи** страницу ресурса. Это строка 32 буквенно-цифровых символов без пробелов и дефисов, `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`.|
    |`billing`|Копируются из **Обзор** страницу ресурса.|
    |`eula`|`accept`|

1. Нажмите кнопку **проверка и создание**
1. После успешной проверке, нажмите кнопку **создать** чтобы завершить процесс создания
1. Если ресурс успешно развернут, он готов