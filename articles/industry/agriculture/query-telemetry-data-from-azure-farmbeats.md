---
title: Запрос попадает телеметрии данных
description: В этой статье описывается, как задать запрос попананных телеметрических данных.
author: sunasing
ms.topic: article
ms.date: 03/11/2020
ms.author: sunasing
ms.openlocfilehash: f717903b3f953e04c793092c86802f2006de7e82
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80349806"
---
# <a name="query-ingested-telemetry-data"></a>Запрос попадает телеметрии данных

В этой статье описывается, как задать запрос данных датчиков с Azure FarmBeats.

Использование данных из ресурсов Интернета вещей (IoT), таких как устройства и датчики, является распространенным сценарием в FarmBeats. Вы создаете метаданные для устройств и датчиков, а затем глотаете исторические данные в FarmBeats в каноническом формате. Как только данные датчика доступны на FarmBeats Datahub, мы можем задать такой же запрос для генерации действенных сведений или построения моделей.

## <a name="before-you-begin"></a>Перед началом

Прежде чем приступить к этой статье, убедитесь, что вы установили FarmBeats и попадаете на данные телеметрии датчиков с ваших устройств IoT на FarmBeats.

Чтобы глотать данные телеметрии датчика, посетите [данные о глотании исторических телеметрии](ingest-historical-telemetry-data-in-azure-farmbeats.md)

Прежде чем продолжить работу, необходимо также убедиться, что вы знакомы с ApIs FarmBeats REST, так как вы будете загонить просачиваемую телеметрию с помощью AIS. Для получения дополнительной информации о [FarmBeats REST APIs](rest-api-in-azure-farmbeats.md)AIS FarmBeats см. **Убедитесь, что вы можете сделать запросы API на конечную точку FarmBeats Datahub.**

## <a name="query-ingested-sensor-telemetry-data"></a>Запрос попадает датчик телеметрии данных

Существует два способа доступа к телеметрическим данным и запросов от FarmBeats:

- API и
- Исследования временных рядов (TSI).

### <a name="query-using-rest-api"></a>Запрос с помощью REST API

Выполните последующие действия по запросу данных телеметрии датчиков с помощью APIs FarmBeats REST:

1. Определите интересуемый вам датчик. Вы можете сделать это, сделав запрос GET на /Sensor API.

> [!NOTE]
> **Идентификатор** и **сенсорModelид** заинтересованного объекта датчика.

2. Сделайте GET /id' на /SensorModel API для **sensorModelId,** как указано в шаге 1. "Сенсорная модель" имеет все метаданные и сведения о проглоченной телеметрии с датчика. Например, **Sensor Measure** в объекте Sensor **Model** имеет подробную информацию о том, какие измерения отправляют датчики и в каких типах и единицах. Например,

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
Обратите внимание на ответ от вызова GET /id для модели датчика.

3. У POST вызова на / Телеметрия API со следующими входными полезной нагрузкой

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
4. Ответ от /Telemetry API будет выглядеть примерно так:

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
В приведенном выше ответе запрашиваемый датчик телеметрии дает данные для двух меток времени вместе с именем измерения ("moist_soil_last") и значениями заявленной телеметрии в двух метки времени. Для интерпретации типа и единицы сообщенных значений необходимо сослаться на связанную модель датчика (описанную в шаге 2).

### <a name="query-using-azure-time-series-insights-tsi"></a>Запрос с использованием Azure Time Series Исследования (TSI)

FarmBeats использует [Azure Time Series Insights (TSI)](https://azure.microsoft.com/services/time-series-insights/) для глотания, хранения, запроса и визуализации данных в масштабе IoT - данных, которые высоко контекстуализированы и оптимизированы для временных рядов.

Телеметрические данные поступают на EventHub, а затем обрабатываются и перемещаются в среду TSI в группе ресурсов FarmBeats. Данные могут быть заданы непосредственно из TSI. Для получения дополнительной [информации](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-explorer) см.

Выполните последующие действия по визуализации данных о TSI:

1. Перейдите на ресурсную группу **Azure Portal** > **FarmBeats DataHub** > выбрать среду **Time Series Insights** (tsi-xxxx) > **политик доступа к данным.** Добавить пользователя с доступом Reader или Contributor.
2. Перейдите на страницу **Обзор** **среды Time Series Insights** (tsi-xxxx) и выберите **URL-адрес Time Series Insights Explorer.** Теперь вы сможете визуализировать пропавшую телеметрию.

Помимо хранения, запроса и визуализации телеметрии, TSI также позволяет интегрироваться в приборную панель Power BI. Для получения дополнительной информации, смотрите [здесь]( https://docs.microsoft.com/azure/time-series-insights/how-to-connect-power-bi)

## <a name="next-steps"></a>Дальнейшие действия

Теперь вы запросили данные датчиков из экземпляра Azure FarmBeats. Теперь узнайте, как [создавать карты](generate-maps-in-azure-farmbeats.md#generate-maps) для ваших ферм.
