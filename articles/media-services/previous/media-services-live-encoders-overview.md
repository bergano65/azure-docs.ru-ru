---
title: Настройка локальных кодировщиков при использовании служб мультимедиа Azure для создания потоков с несколькими скоростями | Документация Майкрософт
description: В этом разделе перечислены локальные динамические кодировщики, которые можно использовать для захвата динамических событий и отправки односкоростного динамического потока в каналы AMS (с включенным кодированием в реальном времени) для дальнейшей обработки. Этот раздел содержит ссылки на учебники, в которых описывается, как настроить перечисленные кодировщики.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: 0ec6f046-0841-4673-9057-883bdbc30d5c
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 8bd0014518dc93abb952114a1728f8fc1d3fb4a5
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2019
ms.locfileid: "64708076"
---
# <a name="how-to-configure-on-premises-encoders-when-using-azure-media-services-to-create-multi-bitrate-streams"></a>Как настроить локальные кодировщики при использовании служб мультимедиа Azure для создания потоков с несколькими скоростями
В этом разделе перечислены локальные динамические кодировщики, которые можно использовать для захвата динамических событий и отправки односкоростного динамического потока в каналы AMS (с включенным кодированием в реальном времени) для дальнейшей обработки. Кроме того, этот раздел содержит ссылки на учебники, в которых описывается, как настроить перечисленные кодировщики.

> [!NOTE]
> При потоковой передаче через RTMP проверьте параметры брандмауэра или прокси-сервера, чтобы убедиться, что исходящие TCP-порты 1935 и 1936 открыты.

## <a name="flash-media-live-encoder"></a>Flash Media Live Encoder
Сведения о настройке кодировщика [Flash Media Live Encoder](https://www.adobe.com/products/flash-media-encoder.html) (FMLE) для отправки односкоростного динамического потока в канал AMS см. в статье [Использование кодировщика FMLE для отправки односкоростного обновляющегося потока](media-services-configure-fmle-live-encoder.md).

## <a name="haivision-kb-encoder"></a>Кодировщик Haivision KB
Сведения о настройке кодировщика [Haivision KB](https://www.haivision.com/products/kb-series/) для отправки односкоростного динамического потока в канал AMS см. в статье [Использование кодировщика Haivision KB для отправки односкоростного обновляющегося потока](media-services-configure-kb-live-encoder.md).

## <a name="telestream-wirecast"></a>Telestream Wirecast
Сведения о настройке кодировщика [Telestream Wirecast](https://www.telestream.net/wirecast/overview.htm) для отправки односкоростного динамического потока в канал AMS см. в статье [Использование кодировщика Wirecast для отправки односкоростного обновляющегося потока](media-services-configure-wirecast-live-encoder.md).

## <a name="newtek-tricaster"></a>NewTek TriCaster
Сведения о настройке кодировщика [Tricaster](https://newtek.com/products/tricaster-40.html) для отправки односкоростного динамического потока в канал AMS см. в статье [Использование кодировщика NewTek TriCaster для отправки односкоростного обновляющегося потока](media-services-configure-tricaster-live-encoder.md).

## <a name="elemental-live"></a>Elemental Live
Дополнительные сведения см. в разделе [Elemental Live](https://www.elementaltechnologies.com/products/elemental-live).

## <a name="media-services-learning-paths"></a>Схемы обучения работе со службами мультимедиа
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Отзывы
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Дальнейшие действия

[Потоковая трансляция с использованием служб мультимедиа Azure для создания потоков с разными скоростями](media-services-manage-live-encoder-enabled-channels.md).

