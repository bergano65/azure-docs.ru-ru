---
author: normesta
ms.service: storage
ms.topic: include
ms.date: 09/28/2020
ms.author: normesta
ms.openlocfilehash: 48e2d798a3dcf7354e68d07785c2762ba03f39f8
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/24/2020
ms.locfileid: "95560883"
---
В этой таблице показаны [метрики уровня учетной записи](../articles/azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccounts).

| Metric | Описание |
| ------------------- | ----------------- |
| UsedCapacity | Объем хранилища, используемый учетной записью хранения. Для стандартных учетных записей хранения это общая емкость, используемая большим двоичным объектом, таблицей, файлом или очередью. Для учетных записей хранения уровня "Премиум" и учетных записей хранения больших двоичных объектов эта емкость сопоставима с емкостью метрики BlobCapacity. <br/><br/> Единица измерения: Байты <br/> Тип агрегирования: Среднее <br/> Пример значения. 1024 |