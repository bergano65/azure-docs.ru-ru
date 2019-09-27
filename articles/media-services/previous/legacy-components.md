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
ms.date: 09/26/2019
ms.author: juliako
ms.openlocfilehash: e4ff157f58a68c68f8610c6c473f5d69897650ad
ms.sourcegitcommit: e1b6a40a9c9341b33df384aa607ae359e4ab0f53
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/27/2019
ms.locfileid: "71338413"
---
# <a name="azure-media-services-legacy-components"></a>Устаревшие компоненты служб мультимедиа Azure

Со временем были внесены устойчивые улучшения и усовершенствования компонентов службы мультимедиа. В результате некоторые устаревшие компоненты были удалены. Инструкции по переносу приложения из устаревшего компонента можно найти в текущем компоненте в следующих статьях.

## <a name="legacy-components-and-migration-guidance"></a>Устаревшие компоненты и руководство по миграции

Мы сообщаем о прекращении использования *Windows Azure Media Encoder* (Ваме) и обработчиков мультимедиа *кодировщика мультимедиа Azure* (\ \ Media Encoder). Эти процессоры выпускают 30 ноября 2019.

* [Миграция из кодировщика мультимедиа Windows Azure в Media Encoder Standard](migrate-windows-azure-media-encoder.md)
* [Миграция из кодировщика мультимедиа Azure в Media Encoder Standard](migrate-azure-media-encoder.md)

Мы также сообщаем о прекращении использования предварительной версии *Azure Media indexer v1* и *Azure Media indexer v2*. Процессор мультимедиа [Azure Media indexer v1](media-services-index-content.md) будет снят с 1 октября по 2020. [Процессоры мультимедиа[предварительной версии Azure Media indexer v2](media-services-process-content-with-indexer2.md) будут прекращены 1 января 2019.  [Индексатор видео служб мультимедиа Azure](https://docs.microsoft.com/azure/media-services/video-indexer/) заменяет эти устаревшие обработчики мультимедиа.

* [Выполните миграцию из Azure Media indexer v1 и Azure Media indexer v2 в индексатор видео служб мультимедиа Azure](migrate-indexer-v1-v2.md).

## <a name="next-steps"></a>Следующие шаги

[Руководство по миграции из версии 2 в версию 3 Служб мультимедиа](../latest/migrate-from-v2-to-v3.md)
