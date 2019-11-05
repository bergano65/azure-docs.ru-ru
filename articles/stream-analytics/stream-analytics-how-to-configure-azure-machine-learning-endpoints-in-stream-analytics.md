---
title: Использование конечных точек службы "Машинное обучение" в Azure Stream Analytics
description: В этой статье объясняется, как использовать определяемые пользователем функции машинного обучения в Azure Stream Analytics.
services: stream-analytics
author: jseb225
ms.author: jeanb
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/11/2019
ms.openlocfilehash: 1adb7d58246ea37fd8322cb6fc6ffd53c5f19efb
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73467818"
---
# <a name="azure-machine-learning-studio-classic-integration-in-stream-analytics-preview"></a>Интеграция Машинное обучение Azure Studio (классическая модель) в Stream Analytics (Предварительная версия)
Stream Analytics поддерживает пользовательские функции, которые вызывают конечные точки Машинное обучение Azure Studio (классические). Поддержка этой возможности в REST API описана в статье [Библиотека REST API Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx). В этой статье приведены дополнительные сведения, необходимые для успешной реализации этой возможности в Stream Analytics. Также было размещено руководство, которое доступно [здесь](stream-analytics-machine-learning-integration-tutorial.md).

## <a name="overview-azure-machine-learning-studio-classic-terminology"></a>Обзор: терминология Машинное обучение Azure Studio (классическая модель)
Студия машинного обучения Microsoft Azure (классическая модель) предоставляет инструмент для совместной работы и перетаскивания, который можно использовать для создания, тестирования и развертывания решений прогнозной аналитики на основе данных. Это средство называется *машинное обучение Azure Studio (классическая модель)* . Студия используется для взаимодействия с ресурсами машинного обучения, а также для компилирования и тестирования прототипа с последующими итерациями. Эти ресурсы и их определения приведены ниже.

* **Рабочая область**. *Рабочая область* — это контейнер, содержащий все ресурсы машинного обучения для управления и контроля.
* **Эксперимент**. *Эксперименты* создаются специалистами по обработке и анализу данных для использования наборов данных и обучения моделей машинного обучения.
* **Конечная точка**. *конечные точки* — это объект машинное обучение Azure Studio (классический), используемый для получения в качестве входных данных функций, применения указанной модели машинного обучения и возврата выводимых данных.
* **Оценивающая веб-служба**. *Оценивающая веб-служба* — это коллекция конечных точек, упомянутых выше.

Каждая конечная точка имеет API для пакетного и синхронного выполнения. В Stream Analytics используется синхронное выполнение. Конкретная служба называется службой " [запрос-ответ](../machine-learning/studio/consume-web-services.md) " в машинное обучение Azure Studio (классическая модель).

## <a name="machine-learning-resources-needed-for-stream-analytics-jobs"></a>Ресурсы машинного обучения, необходимые для заданий Stream Analytics
Для успешной обработки задания Stream Analytics требуются конечная точка типа "запрос — ответ", [apikey](../machine-learning/machine-learning-connect-to-azure-machine-learning-web-service.md)и определение swagger. В Stream Analytics есть дополнительная конечная точка, которая создает URL-адрес конечной точки swagger, ищет интерфейс, а затем возвращает пользователю определение определяемой пользователем функции по умолчанию.

## <a name="configure-a-stream-analytics-and-machine-learning-udf-via-rest-api"></a>Настройка Stream Analytics и определяемой пользователем функции машинного обучения с помощью REST API
С помощью интерфейсов REST API можно настроить задание для вызова функций машинного обучения Azure. Для этого необходимо выполнить следующие шаги:

1. Создание задания Stream Analytics
2. Определение входных данных.
3. Определение выходных данных.
4. Создание определяемой пользователем функции
5. Создание преобразования Stream Analytics, которое вызывает определяемую пользователем функцию.
6. Запустите задание

## <a name="creating-a-udf-with-basic-properties"></a>Создание определяемой пользователем функции с базовыми свойствами
Например, в следующем примере кода создается скалярная определяемая пользователем функция с именем *невудф* , которая привязывается к конечной точке машинное обучение Azure Studio (классической). Обратите внимание, что *конечную точку* (URI службы) можно найти на странице справки по API для выбранной службы, а *apiKey* — на главной странице служб.

```
    PUT : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.StreamAnalytics/streamingjobs/<streamingjobName>/functions/<udfName>?api-version=<apiVersion>
```

Пример тела запроса:

```json
    {
        "name": "newudf",
        "properties": {
            "type": "Scalar",
            "properties": {
                "binding": {
                    "type": "Microsoft.MachineLearning/WebService",
                    "properties": {
                        "endpoint": "https://ussouthcentral.services.azureml.net/workspaces/f80d5d7a77fb4b46bf2a30c63c078dca/services/b7be5e40fd194258796fb402c1958eaf/execute ",
                        "apiKey": "replacekeyhere"
                    }
                }
            }
        }
    }
```

