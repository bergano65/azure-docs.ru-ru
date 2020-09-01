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
ms.openlocfilehash: 1c3e50fd64c113aafd2d70cc76c7f789e080ad23
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/01/2020
ms.locfileid: "89269528"
---
# <a name="azure-media-services-legacy-components"></a>Устаревшие компоненты служб мультимедиа Azure

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

Со временем были внесены устойчивые улучшения и усовершенствования компонентов службы мультимедиа. В результате некоторые устаревшие компоненты были удалены. Инструкции по переносу приложения из устаревшего компонента можно найти в текущем компоненте в следующих статьях.
 
## <a name="retirement-plans-of-legacy-components-and-migration-guidance"></a>Планы выбытия устаревших компонентов и руководство по миграции

Мы сообщаем о прекращении использования обработчиков мультимедиа *Windows Azure Media Encoder* (ВАМЕ) и *кодировщика мультимедиа Azure* (AME). Эти процессоры будут прекращены 31 марта 2020 г.

* [Миграция из кодировщика мультимедиа Windows Azure в Media Encoder Standard](migrate-windows-azure-media-encoder.md)
* [Миграция из кодировщика мультимедиа Azure в Media Encoder Standard](migrate-azure-media-encoder.md)

Мы также сообщаем о прекращении использования следующих Аналитика мультимедиа обработчиков мультимедиа: 
 
|Имя обработчика мультимедиа|Дата вывода|Дополнительные сведения|
|---|---|
|[Azure Media Indexer 2](media-services-process-content-with-indexer2.md)|1 января 2020 г.|Этот обработчик мультимедиа заменяется [индексатором видео служб мультимедиа Azure](../video-indexer/index.yml). Дополнительные сведения см. в статье [Миграция из Azure Media indexer 2 в индексатор видео служб мультимедиа Azure](migrate-indexer-v1-v2.md).|
|[Azure Media Indexer](media-services-index-content.md)|1 марта 2023 г.|Этот обработчик мультимедиа заменяется [индексатором видео служб мультимедиа Azure](../video-indexer/index.yml). Дополнительные сведения см. в статье [Миграция с Azure Media indexer в индексатор видео служб мультимедиа Azure](migrate-indexer-v1-v2.md) .|
|[Обнаружение движения](media-services-motion-detection.md)|1 июня 2020 г.|В настоящее время нет планов замены.|
|[Формирование сводных данных видео](media-services-video-summarization.md)|1 июня 2020 г.|В настоящее время нет планов замены.|
|[Оптическое распознавание символов видео](media-services-video-optical-character-recognition.md)|1 июня 2020 г.|Этот обработчик мультимедиа заменяется [индексатором видео служб мультимедиа Azure](../video-indexer/index.yml). Кроме того, рассмотрите возможность использования [API служб мультимедиа Azure v3](../latest/analyzing-video-audio-files-concept.md). <br/>См. статью [Сравнение установок и индексатора видео служб мультимедиа Azure v3](../video-indexer/compare-video-indexer-with-media-services-presets.md) .|
|[Обнаружение лиц](media-services-face-and-emotion-detection.md)|1 июня 2020 г.|Этот обработчик мультимедиа заменяется [индексатором видео служб мультимедиа Azure](../video-indexer/index.yml). Кроме того, рассмотрите возможность использования [API служб мультимедиа Azure v3](../latest/analyzing-video-audio-files-concept.md). <br/>См. статью [Сравнение установок и индексатора видео служб мультимедиа Azure v3](../video-indexer/compare-video-indexer-with-media-services-presets.md) .|
|[Content Moderator](media-services-content-moderation.md)|1 июня 2020 г.|Этот обработчик мультимедиа заменяется [индексатором видео служб мультимедиа Azure](../video-indexer/index.yml). Кроме того, рассмотрите возможность использования [API служб мультимедиа Azure v3](../latest/analyzing-video-audio-files-concept.md). <br/>См. статью [Сравнение установок и индексатора видео служб мультимедиа Azure v3](../video-indexer/compare-video-indexer-with-media-services-presets.md) .|

## <a name="next-steps"></a>Дальнейшие действия

[Руководство по миграции из версии 2 в версию 3 Служб мультимедиа](../latest/migrate-from-v2-to-v3.md)
