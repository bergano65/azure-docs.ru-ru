---
title: Заметки о выпуске Live Video Analytics в IoT Edge. Azure
description: В этом разделе содержатся заметки о выпуске Live Video Analytics на IoT Edge выпусках, улучшениях, исправлениях ошибок и известных проблемах.
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: 28260728532d9db52b8d36488c2e456bd11803ea
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87091785"
---
# <a name="live-video-analytics-on-iot-edge-release-notes"></a>Заметки о выпуске Live Video Analytics в IoT Edge

>Получайте уведомления о том, когда следует повторно посетить эту страницу для обновлений, скопировав и вставив URL-адрес `https://docs.microsoft.com/api/search/rss?search=%22Live+Video+Analytics+on+IoT+Edge+release+notes%22&locale=en-us` в средство чтения RSS-канала.

В этой статье вы ознакомитесь:

* Последние выпуски.
* Известные проблемы
* Исправления ошибок
* Нерекомендуемые функции.

## <a name="july-13-2020"></a>13 июля 2020 г.

Этот тег выпуска для обновления за Июль 2020:

```
     mcr.microsoft.com/media/live-video-analytics:1.0.2
```

> [!NOTE]
> В кратком руководстве и учебниках в манифестах развертывания используется тег 1 (Live-Video-Analytics: 1). Поэтому простое повторное развертывание таких манифестов должно обновить модуль на пограничном > устройствах.

### <a name="new-features"></a>новые функции;
* Теперь можно создавать топологии графов, которые имеют узел приемника ресурса, а также узел приемника файлов, расположенный на узле процессора сигнального шлюза. См. пример в [этом](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/evr-motion-assets-files) разделе.

### <a name="bug-fixes"></a>Исправления ошибок
* Улучшения проверки требуемых свойств

## <a name="june-1-2020"></a>1 июня 2020 г.

Этот выпуск является первым общедоступным предварительным выпуском Live Video Analytics на IoT Edge. Тег выпуска

```
     mcr.microsoft.com/media/live-video-analytics:1.0.0
```

### <a name="supported-functionalities"></a>Поддерживаемые функциональные возможности
* Анализ потоков видео в реальном времени с помощью выбранных модулей AI и при необходимости запись видео на пограничном устройстве или в облаке
* Использование в операционных системах Linux AMD64, [поддерживаемых](../../iot-edge/support.md) IOT Edge
* Развертывание и Настройка модуля через центр Интернета вещей с помощью портал Azure или Visual Studio Code
* Управление [топологиями графов и экземплярами графов](media-graph-concept.md#media-graph-topologies-and-instances) удаленно или локально с помощью следующих прямых вызовов методов

    *   графтопологижет
    *   графтопологисет
    *   графтопологиделете
    *   графтопологилист
    *   графинстанцежет
    *   графинстанцесет
    *   графинстанцеделете
    *   графинстанцелист


## <a name="next-steps"></a>Дальнейшие действия

[Обзор](overview.md)