## <a name="call-retrievedefaultdefinition-endpoint-for-default-udf"></a>Вызов конечной точки RetrieveDefaultDefinition для определяемой пользователем функции по умолчанию
После создания схемы определяемой пользователем функции требуется получить ее полное определение. Конечная точка RetrieveDefaultDefinition помогает получить определение по умолчанию для скалярной функции, привязанной к конечной точке Машинное обучение Azure Studio (классической). Для приведенных ниже полезных данных требуется получить определение указанной пользователем функции по умолчанию для скалярной функции, которая привязана к конечной точке машинного обучения Azure. Фактическая конечная точка не указывается, так как она задана во время запроса PUT. Stream Analytics вызывает указанную в запросе конечную точку, если она задана явно. В противном случае будет использоваться конечная точка, указанная изначально. Здесь определяемая пользователем функция (UDF) принимает один строковой параметр (предложение) и возвращает один вывод строкового типа, который указывает на метку "тональность" для этого предложения.

```
POST : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.StreamAnalytics/streamingjobs/<streamingjobName>/functions/<udfName>/RetrieveDefaultDefinition?api-version=<apiVersion>
```

Пример тела запроса:

```json
    {
        "bindingType": "Microsoft.MachineLearning/WebService",
        "bindingRetrievalProperties": {
            "executeEndpoint": null,
            "udfType": "Scalar"
        }
    }
```

Вывод будет выглядеть примерно так, как показано ниже.

```json
    {
        "name": "newudf",
        "properties": {
            "type": "Scalar",
            "properties": {
                "inputs": [{
                    "dataType": "nvarchar(max)",
                    "isConfigurationParameter": null
                }],
                "output": {
                    "dataType": "nvarchar(max)"
                },
                "binding": {
                    "type": "Microsoft.MachineLearning/WebService",
                    "properties": {
                        "endpoint": "https://ussouthcentral.services.azureml.net/workspaces/f80d5d7a77ga4a4bbf2a30c63c078dca/services/b7be5e40fd194258896fb602c1858eaf/execute",
                        "apiKey": null,
                        "inputs": {
                            "name": "input1",
                            "columnNames": [{
                                "name": "tweet",
                                "dataType": "string",
                                "mapTo": 0
                            }]
                        },
                        "outputs": [{
                            "name": "Sentiment",
                            "dataType": "string"
                        }],
                        "batchSize": 10
                    }
                }
            }
        }
    }
```

## <a name="patch-udf-with-the-response"></a>Исправление для определяемой пользователем функции с помощью ответа
Теперь необходимо исправить определяемую пользователем функцию с использованием предыдущего ответа, как показано ниже.

```
PATCH : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.StreamAnalytics/streamingjobs/<streamingjobName>/functions/<udfName>?api-version=<apiVersion>
```

Текст запроса (вывод из RetrieveDefaultDefinition):

```json
    {
        "name": "newudf",
        "properties": {
            "type": "Scalar",
            "properties": {
                "inputs": [{
                    "dataType": "nvarchar(max)",
                    "isConfigurationParameter": null
                }],
                "output": {
                    "dataType": "nvarchar(max)"
                },
                "binding": {
                    "type": "Microsoft.MachineLearning/WebService",
                    "properties": {
                        "endpoint": "https://ussouthcentral.services.azureml.net/workspaces/f80d5d7a77ga4a4bbf2a30c63c078dca/services/b7be5e40fd194258896fb602c1858eaf/execute",
                        "apiKey": null,
                        "inputs": {
                            "name": "input1",
                            "columnNames": [{
                                "name": "tweet",
                                "dataType": "string",
                                "mapTo": 0
                            }]
                        },
                        "outputs": [{
                            "name": "Sentiment",
                            "dataType": "string"
                        }],
                        "batchSize": 10
                    }
                }
            }
        }
    }
```

## <a name="implement-stream-analytics-transformation-to-call-the-udf"></a>Реализация преобразования Stream Analytics для вызова определяемой пользователем функции
Теперь выполните запрос к определяемой пользователем функции (здесь с именем scoreTweet) для каждого входного события и запишите ответ для этого события в вывод.

```json
    {
        "name": "transformation",
        "properties": {
            "streamingUnits": null,
            "query": "select *,scoreTweet(Tweet) TweetSentiment into blobOutput from blobInput"
        }
    }
```


## <a name="get-help"></a>Получение справки
Дополнительную помощь и поддержку вы можете получить на нашем [форуме Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Дальнейшие действия
* [Введение в Azure Stream Analytics](stream-analytics-introduction.md)
* [Приступая к работе с Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Масштабирование заданий в службе Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Справочник по языку запросов Azure Stream Analytics](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Справочник по API-интерфейсу REST управления Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
