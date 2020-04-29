---
title: Миграция из кодировщика мультимедиа Windows Azure в Media Encoder Standard | Документация Майкрософт
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
ms.date: 10/17/2019
ms.author: juliako
ms.openlocfilehash: e75e3f3eecf6c34050aeaa7fe387fffb0de58a74
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "76513207"
---
# <a name="migrate-from-windows-azure-media-encoder-to-media-encoder-standard"></a>Миграция из кодировщика мультимедиа Windows Azure в Media Encoder Standard

В этой статье рассматриваются действия по переходу с устаревшего обработчика мультимедиа Windows Azure Media Encoder (ВАМЕ) (который будет снят с учета) на процессор Media Encoder Standard Media. Сведения о датах выбытия см. в разделе об [устаревших компонентах](legacy-components.md) .

При кодировании файлов с помощью ВАМЕ в клиентах обычно используется именованная предустановленная строка, например `H264 Adaptive Bitrate MP4 Set 1080p`. Чтобы выполнить миграцию, необходимо обновить код для использования **Media Encoder Standardного** обработчика мультимедиа вместо Ваме и одной из эквивалентных [системных предустановок](media-services-mes-presets-overview.md) , таких как `H264 Multiple Bitrate 1080p`. 

## <a name="migrating-to-media-encoder-standard"></a>Переход на Media Encoder Standard

Ниже приведен типичный пример кода C#, использующий устаревший компонент. 

```csharp
// Declare a new job. 
IJob job = _context.Jobs.Create("WAME Job"); 
// Get a media processor reference, and pass to it the name of the  
// processor to use for the specific task. 
IMediaProcessor processor = GetLatestMediaProcessorByName("Windows Azure Media Encoder"); 

// Create a task with the encoding details, using a string preset. 
// In this case " H264 Adaptive Bitrate MP4 Set 1080p" preset is used. 
ITask task = job.Tasks.AddNew("My encoding task", 
    processor, 
    "H264 Adaptive Bitrate MP4 Set 1080p", 
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

Если вы создали собственную предустановку кодирования для ВАМЕ, используя схему, то [для Media Encoder Standard существует эквивалентная схема](media-services-mes-schema.md).

## <a name="known-differences"></a>Известные различия 

Media Encoder Standard является более надежным, надежным, имеет лучшую производительность и обеспечивает лучшую качество вывода, чем устаревший кодировщик ВАМЕ. В дополнение: 

* Media Encoder Standard создает выходные файлы с другим соглашением об именовании, чем ВАМЕ.
* Media Encoder Standard создает такие артефакты, как файлы, содержащие [метаданные входного файла](media-services-input-metadata-schema.md) и [метаданные выходных файлов](media-services-output-metadata-schema.md).
* Как описано на [странице цен](https://azure.microsoft.com/pricing/details/media-services/#encoding) (особенно в разделе часто задаваемых вопросов), при кодировании видео с помощью Media Encoder Standard вы получаете счет на основе продолжительности файлов, создаваемых в качестве выходных данных. При использовании ВАМЕ плата будет взиматься в зависимости от размеров входных видеофайлов и выходных видеофайлов.

## <a name="need-help"></a>Требуется помощь?

Вы можете отправить запрос в службу поддержки, перейдя к [новому запросу в службу поддержки](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) .

## <a name="next-steps"></a>Дальнейшие шаги

* [Устаревшие компоненты](legacy-components.md)
* [Страница цен](https://azure.microsoft.com/pricing/details/media-services/#encoding)
