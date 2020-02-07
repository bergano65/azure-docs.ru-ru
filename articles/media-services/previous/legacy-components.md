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
ms.date: 11/22/2019
ms.author: juliako
ms.openlocfilehash: bbf3ba17e7f91e88136f2dac50c908262100e77a
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/06/2020
ms.locfileid: "77048778"
---
# <a name="azure-media-services-legacy-components"></a>Устаревшие компоненты служб мультимедиа Azure

Со временем были внесены устойчивые улучшения и усовершенствования компонентов службы мультимедиа. В результате некоторые устаревшие компоненты были удалены. Инструкции по переносу приложения из устаревшего компонента можно найти в текущем компоненте в следующих статьях.
 
## <a name="retirement-plans-of-legacy-components-and-migration-guidance"></a>Планы выбытия устаревших компонентов и руководство по миграции

Мы сообщаем о прекращении использования *Windows Azure Media Encoder* (Ваме) и обработчиков мультимедиа *кодировщика мультимедиа Azure* (\ \ Media Encoder). Эти процессоры будут прекращены 31 марта 2020 г.

* [Миграция из кодировщика мультимедиа Windows Azure в Media Encoder Standard](migrate-windows-azure-media-encoder.md)
* [Миграция из кодировщика мультимедиа Azure в Media Encoder Standard](migrate-azure-media-encoder.md)

Мы также сообщаем о прекращении использования следующих Аналитика мультимедиа обработчиков мультимедиа: 
 
|Имя обработчика мультимедиа|Дата вывода|Дополнительные замечания|
|---|---|
|[Azure Media Indexer 2](media-services-process-content-with-indexer2.md)| 1 октября 2020 г.|Этот обработчик мультимедиа будет заменен [индексатором видео служб мультимедиа Azure](https://docs.microsoft.com/azure/media-services/video-indexer/). Дополнительные сведения см. в статье [Миграция из Azure Media indexer 2 в индексатор видео служб мультимедиа Azure](migrate-indexer-v1-v2.md).|
|[Azure Media Indexer](media-services-index-content.md)|1 октября 2020 г.|Этот обработчик мультимедиа будет заменен [индексатором видео служб мультимедиа Azure](https://docs.microsoft.com/azure/media-services/video-indexer/). Дополнительные сведения см. в статье [Миграция с Azure Media indexer в индексатор видео служб мультимедиа Azure](migrate-indexer-v1-v2.md) .

## <a name="next-steps"></a>Дальнейшие действия

[Руководство по миграции из версии 2 в версию 3 Служб мультимедиа](../latest/migrate-from-v2-to-v3.md)
