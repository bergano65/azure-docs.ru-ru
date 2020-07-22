---
title: Коды внешних ошибок доступности — Azure Stream Analytics
description: Устранить неполадки в работе Azure Stream Analytics помогут коды внешних ошибок доступности.
ms.author: mamccrea
author: mamccrea
ms.topic: troubleshooting
ms.date: 05/07/2020
ms.service: stream-analytics
ms.openlocfilehash: c70a3a2d0630148a077f9c149ba40d48a6b7c0ae
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/07/2020
ms.locfileid: "86045286"
---
# <a name="azure-stream-analytics-external-availability-error-codes"></a>Коды внешних ошибок доступности в Azure Stream Analytics

Журналы действий и журналы ресурсов можно использовать для отладки и устранения ошибок при выполнении заданий в Azure Stream Analytics. В этой статье приводится описание всех кодов внешних ошибок доступности. Внешние ошибки доступности возникают, когда зависимая служба недоступна.

## <a name="externalserviceunavailable"></a>ExternalServiceUnavailable

* **Причина.** Служба временно недоступна.
* **Рекомендация**. Stream Analytics будет и дальше пытаться получить доступ к службе.

## <a name="eventhubmessagingerror"></a>EventHubMessagingError

* **Причина.** В Stream Analytics произошла ошибка при обмене данными с концентратором событий. 


## <a name="next-steps"></a>Дальнейшие действия

* [Устранение неполадок с входящими подключениями](stream-analytics-troubleshoot-input.md)
* [Устранение неполадок с выходными данными в Azure Stream Analytics](stream-analytics-troubleshoot-output.md)
* [Устранение неполадок с запросами в Azure Stream Analytics](stream-analytics-troubleshoot-query.md)
* [Устранение неполадок в Azure Stream Analytics с помощью журналов ресурсов](stream-analytics-job-diagnostic-logs.md)
* [Ошибки данных в Azure Stream Analytics](data-errors.md)
