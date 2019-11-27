---
title: Миграция из кодировщика мультимедиа Azure в Media Encoder Standard | Документация Майкрософт
description: В этом разделе описано, как выполнить миграцию из кодировщика мультимедиа Azure в обработчик Media Encoder Standard мультимедиа.
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
ms.date: 08/21/2019
ms.author: juliako
ms.openlocfilehash: e9c83a25147696b5e492241a191b3104df001c7c
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/23/2019
ms.locfileid: "74424021"
---
# <a name="migrate-from-azure-media-encoder-to-media-encoder-standard"></a>Миграция из кодировщика мультимедиа Azure в Media Encoder Standard

В этой статье рассматриваются действия по переходу с устаревшего процессора мультимедиа кодировщика мультимедиа Azure (\ \), который выводится из эксплуатации с 1 марта 2020 на процессор Media Encoder Standard Media.  

При кодировании файлов с именем в клиентах обычно используется именованная строка предустановки, например `H264 Adaptive Bitrate MP4 Set 1080p`. Чтобы выполнить миграцию, необходимо обновить код для использования **Media Encoder Standardного** обработчика мультимедиа вместо «имя», а также одной из эквивалентных [системных предустановок](media-services-mes-presets-overview.md) , таких как `H264 Multiple Bitrate 1080p`. 

## <a name="migrating-to-media-encoder-standard"></a>Переход на Media Encoder Standard

Ниже приведен типичный C# пример кода, в котором используется устаревший обработчик мультимедиа. 

```csharp
// Declare a new job. 
IJob job = _context.Jobs.Create("AME Job"); 
// Get a media processor reference, and pass to it the name of the  
// processor to use for the specific task. 
IMediaProcessor processor = GetLatestMediaProcessorByName("Azure Media Encoder"); 

// Create a task with the encoding details, using a string preset. 
// In this case " H264 Adaptive Bitrate MP4 Set 1080p" preset is used. 
ITask task = job.Tasks.AddNew("My encoding task", 
    processor, 
    " H264 Adaptive Bitrate MP4 Set 1080p", 
    TaskOptions.None); 
```

Ниже приведена обновленная версия, использующая Media Encoder Standard.

```csharp
// Declare a new job. 
IJob job = _context.Jobs.Create("Media Encoder Standard Job"); 
// Get a media processor reference, and pass to it the name of the  
// processor to use for the specific task. 
IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard"); 

// Create a task with the encoding details, using a string preset. 
// In this case " H264 Multiple Bitrate 1080p" preset is used. 
ITask task = job.Tasks.AddNew("My encoding task", 
    processor, 
    "H264 Multiple Bitrate 1080p", 
    TaskOptions.None); 
```

### <a name="advanced-scenarios"></a>Сложные сценарии 

Если вы создали собственную предустановку кодирования для \ "имя \", используя схему, то [для Media Encoder Standard существует эквивалентная схема](media-services-mes-schema.md). Если у вас возникли вопросы по сопоставлению старых параметров с новым кодировщиком, обратитесь к нам через mailto:amshelp@microsoft.com  
## <a name="known-differences"></a>Известные различия 

Media Encoder Standard более надежна, надежна, имеет лучшую производительность и обеспечивает лучшую качество вывода, чем кодировщик устаревшей версии. Кроме того, сделайте следующее: 

* Media Encoder Standard создает выходные файлы с другим соглашением об именовании, чем имя в имени.
* Media Encoder Standard создает такие артефакты, как файлы, содержащие [метаданные входного файла](media-services-input-metadata-schema.md) и [метаданные выходных файлов](media-services-output-metadata-schema.md).

## <a name="next-steps"></a>Дополнительная информация

* [Устаревшие компоненты](legacy-components.md)
* [Страница цен](https://azure.microsoft.com/pricing/details/media-services/#encoding)
