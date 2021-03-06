---
title: Состояния LiveEvent и выставление счетов в Службах мультимедиа Azure | Документация Майкрософт
description: В этой статье представлены общие сведения о состояниях компонента LiveEvent и выставлении счетов за его использование.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 10/24/2019
ms.author: juliako
ms.openlocfilehash: af3d4b51dadfaa99a166ca0ce475c5a110d8f6e8
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2019
ms.locfileid: "72933679"
---
# <a name="live-event-states-and-billing"></a>Состояния события потоковой трансляции и выставление счетов

В Службах мультимедиа Azure начисление платы за использование события потоковой трансляции начинается, как только оно переходит в состояние **Выполняется**. Чтобы остановить начисление платы за использование события потоковой трансляции, его нужно остановить.

Когда **лививентенкодингтипе** в [прямом эфире](https://docs.microsoft.com/rest/api/media/liveevents) имеет значение Standard или Premium1080p, службы мультимедиа по-прежнему завершают любое активное событие, которое еще находится в **работающем** состоянии 12 часов после того, как входной канал будет потерян, а **выходные данные** отсутствуют. с. Но при этом вам будет начислена плата за все время, пока событие потоковой трансляции находилось в состоянии **Выполняется**.

> [!NOTE]
> Передаваемые Интерактивные события не завершаются автоматически и должны быть явно остановлены через API, чтобы избежать чрезмерного выставления счетов. 

## <a name="states"></a>Состояния

Событие потоковой трансляции может находиться в одном из указанных ниже состояний.

|Состояние|Описание|
|---|---|
|**Stopped**| Это начальное состояние события прямой трансляции после создания (если для параметра автозапуска не задано значение true). В этом состоянии выставление счетов не выполняется. В этом состоянии можно изменять свойства трансляции, но потоковая передача невозможна.|
|**Запуск**| Событие потоковой трансляции запускается, и выделяются ресурсы. В этом состоянии начисление оплаты не происходит. В этом состоянии обновление и потоковая передача запрещены. Если возникает ошибка, событие потоковой трансляции возвращается в состояние "Остановлено".|
|**Выполнение**| Ресурсы события потоковой трансляции выделены, URL-адреса приема и предварительного просмотра созданы, и компонент может получать потоки в реальном времени. На этом этапе начисление оплаты активно. Чтобы остановить начисление оплаты, нужно явно вызвать функцию Stop (Остановить) для ресурса события потоковой трансляции.|
|**Остановка**| Работа события потоковой трансляции приостанавливается, и выделенные ресурсы автоматически освобождаются. В этом переходном состоянии оплата не начисляется. В этом состоянии обновление и потоковая передача запрещены.|
|**Удаление**| Трансляция удаляется. В этом переходном состоянии оплата не начисляется. В этом состоянии обновление и потоковая передача запрещены.|

## <a name="next-steps"></a>Дальнейшие действия

- [Потоковая трансляция в Службах мультимедиа Azure версии 3](live-streaming-overview.md)
- [Руководство по потоковой трансляции](stream-live-tutorial-with-api.md)
