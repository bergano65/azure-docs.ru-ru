---
title: Запрос полученных данных телеметрии
description: В этой статье описывается, как запросить полученные данные телеметрии.
author: sunasing
ms.topic: article
ms.date: 03/11/2020
ms.author: sunasing
ms.openlocfilehash: 2786519bfc54b39c986ba57c78d9d6409a596351
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/11/2020
ms.locfileid: "79129779"
---
# <a name="query-ingested-telemetry-data"></a>Запрос полученных данных телеметрии

В этой статье описывается, как запросить полученные данные датчика из Azure Фармбеатс.

Получение данных из ресурсов "Интернет вещей" (IoT), таких как устройства и датчики, является распространенным сценарием в Фармбеатс. Вы создаете метаданные для устройств и датчиков, а затем принимаете исторические данные в Фармбеатс в каноническом формате. После того как данные датчика будут доступны в центре данных Фармбеатс, можно выполнить запрос к тому же, чтобы создать ценные сведения или модели сборки.

## <a name="before-you-begin"></a>Перед началом

Прежде чем продолжить работу с этой статьей, убедитесь, что вы установили Фармбеатс и полученные данные телеметрии датчика с устройств IoT на Фармбеатс.
Чтобы принять данные телеметрии датчика, посетите страницу [приема исторических данных телеметрии](ingest-historical-telemetry-data-in-azure-farmbeats.md)

Прежде чем продолжить, убедитесь, что вы знакомы с Фармбеатс API-интерфейсами, так как будете запрашивать полученные данные телеметрии с помощью API. Дополнительные сведения об API-интерфейсах Фармбеатс см. в разделе [Фармбеатс API-интерфейсы RESTful](rest-api-in-azure-farmbeats.md). **Убедитесь, что вы можете выполнять запросы API к конечной точке концентратора данных Фармбеатс.**

## <a name="query-ingested-sensor-telemetry-data"></a>Запрос данных телеметрии для полученных датчиков

Существует два способа доступа и запроса данных телеметрии из Фармбеатс: API и Time Series Insights (TSI). 

### <a name="query-using-rest-api"></a>Запрос с помощью REST API

Выполните следующие действия, чтобы запросить полученные данные телеметрии датчика с помощью интерфейсов API Фармбеатс.

1. Найдите датчик, который вас интересует. Это можно сделать, создав запрос GET в API/сенсор. Запишите **идентификатор** и **сенсормоделид** объекта заинтересованного датчика.

2. Выполните GET/{ID} в API/Сенсормодел для **сенсормоделид** , как указано в шаге 1. "Модель датчика" содержит все метаданные и сведения о полученных данных телеметрии от датчика. Например, "мера датчика" в объекте "модель датчика" содержит подробные сведения о том, какие меры отправляются датчиком и в каких типах и единицах. Например,

```json
{
    "name": "moist_soil_last <name of the sensor measure - this is what we will receive as part of the queried telemetry data>",
    "dataType": "Double <Data Type - eg. Double>",
    "type": "SoilMoisture <Type of measure eg. temperature, soil moisture etc.>",
    "unit": "Percentage <Unit of measure eg. Celsius, Percentage etc.>",
    "aggregationType": "None <either of None, Average, Maximum, Minimum, StandardDeviation>",
    "description": "<Description of the measure>"
}
```
Запишите ответ вызова GET/{ID} для модели датчика.

3. Выполнить вызов POST в API/Телеметри со следующими входными полезными данными

```json
{
  "sensorId": "<id of the sensor as noted in step 1>",
  "searchSpan": {
    "from": "<desired start timestamp in ISO 8601 format; default is UTC>",
    "to": "<desired end timestamp in ISO 8601 format; default is UTC>"
  },
  "filter": {
    "tsx": "string"
  },
  "projectedProperties": [
    {
      "additionalProp1": "string",
      "additionalProp2": "string",
      "additionalProp3": "string"
    }
  ]
}
```
4. Ответ от API/Телеметри будет выглядеть примерно так:

```json
{
  "timestamps": [
    "2020-XX-XXT07:30:00Z",
    "2020-XX-XXT07:45:00Z"
  ],
  "properties": [
    {
      "values": [
        "<id of the sensor>",
        "<id of the sensor>"
      ],
      "name": "Id",
      "type": "String"
    },
    {
      "values": [
        2.1,
        2.2
      ],
      "name": "moist_soil_last <name of the SensorMeasure as defined in the SensorModel object>",
      "type": "Double <Data Type of the value - eg. Double>"
    }
  ]
}
```
В приведенном выше примере ответа на запрос данные телеметрии датчика запрашивается наличие данных для двух меток времени, а также имени меры ("moist_soil_last") и значений данных телеметрии в этих двух отметках. Чтобы интерпретировать тип и единицу сообщаемых значений, необходимо обратиться к связанной модели датчика (как описано в шаге 2).

### <a name="query-using-azure-time-series-insights-tsi"></a>Запрос с использованием службы "аналитика временных рядов Azure" (TSI)

Фармбеатс использует службу ["аналитика временных рядов Azure" (TSI)](https://azure.microsoft.com/services/time-series-insights/) для приема, хранения, запроса и визуализации данных в масштабе "Интернет вещей" (IOT) — данные, которые сильно контекстуальны и оптимизированы для временных рядов.

Данные телеметрии получаются на EventHub, а затем обрабатываются и отправляются в среду TSI в группе ресурсов Фармбеатс. Затем данные можно напрямую запрашивать из TSI. Дополнительные сведения см. в [документации по TSI](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-explorer) .

Выполните следующие действия, чтобы визуализировать данные на TSI

1. Перейдите на портал Azure — > Фармбеатс Датахуб группа ресурсов — > щелкните "среда" аналитики временных рядов "(TSI-XXXX) — > политики доступа к данным. Добавьте пользователя с правами читателя или участника.
2. Перейдите на страницу обзора среды Time Series Insights (TSI-XXXX) и щелкните URL-адрес обозревателя "аналитика временных рядов". Теперь вы сможете визуализировать полученные данные телеметрии.

Помимо хранения, запроса и визуализации телеметрии TSI также обеспечивает интеграцию с Power BI панелью мониторинга. [Дополнительные сведения]( https://docs.microsoft.com/azure/time-series-insights/how-to-connect-power-bi)

## <a name="next-steps"></a>Дальнейшие действия

Теперь вы запросили данные датчиков из экземпляра Azure Фармбеатс. Теперь Узнайте, как [создавать карты](generate-maps-in-azure-farmbeats.md#generate-maps) для ферм.
