---
title: Устаревшие компоненты мультимедиа-сервисов Azure Документы Майкрософт
description: В этой теме обсуждаются устаревшие компоненты медиаслужбы Azure.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2020
ms.author: juliako
ms.openlocfilehash: 94a70a1234d902787f248890f0cb538a4ba9c2f9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77921085"
---
# <a name="azure-media-services-legacy-components"></a>Устаревшие компоненты Мультимедиа Службы Azure

Со временем произошли устойчивые усовершенствования и усовершенствования компонентов медиа-сервиса. В результате некоторые устаревшие компоненты были удалены. Инструкции о переносе приложения из устаревшего компонента в текущий компонент можно найти в следующих статьях.
 
## <a name="retirement-plans-of-legacy-components-and-migration-guidance"></a>Пенсионные планы устаревших компонентов и миграционного руководства

Мы объявляем об амортизацию *медиа-процессоров Windows Azure Media Encoder* (WAME) и *мультимедиа-процессоров Azure Media Encoder* (AME). Эти процессоры выходят на пенсию 31 марта 2020 года.

* [Переход от Windows Azure Media Encoder к стандарту медиа-кодаре](migrate-windows-azure-media-encoder.md)
* [Переход от медиа-кодера Azure в стандарт медиа-кодаре](migrate-azure-media-encoder.md)

Мы также объявляем о выходе на пенсию следующих медиа-процессоров Media Analytics: 
 
|Имя обработчика мультимедиа|Дата вывода|Дополнительные замечания|
|---|---|
|[Azure Media Indexer 2](media-services-process-content-with-indexer2.md)|1 января 2020 г.|Этот медиапроцессор заменяется [индексом видеоиндекса медиасервисов Azure](https://docs.microsoft.com/azure/media-services/video-indexer/)Media. Для получения дополнительной информации [см.](migrate-indexer-v1-v2.md)|
|[Azure Media Indexer](media-services-index-content.md)|1 марта 2023 г.|Этот медиапроцессор заменяется [индексом видеоиндекса медиасервисов Azure](https://docs.microsoft.com/azure/media-services/video-indexer/)Media. Для получения дополнительной информации [см.](migrate-indexer-v1-v2.md)|
|[Обнаружение движения](media-services-motion-detection.md)|1 июня 2020 г.|Нет планов замены на данный момент.|
|[Видео резюме](media-services-video-summarization.md)|1 июня 2020 г.|Нет планов замены на данный момент.|
|[Распознавание оптического персонажа](media-services-video-optical-character-recognition.md)|1 июня 2020 г.|Этот медиапроцессор заменяется [индексом видеоиндекса медиасервисов Azure](https://docs.microsoft.com/azure/media-services/video-indexer/)Media. Кроме того, рассмотрите возможность использования [Azure Media Services v3 API](https://docs.microsoft.com/azure/media-services/latest/analyzing-video-audio-files-concept). <br/>[См. Сравнить Azure Медиа Услуги v3 пресетов и видео индексатор](https://docs.microsoft.com/azure/media-services/video-indexer/compare-video-indexer-with-media-services-presets)|
|[Обнаружение лиц](media-services-face-and-emotion-detection.md)|1 июня 2020 г.|Этот медиапроцессор заменяется [индексом видеоиндекса медиасервисов Azure](https://docs.microsoft.com/azure/media-services/video-indexer/)Media. Кроме того, рассмотрите возможность использования [Azure Media Services v3 API](https://docs.microsoft.com/azure/media-services/latest/analyzing-video-audio-files-concept). <br/>[См. Сравнить Azure Медиа Услуги v3 пресетов и видео индексатор](https://docs.microsoft.com/azure/media-services/video-indexer/compare-video-indexer-with-media-services-presets)|
|[Модератор контента](media-services-content-moderation.md)|1 июня 2020 г.|Этот медиапроцессор заменяется [индексом видеоиндекса медиасервисов Azure](https://docs.microsoft.com/azure/media-services/video-indexer/)Media. Кроме того, рассмотрите возможность использования [Azure Media Services v3 API](https://docs.microsoft.com/azure/media-services/latest/analyzing-video-audio-files-concept). <br/>[См. Сравнить Azure Медиа Услуги v3 пресетов и видео индексатор](https://docs.microsoft.com/azure/media-services/video-indexer/compare-video-indexer-with-media-services-presets)|

## <a name="next-steps"></a>Дальнейшие действия

[Руководство по миграции из версии 2 в версию 3 Служб мультимедиа](../latest/migrate-from-v2-to-v3.md)
