---
title: Создание входных данных задания Служб мультимедиа Azure из URL-адреса HTTPS | Документация Майкрософт
description: В этой статье показано, как создать входные данные задания из URL-адреса HTTP (HTTPS).
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
ms.openlocfilehash: f6eee912bb3bba112bd13969f1a8d9cb5748e387
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65413827"
---
# <a name="create-a-job-input-from-an-https-url"></a>Создание входных данных задания из URL-адреса HTTPS

При отправке заданий из Служб мультимедиа версии 3 необходимо указать расположение входного видео. Это можно сделать, указав URL-адрес HTTPS как входные данные задания (как показано в этом примере). Обратите внимание, что в настоящее время AMS версии 3 не поддерживает кодирование блочной передачи по URL-адресам HTTPS. Полный пример см. в этом [репозитории GitHub](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs).

> [!TIP]
> Перед началом разработки, просмотрите [разработка с использованием API-интерфейсы служб мультимедиа v3](media-services-apis-overview.md) (включает в себя сведения о доступе к API-интерфейсы, соглашения об именовании и т. д.)

## <a name="net-sample"></a>Пример кода .NET

В следующем коде показано, как создать задание с помощью входных данных URL-адреса HTTPS.

[!code-csharp[Main](../../../media-services-v3-dotnet-quickstarts/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs#SubmitJob)]

## <a name="job-error-codes"></a>Коды ошибок задания

См. статью о [кодах ошибок](https://docs.microsoft.com/rest/api/media/jobs/get#joberrorcode).

## <a name="next-steps"></a>Дальнейшие действия

[Создание входных данных задания из локального файла](job-input-from-local-file-how-to.md).
