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
ms.date: 10/27/2019
ms.author: juliako
ms.openlocfilehash: 30e8fa51df42d202d77ecdbc6a31fe3c7aaac6f4
ms.sourcegitcommit: b1c94635078a53eb558d0eb276a5faca1020f835
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/27/2019
ms.locfileid: "72968623"
---
# <a name="azure-media-services-legacy-components"></a>Устаревшие компоненты служб мультимедиа Azure

Со временем были внесены устойчивые улучшения и усовершенствования компонентов службы мультимедиа. В результате некоторые устаревшие компоненты были удалены. Инструкции по переносу приложения из устаревшего компонента можно найти в текущем компоненте в следующих статьях.

## <a name="retirement-plans-of-legacy-components-and-migration-guidance"></a>Планы выбытия устаревших компонентов и руководство по миграции

Мы сообщаем о прекращении использования *Windows Azure Media Encoder* (Ваме) и обработчиков мультимедиа *кодировщика мультимедиа Azure* (\ \ Media Encoder). Эти процессоры выпускают 30 ноября 2019.

* [Миграция из кодировщика мультимедиа Windows Azure в Media Encoder Standard](migrate-windows-azure-media-encoder.md)
* [Миграция из кодировщика мультимедиа Azure в Media Encoder Standard](migrate-azure-media-encoder.md)

Мы также сообщаем о прекращении использования следующих Аналитика мультимедиа обработчиков мультимедиа: 

|Имя обработчика мультимедиа|Дата вывода|Дополнительные замечания|
|---|---|
|[Azure Media Indexer 2](media-services-process-content-with-indexer2.md)| 1 января 2020 года|Этот обработчик мультимедиа будет заменен [индексатором видео служб мультимедиа Azure](https://docs.microsoft.com/azure/media-services/video-indexer/). Дополнительные сведения см. в статье [Миграция из Azure Media indexer 2 в индексатор видео служб мультимедиа Azure](migrate-indexer-v1-v2.md).|
|[Azure Media Indexer](media-services-index-content.md)|1 октября 2020 г.|Этот обработчик мультимедиа будет заменен [индексатором видео служб мультимедиа Azure](https://docs.microsoft.com/azure/media-services/video-indexer/). Дополнительные сведения см. в статье [Миграция с Azure Media indexer в индексатор видео служб мультимедиа Azure](migrate-indexer-v1-v2.md) .
|[Azure Media Face Detector](media-services-face-and-emotion-detection.md)|1 февраля 2020 г.|Этот обработчик Аналитика мультимедиа предварительной версии будет снят с учета и не будет перенесен в общедоступную версию. Мы будем оценивать свои сценарии и варианты использования с клиентами для будущих инвестиций.|
|[Azure Media Motion Detector](media-services-motion-detection.md)|1 февраля 2020 г.|Этот обработчик Аналитика мультимедиа предварительной версии будет снят с учета и не будет перенесен в общедоступную версию. Мы будем оценивать свои сценарии и варианты использования с клиентами для будущих инвестиций.|
|[РАСПОЗНАВАНИе мультимедиа Azure](media-services-video-optical-character-recognition.md)|1 февраля 2020 г.|Этот обработчик мультимедиа будет заменен [индексатором видео служб мультимедиа Azure](https://docs.microsoft.com/azure/media-services/video-indexer/) и [предустановленной мастером Video API служб мультимедиа версии 3](../latest/analyzing-video-audio-files-concept.md).|
|[Azure Media Video Thumbnails](media-services-video-summarization.md)|1 февраля 2020 г.|Этот обработчик мультимедиа будет заменен [индексатором видео служб мультимедиа Azure](https://docs.microsoft.com/azure/media-services/video-indexer/) и [предустановленной мастером Video API служб мультимедиа версии 3](../latest/analyzing-video-audio-files-concept.md).|

## <a name="next-steps"></a>Дальнейшие действия

[Руководство по миграции из версии 2 в версию 3 Служб мультимедиа](../latest/migrate-from-v2-to-v3.md)
