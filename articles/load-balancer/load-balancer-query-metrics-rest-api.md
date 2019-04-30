---
title: Извлечение метрик через REST API
titlesuffix: Azure Load Balancer
description: Azure REST API позволяет собирать метрики работоспособности и использования Load Balancer за определенный диапазон времени и дат.
services: sql-database
author: KumudD
ms.reviewer: routlaw
manager: jeconnoc
ms.service: load-balancer
ms.custom: REST, seodec18
ms.topic: article
ms.date: 06/06/2017
ms.author: KumudD
ms.openlocfilehash: 9f5206ef5348ee8fd7b3fe981a9cfe4afc1367fb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60734547"
---
# <a name="get-load-balancer-utilization-metrics-using-the-rest-api"></a>Получение метрик использования Load Balancer с помощью REST API

В этом практическом руководстве показано, как собрать данные о количестве обрабатываемых службой [Load Balancer ценовой категории "Стандартный"](/azure/load-balancer/load-balancer-standard-overview) байтов за определенный период времени с помощью [Azure REST API](/rest/api/azure/).

Полная справочная документация и дополнительные образцы для REST API можно найти в [Azure Monitor REST API reference](/rest/api/monitor) (Справочник по REST API Azure Monitor). 

## <a name="build-the-request"></a>Создание запроса

Чтобы собрать данные [метрики ByteCount](/azure/load-balancer/load-balancer-standard-diagnostics#multi-dimensional-metrics) с Load Balancer ценовой категории "Стандартный", выполните приведенный ниже запрос GET. 

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/loadBalancers/{loadBalancerName}/providers/microsoft.insights/metrics?api-version=2018-01-01&metricnames=ByteCount&timespan=2018-06-05T03:00:00Z/2018-06-07T03:00:00Z
```

### <a name="request-headers"></a>Заголовки запросов

Ниже приведены обязательные заголовки. 

|Заголовок запроса|Описание|  
|--------------------|-----------------|  
|*Content-Type:*|Обязательный. Задайте значение `application/json`.|  
|*Authorization:*|Обязательный. Задайте в качестве значения [допустимый токен доступа](/rest/api/azure/#authorization-code-grant-interactive-clients) `Bearer`. |  

### <a name="uri-parameters"></a>Параметры универсального кода ресурса (URI)

| ИМЯ | Описание |
| :--- | :---------- |
| subscriptionId | Идентификатор подписки Azure. Если у вас несколько подписок, см. раздел [Использование нескольких подписок](https://docs.microsoft.com/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest). |
| resourceGroupName | Имя группы ресурсов, к которой относится ресурс. Это значение можно получить с помощью API-интерфейса Azure Resource Manager, CLI или портала. |
| loadBalancerName | Имя Azure Load Balancer. |
| metricnames | Разделенный запятыми список допустимых [метрик Load Balancer](/azure/load-balancer/load-balancer-standard-diagnostics). |
| api-version | Версия API для использования в запросе.<br /><br /> В этом документе рассматривается API версии `2018-01-01`, которая включена в приведенный выше URL-адрес.  |
| Интервал времени | Временной диапазон запроса. Это строка в формате `startDateTime_ISO/endDateTime_ISO`. Этот необязательный параметр имеет значение для возврата суточных данных в примере. |
| &nbsp; | &nbsp; |

### <a name="request-body"></a>Текст запроса

Для этой операции текст запроса не требуется.

## <a name="handle-the-response"></a>Обработка ответа

Код состояния 200 возвращается в том случае, если успешно возвращается список значений метрик. Полный список кодов ошибок доступен в [справочной документации](/rest/api/monitor/metrics/list#errorresponse).

## <a name="example-response"></a>Пример ответа 

```json
{
    "cost": 0,
    "timespan": "2018-06-05T03:00:00Z/2018-06-07T03:00:00Z",
    "interval": "PT1M",
    "value": [
        {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/loadBalancers/{loadBalancerName}/providers/Microsoft.Insights/metrics/ByteCount",
            "type": "Microsoft.Insights/metrics",
            "name": {
                "value": "ByteCount",
                "localizedValue": "Byte Count"
            },
            "unit": "Count",
            "timeseries": [
                {
                    "metadatavalues": [],
                    "data": [
                        {
                            "timeStamp": "2018-06-06T17:24:00Z",
                            "total": 1067921034.0
                        },
                        {
                            "timeStamp": "2018-06-06T17:25:00Z",
                            "total": 0.0
                        },
                        {
                            "timeStamp": "2018-06-06T17:26:00Z",
                            "total": 3781344.0
                        },
                    ]
                }
            ]
        }
    ],
    "namespace": "Microsoft.Network/loadBalancers",
    "resourceregion": "eastus"
}
```
