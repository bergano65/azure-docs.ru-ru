---
title: Устранение неполадок с использованием кодов ошибок в Azure Stream Analytics
description: Устранить неполадки в работе Azure Stream Analytics помогут коды внутренних ошибок.
ms.author: mamccrea
author: mamccrea
ms.topic: conceptual
ms.date: 05/07/2020
ms.service: stream-analytics
ms.openlocfilehash: c8c792d548fc953cb5d059f322ad1f3139c91562
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83595123"
---
# <a name="azure-stream-analytics-internal-error-codes"></a>Коды внутренних ошибок в Azure Stream Analytics

Журналы действий и журналы ресурсов можно использовать для отладки и устранения ошибок при выполнении заданий в Azure Stream Analytics. В этой статье приводится описание всех кодов внутренних ошибок. Внутренними называются общие ошибки, которые возникают на платформе Stream Analytics, когда она не может определить, произошла внутренняя ошибка доступности или ошибка в системе.

## <a name="kafkainvalidrequest"></a>KafkaInvalidRequest

* **Причина.** Запрос, отправленный на сервер Kafka, является недопустимым.

## <a name="kafkainputerror"></a>KafkaInputError

* **Причина.** При вводе данных в Kafka возникла проблема.

## <a name="cosmosdboutputbatchsizetoolarge"></a>CosmosDBOutputBatchSizeTooLarge

* **Причина.** Слишком большой размер пакета, используемого для записи в Cosmos DB. 
* **Рекомендация**. Повторите попытку с пакетом меньшего размера.

## <a name="next-steps"></a>Дальнейшие действия

* [Устранение неполадок с входящими подключениями](stream-analytics-troubleshoot-input.md)
* [Устранение неполадок с выходными данными в Azure Stream Analytics](stream-analytics-troubleshoot-output.md)
* [Устранение неполадок с запросами в Azure Stream Analytics](stream-analytics-troubleshoot-query.md)
* [Устранение неполадок в Azure Stream Analytics с помощью журналов ресурсов](stream-analytics-job-diagnostic-logs.md)
* [Ошибки данных в Azure Stream Analytics](data-errors.md)