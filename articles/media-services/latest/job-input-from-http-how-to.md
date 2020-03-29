---
title: Создание входных данных задания Служб мультимедиа Azure из URL-адреса HTTPS | Документация Майкрософт
description: В этой теме показано, как создать ввод вакансии служб ы мультимедиа Azure из URL HTTPS.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 02/13/2019
ms.author: juliako
ms.openlocfilehash: a3d4568dd237491f28ae2880bdd78dd236870c3f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74899829"
---
# <a name="create-a-job-input-from-an-https-url"></a>Создание входных данных задания из URL-адреса HTTPS

При отправке заданий из Служб мультимедиа версии 3 необходимо указать расположение входного видео. Это можно сделать, указав URL-адрес HTTPS как входные данные задания (как показано в этом примере). Обратите внимание, что в настоящее время AMS версии 3 не поддерживает кодирование блочной передачи по URL-адресам HTTPS. Полный пример см. в этом [репозитории GitHub](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs).

> [!TIP]
> Прежде чем приступить к разработке, просмотрите [разработку с помощью AIS Media Services v3](media-services-apis-overview.md) (включает информацию о доступе к AIS, именования конвенций и т.д.)

## <a name="net-sample"></a>Пример кода .NET

В следующем коде показано, как создать задание с помощью входных данных URL-адреса HTTPS.

[!code-csharp[Main](../../../media-services-v3-dotnet-quickstarts/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs#SubmitJob)]

## <a name="job-error-codes"></a>Коды ошибок задания

См. статью о [кодах ошибок](https://docs.microsoft.com/rest/api/media/jobs/get#joberrorcode).

## <a name="next-steps"></a>Дальнейшие действия

[Создание ввода задания из локального файла.](job-input-from-local-file-how-to.md)
