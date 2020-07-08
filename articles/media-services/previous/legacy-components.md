---
title: Устаревшие компоненты служб мультимедиа Azure | Документация Майкрософт
description: В этом разделе обсуждаются устаревшие компоненты служб мультимедиа Azure.
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "77921085"
---
# <a name="azure-media-services-legacy-components"></a>Устаревшие компоненты служб мультимедиа Azure

Со временем были внесены устойчивые улучшения и усовершенствования компонентов службы мультимедиа. В результате некоторые устаревшие компоненты были удалены. Инструкции по переносу приложения из устаревшего компонента можно найти в текущем компоненте в следующих статьях.
 
## <a name="retirement-plans-of-legacy-components-and-migration-guidance"></a>Планы выбытия устаревших компонентов и руководство по миграции

Мы сообщаем о прекращении использования обработчиков мультимедиа *Windows Azure Media Encoder* (ВАМЕ) и *кодировщика мультимедиа Azure* (AME). Эти процессоры будут прекращены 31 марта 2020 г.

* [Миграция из кодировщика мультимедиа Windows Azure в Media Encoder Standard](migrate-windows-azure-media-encoder.md)
* [Миграция из кодировщика мультимедиа Azure в Media Encoder Standard](migrate-azure-media-encoder.md)

Мы также сообщаем о прекращении использования следующих Аналитика мультимедиа обработчиков мультимедиа: 
 
|Имя обработчика мультимедиа|Дата вывода|Дополнительные замечания|
|---|---|
|[Azure Media Indexer 2](media-services-process-content-with-indexer2.md)|1 января 2020 г.|Этот обработчик мультимедиа заменяется [индексатором видео служб мультимедиа Azure](https://docs.microsoft.com/azure/media-services/video-indexer/). Дополнительные сведения см. в статье [Миграция из Azure Media indexer 2 в индексатор видео служб мультимедиа Azure](migrate-indexer-v1-v2.md).|
|[Azure Media Indexer](media-services-index-content.md)|1 марта 2023 г.|Этот обработчик мультимедиа заменяется [индексатором видео служб мультимедиа Azure](https://docs.microsoft.com/azure/media-services/video-indexer/). Дополнительные сведения см. в статье [Миграция с Azure Media indexer в индексатор видео служб мультимедиа Azure](migrate-indexer-v1-v2.md) .|
|[Обнаружение движения](media-services-motion-detection.md)|1 июня 2020 г.|В настоящее время нет планов замены.|
|[Формирование сводных данных видео](media-services-video-summarization.md)|1 июня 2020 г.|В настоящее время нет планов замены.|
|[Оптическое распознавание символов видео](media-services-video-optical-character-recognition.md)|1 июня 2020 г.|Этот обработчик мультимедиа заменяется [индексатором видео служб мультимедиа Azure](https://docs.microsoft.com/azure/media-services/video-indexer/). Кроме того, рассмотрите возможность использования [API служб мультимедиа Azure v3](https://docs.microsoft.com/azure/media-services/latest/analyzing-video-audio-files-concept). <br/>См. статью [Сравнение установок и индексатора видео служб мультимедиа Azure v3](https://docs.microsoft.com/azure/media-services/video-indexer/compare-video-indexer-with-media-services-presets) .|
|[Обнаружение лиц](media-services-face-and-emotion-detection.md)|1 июня 2020 г.|Этот обработчик мультимедиа заменяется [индексатором видео служб мультимедиа Azure](https://docs.microsoft.com/azure/media-services/video-indexer/). Кроме того, рассмотрите возможность использования [API служб мультимедиа Azure v3](https://docs.microsoft.com/azure/media-services/latest/analyzing-video-audio-files-concept). <br/>См. статью [Сравнение установок и индексатора видео служб мультимедиа Azure v3](https://docs.microsoft.com/azure/media-services/video-indexer/compare-video-indexer-with-media-services-presets) .|
|[Content Moderator](media-services-content-moderation.md)|1 июня 2020 г.|Этот обработчик мультимедиа заменяется [индексатором видео служб мультимедиа Azure](https://docs.microsoft.com/azure/media-services/video-indexer/). Кроме того, рассмотрите возможность использования [API служб мультимедиа Azure v3](https://docs.microsoft.com/azure/media-services/latest/analyzing-video-audio-files-concept). <br/>См. статью [Сравнение установок и индексатора видео служб мультимедиа Azure v3](https://docs.microsoft.com/azure/media-services/video-indexer/compare-video-indexer-with-media-services-presets) .|

## <a name="next-steps"></a>Дальнейшие шаги

[Руководство по миграции из версии 2 в версию 3 Служб мультимедиа](../latest/migrate-from-v2-to-v3.md)
