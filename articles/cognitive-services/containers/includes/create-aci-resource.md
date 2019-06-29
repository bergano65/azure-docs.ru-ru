---
title: Поддержка контейнеров
titleSuffix: Azure Cognitive Services
description: Узнайте, как создать ресурс экземпляра (ACI) контейнеров azure.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: b24a78873c3220b969fc548d2553e465e80773cd
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67455074"
---
## <a name="create-an-azure-container-instance-aci-resource"></a>Создайте ресурс экземпляра контейнера Azure (ACI)

1. Перейдите к [создать](https://ms.portal.azure.com/#create/Microsoft.ContainerInstances) страницы для экземпляров контейнеров.

2. На **основы** введите следующие сведения:

    |Параметр|Значение|
    |--|--|
    |Подписка|Выберите свою подписку.|
    |Группа ресурсов|Выберите группу доступных ресурсов или создайте новую, такие как `cognitive-services`.|
    |Имя контейнера|Введите имя, такое как `cognitive-container-instance`. Это имя должно быть в нижнем caps.|
    |Location|Выберите регион для развертывания.|
    |Тип образа|`Public`|
    |Имя образа|Укажите расположение контейнера Cognitive Services. Это может быть расположение, которое использовалось в `docker pull` команде _например_: <br>`mcr.microsoft.com/azure-cognitive-services/sentiment`|
    |Тип ОС|`Linux`|
    |Size|Измените размер на предлагаемые рекомендации для конкретного контейнера Cognitive Service.:<br>2 ядра<br>4 ГБ

3. На **сети** введите следующие сведения:

    |Параметр|Значение|
    |--|--|
    |порты;|Значение TCP-порт `5000`. Предоставляет контейнер с портом 5000.|

4. На **Дополнительно** введите следующие сведения для передачи через контейнер [обязательные параметры выставления счетов](https://docs.microsoft.com/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-install-containers#billing-arguments) ACI ресурс:

    |Страница "Дополнительно" ключа|Страница "Дополнительно" значение|
    |--|--|
    |`apikey`|Копируются из **ключи** страницу ресурса анализа текста. Это строка 32 буквенно-цифровых символов без пробелов и дефисов, `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`.|
    |`billing`|Копируются из **Обзор** страницу ресурса анализа текста. Пример: `https://northeurope.api.cognitive.microsoft.com/text/analytics/v2.0`|
    |`eula`|`accept`|

1. Нажмите кнопку **проверка и создание**
1. После успешной проверке, нажмите кнопку **создать** чтобы завершить процесс создания
1. Если ресурс успешно развернут, он будет готов к использованию