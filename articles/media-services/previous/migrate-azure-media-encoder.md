---
title: Миграция из медиа-кодадера Azure в стандарт Media Encoder (ru) Документы Майкрософт
description: В этой теме обсуждается, как перейти от мультимедиа-кодареaz Azure media Encoder к медиапроцессору Media Encoder Standard.
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
ms.openlocfilehash: f8fe1b13db6473e80f0d7cdc638b775a0c8062c7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76513507"
---
# <a name="migrate-from-azure-media-encoder-to-media-encoder-standard"></a>Переход от медиа-кодера Azure в стандарт медиа-кодаре

В этой статье рассматриваются шаги для перехода от устаревшего медиапроцессора Azure Media Encoder (AME) (который в настоящее время отставает) к медиапроцессору Media Encoder Standard. Для даты выхода на [legacy components](legacy-components.md) пенсию см.

При кодировании файлов с Помощью AME клиенты `H264 Adaptive Bitrate MP4 Set 1080p`обычно используют именованную предустановленную строку, такую как. Для того, чтобы мигрировать, ваш код должен быть обновлен, чтобы использовать **медиа-процессор Encoder** `H264 Multiple Bitrate 1080p`Standard вместо AME, и один из эквивалентных [пресетов системы,](media-services-mes-presets-overview.md) как. 

## <a name="migrating-to-media-encoder-standard"></a>Миграция к медиа-стандарту

Вот типичный образец кода СЗ, который использует устаревший медиапроцессор. 

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

Вот обновленная версия, которая использует Media Encoder Standard.

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

Если вы создали свой собственный предустановленный кодирование для AME, используя свою схему, есть [эквивалентная схема для Media Encoder Standard.](media-services-mes-schema.md) Если у вас есть вопросы о том, как сопоставить старые настройки с новым кодером, пожалуйста, обратитесь к нам черезmailto:amshelp@microsoft.com  
## <a name="known-differences"></a>Известные различия 

Media Encoder Standard является более надежным, надежным, имеет более высокую производительность и обеспечивает более высокое качество продукции, чем устаревший кодер AME. Кроме того, примите во внимание: 

* Media Encoder Standard производит выходные файлы с другой конвенцией именования, чем AME.
* Media Encoder Standard производит артефакты, такие как файлы, содержащие [метаданные ввода и](media-services-input-metadata-schema.md) [метаданные вывода (s)](media-services-output-metadata-schema.md).

## <a name="next-steps"></a>Дальнейшие действия

* [Устаревшие компоненты](legacy-components.md)
* [Страница цен](https://azure.microsoft.com/pricing/details/media-services/#encoding)
