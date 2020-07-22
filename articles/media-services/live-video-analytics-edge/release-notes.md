---
title: Заметки о выпуске Live Video Analytics в IoT Edge. Azure
description: В этом разделе содержатся заметки о выпуске Live Video Analytics на IoT Edge выпусках, улучшениях, исправлениях ошибок и известных проблемах.
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: fd607ee027b46cf386180338440e6530da911302
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84260327"
---
# <a name="live-video-analytics-on-iot-edge-release-notes"></a>Заметки о выпуске Live Video Analytics в IoT Edge

>Получайте уведомления о том, когда следует повторно посетить эту страницу для обновлений, скопировав и вставив URL-адрес `https://docs.microsoft.com/api/search/rss?search=%22Live+Video+Analytics+on+IoT+Edge+release+notes%22&locale=en-us` в средство чтения RSS-канала.

В этой статье вы ознакомитесь:

* Последние выпуски.
* Известные проблемы
* Исправления ошибок
* Нерекомендуемые функции.

## <a name="june-1-2020"></a>1 июня 2020 г.

Этот выпуск является первым общедоступным предварительным выпуском Live Video Analytics на IoT Edge. Тег выпуска

```
     mcr.microsoft.com/media/live-video-analytics:1.0.0
```

### <a name="supported-functionalities"></a>Поддерживаемые функциональные возможности
* Анализ потоков видео в реальном времени с помощью выбранных модулей AI и при необходимости запись видео на пограничном устройстве или в облаке
* Использование в операционных системах Linux AMD64, [поддерживаемых](https://docs.microsoft.com/azure/iot-edge/support) IOT Edge
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
