---
title: Коды внешних ошибок — Azure Stream Analytics
description: Устранить неполадки в работе Azure Stream Analytics помогут коды внешних ошибок.
ms.author: mamccrea
author: mamccrea
ms.topic: conceptual
ms.date: 05/07/2020
ms.service: stream-analytics
ms.openlocfilehash: e7ef1adea72ed69dc11f0ea8c7e3ce0db3e6ab7c
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83594083"
---
# <a name="azure-stream-analytics-external-error-codes"></a>Коды внешних ошибок в Azure Stream Analytics

Журналы действий и журналы ресурсов можно использовать для отладки и устранения ошибок при выполнении заданий в Azure Stream Analytics. В этой статье приводится описание всех кодов внешних ошибок. Внешние ошибки — это общие ошибки, возвращаемые связанной службой, которые Stream Analytics не может классифицировать как ошибку данных, ошибку конфигурации или ошибку внешней доступности.

## <a name="adapterinitializationerror"></a>AdapterInitializationError

* **Причина.** Произошла ошибка при инициализации адаптера.

## <a name="adapterfailedtowriteevents"></a>AdapterFailedToWriteEvents

* **Причина.** Произошла ошибка при записи данных в адаптер.

## <a name="kafkaservererror"></a>KafkaServerError

* **Причина.** Сервер Kafka вернул ошибку:

## <a name="azurefunctionhttperror"></a>AzureFunctionHttpError

* **Причина.** Функции Azure вернули ошибку HTTP.

## <a name="azurefunctionfailedtosendmessage"></a>AzureFunctionFailedToSendMessage

* **Причина.** Stream Analytics не удалось записать события в Функции Azure.

## <a name="azurefunctionredirecterror"></a>AzureFunctionRedirectError

* **Причина.** При выводе данных в Функции Azure произошла ошибка перенаправления.

## <a name="azurefunctionclienterror"></a>AzureFunctionClientError

* **Причина.** При выводе данных в Функции Azure произошла ошибка клиента.

## <a name="azurefunctionservererror"></a>AzureFunctionServerError

* **Причина.** При выводе данных в Функции Azure произошла ошибка сервера.

## <a name="azurefunctionhttptimeouterror"></a>AzureFunctionHttpTimeOutError

* **Причина.** Не удалось выполнить запись в Функции Azure, так как истекло время ожидания выполнения HTTP-запроса. 
* **Рекомендация**. Проверьте журналы Функций Azure на наличие возможных задержек.

## <a name="eventhubargumenterror"></a>EventHubArgumentError

* **Причина.** Недопустимые смещения входных данных. Это может быть вызвано отработкой отказа.
* **Рекомендация**. Перезапустите задание Stream Analytics с момента последнего вывода.

## <a name="eventhubfailedtowriteevents"></a>EventHubFailedToWriteEvents

* **Причина.** Произошла ошибка при отправке данных в концентратор событий.

## <a name="cosmosdbconnectionfailureaftermaxretries"></a>CosmosDBConnectionFailureAfterMaxRetries

* **Причина.** Stream Analytics не удалось подключиться к учетной записи Cosmos DB после максимального числа повторных попыток.

## <a name="cosmosdbfailureaftermaxretries"></a>CosmosDBFailureAfterMaxRetries

* **Причина.** Stream Analytics не удалось подключиться к базе данных Cosmos DB после максимального числа повторных попыток.

## <a name="cosmosdbfailedtocreatestoredprocedure"></a>CosmosDBFailedToCreateStoredProcedure

* **Причина.** CosmosDB не удалось создать хранимую процедуру после нескольких повторных попыток.

## <a name="cosmosdboutputrequesttimeout"></a>CosmosDBOutputRequestTimeout

* **Причина.** Хранимая процедура upsert вернула ошибку. 

## <a name="sqldatabaseoutputinitializationerror"></a>SQLDatabaseOutputInitializationError

* **Причина.** Stream Analytics не может инициализировать выходные данные Базы данных SQL.

## <a name="sqldatabaseoutputwriteerror"></a>SQLDatabaseOutputWriteError

* **Причина.** Stream Analytics не может записать события в выходные данные Базы данных SQL.

## <a name="sqldwoutputinitializationerror"></a>SQLDWOutputInitializationError

* **Причина.** Произошла ошибка при инициализации выходных данных пула Synapse SQL.

## <a name="sqldwoutputwriteerror"></a>SQLDWOutputWriteError

* **Причина.** Произошла ошибка при записи выходных данных в пул Synapse SQL.

## <a name="next-steps"></a>Дальнейшие действия

* [Устранение неполадок с входящими подключениями](stream-analytics-troubleshoot-input.md)
* [Устранение неполадок с выходными данными в Azure Stream Analytics](stream-analytics-troubleshoot-output.md)
* [Устранение неполадок с запросами в Azure Stream Analytics](stream-analytics-troubleshoot-query.md)
* [Устранение неполадок в Azure Stream Analytics с помощью журналов ресурсов](stream-analytics-job-diagnostic-logs.md)
* [Ошибки данных в Azure Stream Analytics](data-errors.md)
