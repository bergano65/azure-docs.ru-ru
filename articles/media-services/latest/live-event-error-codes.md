---
title: Коды ошибок событий в живых службах мультимедиа Azure (ru) Документы Майкрософт
description: В этой статье перечислены живые коды ошибок событий.
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/25/2020
ms.author: juliako
ms.openlocfilehash: f9067562f67190b8bc04392f33078d4d3262f986
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77654585"
---
# <a name="media-services-live-event-error-codes"></a>Коды ошибок media Services Live Event

В таблицах в этом разделе перечисляются коды ошибок [Live Event.](live-events-outputs-concept.md)

## <a name="liveeventconnectionrejected"></a>LiveEventConnectionRejected;

Когда вы подписываетесь на события [Event Grid](https://docs.microsoft.com/azure/event-grid/) для живого события, вы можете увидеть одну из следующих ошибок из события [LiveEventConnectionRejected.](media-services-event-schemas.md#liveeventconnectionrejected)

| Код результата | Описание |
| ----------- | ----------- |
| MPE_RTMP_APPID_AUTH_FAILURE | Неправильный URL-адрес приема. |
| MPE_INGEST_ENCODER_CONNECTION_DENIED | IP-адрес кодировщика отсутствует в настроенном списке разрешенных IP-адресов. |
| MPE_INGEST_RTMP_SETDATAFRAME_NOT_RECEIVED | Коделер RTMP не отправлял команду setDataFrame. |
| MPE_INGEST_CODEC_NOT_SUPPORTED | Указанный кодек не поддерживается. |
| MPE_INGEST_DESCRIPTION_INFO_NOT_RECEIVED |Информация о описании средств массовой информации не была получена до того, как были предоставлены фактические данные сми.|
| MPE_INGEST_MEDIA_QUALITIES_EXCEEDED |Количество качеств для аудио или видео типа превысило максимально допустимый предел.|
| MPE_INGEST_BITRATE_AGGREGATED_EXCEEDED |Общий входящий битр в живом событии или услуге канала превысил максимально допустимый предел.|
| MPE_RTMP_FLV_TAG_TIMESTAMP_INVALID | Метка времени из кодировщика RTMP для FLVTag видео или аудио недействительна. |
| MPE_INGEST_FRAMERATE_EXCEEDED | Входящий кодер просачивал потоки с частотой кадров, превысил максимально допустимые 30fps для кодирования живых событий/каналов.|
| MPE_INGEST_VIDEO_RESOLUTION_NOT_SUPPORTED | Входящий кодеру попадает потоки превысил следующие допустимые резолюции: 1920x1088 для кодирования живых событий / каналов и 4096 х 2160 для сквозных живых событий / каналов.|

## <a name="liveeventencoderdisconnected"></a>LiveEventEncoderDisconnected.

Вы можете увидеть одну из следующих ошибок из события [LiveEventEncoderDisconnected.](media-services-event-schemas.md#liveeventencoderdisconnected)

|Код результата|Описание|
|---|---|
|MPE_RTMP_SESSION_IDLE_TIMEOUT|Превышено время ожидания сеанса RTMP после простоя в течение допустимого ограничения времени.|
|MPE_RTMP_FLV_TAG_TIMESTAMP_INVALID|Метка времени из кодировщика RTMP для FLVTag видео или аудио недействительна.|
|MPE_CAPACITY_LIMIT_REACHED|Кодировщик отправляет данные слишком быстро.|
|Неизвестные коды ошибок|Эти коды ошибок могут варьироваться от ошибки памяти до дублирования записей в карте хэшей.|


## <a name="see-also"></a>См. также

[Коды ошибок потоковой передачи (Origin)](streaming-endpoint-error-codes.md)

## <a name="next-steps"></a>Дальнейшие действия

[Учебник: Поток жить со средствами массовой информации](stream-live-tutorial-with-api.md)
