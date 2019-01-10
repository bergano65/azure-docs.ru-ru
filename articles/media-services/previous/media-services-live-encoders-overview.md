---
title: Настройка локальных кодировщиков при использовании служб мультимедиа Azure для создания потоков с несколькими скоростями | Документация Майкрософт
description: В этом разделе перечислены локальные динамические кодировщики, которые можно использовать для захвата динамических событий и отправки односкоростного динамического потока в каналы AMS (с включенным кодированием в реальном времени) для дальнейшей обработки. Этот раздел содержит ссылки на учебники, в которых описывается, как настроить перечисленные кодировщики.
services: media-services
documentationcenter: ''
author: juliako
manager: cfowler
editor: ''
ms.assetid: 0ec6f046-0841-4673-9057-883bdbc30d5c
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/21/2018
ms.author: juliako
ms.openlocfilehash: c0c7e2b8962cb757141b654c2956ed3ff5600c88
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/27/2018
ms.locfileid: "53787653"
---
# <a name="how-to-configure-on-premises-encoders-when-using-azure-media-services-to-create-multi-bitrate-streams"></a>Как настроить локальные кодировщики при использовании служб мультимедиа Azure для создания потоков с несколькими скоростями
В этом разделе перечислены локальные динамические кодировщики, которые можно использовать для захвата динамических событий и отправки односкоростного динамического потока в каналы AMS (с включенным кодированием в реальном времени) для дальнейшей обработки. Кроме того, этот раздел содержит ссылки на учебники, в которых описывается, как настроить перечисленные кодировщики.

> [!NOTE]
> При потоковой передаче через RTMP проверьте параметры брандмауэра или прокси-сервера, чтобы убедиться, что исходящие TCP-порты 1935 и 1936 открыты.

## <a name="flash-media-live-encoder"></a>Flash Media Live Encoder
Сведения о настройке кодировщика [Flash Media Live Encoder](http://www.adobe.com/products/flash-media-encoder.html) (FMLE) для отправки односкоростного динамического потока в канал AMS см. в статье [Использование кодировщика FMLE для отправки односкоростного обновляющегося потока](media-services-configure-fmle-live-encoder.md).

## <a name="haivision-kb-encoder"></a>Кодировщик Haivision KB
Сведения о настройке кодировщика [Haivision KB](https://www.haivision.com/products/kb-series/) для отправки односкоростного динамического потока в канал AMS см. в статье [Использование кодировщика Haivision KB для отправки односкоростного обновляющегося потока](media-services-configure-kb-live-encoder.md).

## <a name="telestream-wirecast"></a>Telestream Wirecast
Сведения о настройке кодировщика [Telestream Wirecast](http://www.telestream.net/wirecast/overview.htm) для отправки односкоростного динамического потока в канал AMS см. в статье [Использование кодировщика Wirecast для отправки односкоростного обновляющегося потока](media-services-configure-wirecast-live-encoder.md).

## <a name="newtek-tricaster"></a>NewTek TriCaster
Сведения о настройке кодировщика [Tricaster](http://newtek.com/products/tricaster-40.html) для отправки односкоростного динамического потока в канал AMS см. в статье [Использование кодировщика NewTek TriCaster для отправки односкоростного обновляющегося потока](media-services-configure-tricaster-live-encoder.md).

## <a name="elemental-live"></a>Elemental Live
Дополнительные сведения см. в разделе [Elemental Live](http://www.elementaltechnologies.com/products/elemental-live).

## <a name="media-services-learning-paths"></a>Схемы обучения работе со службами мультимедиа
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Отзывы
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Дополнительная информация

[Потоковая трансляция с использованием служб мультимедиа Azure для создания потоков с разными скоростями](media-services-manage-live-encoder-enabled-channels.md).

