---
title: Коды внешних ошибок доступности — Azure Stream Analytics
description: Устранить неполадки в работе Azure Stream Analytics помогут коды внешних ошибок доступности.
ms.author: mamccrea
author: mamccrea
ms.topic: conceptual
ms.date: 05/07/2020
ms.service: stream-analytics
ms.openlocfilehash: 1ce867d60c53b9befe5b4480693b54d2c9eba018
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83650051"
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
