---
title: Как проверить ход выполнения задания с помощью REST API | Документация Майкрософт
description: В этой статье показано, как проверить ход выполнения задания с помощью REST API служб мультимедиа Azure версии 2.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: a1a1f956-c035-448a-af9c-5ac15fcce9dd
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: ec72b7e7bf4c2b859e8ceecf617c90a4853cb517
ms.sourcegitcommit: 375b70d5f12fffbe7b6422512de445bad380fe1e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/06/2019
ms.locfileid: "74901502"
---
# <a name="how-to-check-job-progress"></a>Практическое руководство. Проверка хода выполнения задания

> [!NOTE]
> В Cлужбы мультимедиа версии 2 больше не добавляются новые компоненты или функциональные возможности. <br/>Ознакомьтесь с новейшей версией Служб мультимедиа — [версией 3](https://docs.microsoft.com/azure/media-services/latest/). См. также [руководство по миграции из v2 в версии 3](../latest/migrate-from-v2-to-v3.md) .

При выполнении заданий часто требуется способ отслеживания хода выполнения задачи. Состояние задания можно узнать с помощью свойства State задания. Дополнительную информацию о свойстве State см. в разделе [Свойства сущности задания](https://docs.microsoft.com/rest/api/media/operations/job#job_entity_properties).

## <a name="connect-to-media-services"></a>Подключение к службам мультимедиа

См. дополнительные сведения о [получении доступа к API служб мультимедиа Azure с помощью аутентификации Azure AD](media-services-use-aad-auth-to-access-ams-api.md). 

## <a name="check-job-progress"></a>Проверка хода выполнения задания

Запрос:

    GET https://media.windows.net/api/Jobs()?$filter=Id%20eq%20'nb%3Ajid%3AUUID%3Af3c43f94-327f-2347-90bb-3bf79f8559f1'&$top=1 HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.17
    Host: media.windows.net

Ответ:

    HTTP/1.1 200 OK
    Cache-Control: no-cache
    Content-Length: 450
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Server: Microsoft-IIS/8.5
    x-ms-client-request-id: d9b83c57-e26c-4d10-a20b-2be634c4b6a8
    request-id: 91d2be35-20ed-4e1c-a147-e82cd000c193
    x-ms-request-id: 91d2be35-20ed-4e1c-a147-e82cd000c193
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    Strict-Transport-Security: max-age=31536000; includeSubDomains

    {"odata.metadata":"https://media.windows.net/api/$metadata#Jobs","value":[{"Id":"nb:jid:UUID:f3c43f94-327f-2347-90bb-3bf79f8559f1","Name":"Encoding BigBuckBunny into to H264 Adaptive Bitrate MP4 Set 720p","Created":"2015-02-11T01:46:08.897","LastModified":"2015-02-11T01:46:08.897","EndTime":null,"Priority":0,"RunningDuration":0.0,"StartTime":"2015-02-11T01:46:16.58","State":2,"TemplateId":null,"JobNotificationSubscriptions":[]}]} 


## <a name="media-services-learning-paths"></a>Схемы обучения работе со службами мультимедиа
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Отправить отзыв
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Дополнительные материалы

[Обзор REST API операций служб мультимедиа](media-services-rest-how-to-use.md)
