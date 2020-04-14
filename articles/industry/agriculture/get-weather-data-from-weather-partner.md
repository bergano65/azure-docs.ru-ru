---
title: Получайте данные о погоде от партнеров по погоде
description: В этой статье описывается, как получать данные о погоде от партнеров.
author: sunasing
ms.topic: article
ms.date: 03/31/2020
ms.author: sunasing
ms.openlocfilehash: 66fa4e7d3edf839ab1e497e86362bcfc979dc279
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/13/2020
ms.locfileid: "81266166"
---
# <a name="get-weather-data-from-weather-partners"></a>Получайте данные о погоде от партнеров по погоде

Azure FarmBeats помогает вам доводить данные о погоде от поставщика метеорологических данных (ы) с помощью докерной системы Connector Framework. Используя эту платформу, поставщики метеорологических данных реализуют докер, который может быть интегрирован с FarmBeats. В настоящее время поддерживаются следующие поставщики метеорологических данных:

[Данные NOAA из открытых наборов данных Azure](https://azure.microsoft.com/services/open-datasets/)

Данные о погоде могут быть использованы для создания действенных аналитических данных и создания моделей AI/ML на FarmBeats.

## <a name="before-you-start"></a>Перед началом работы

Чтобы получить данные о погоде, убедитесь, что вы установили FarmBeats. **Интеграция погоды поддерживается в версии 1.2.11 или выше.** Для установки Azure FarmBeats [см.](https://aka.ms/farmbeatsinstalldocumentation)

## <a name="enable-weather-integration-with-farmbeats"></a>Включить интеграцию погоды с FarmBeats

Чтобы начать получать данные о погоде в центре данных FarmBeats, выполните ниже:

1. Перейти к вашей FarmBeats данных концентратор чванство (https://yourdatahub.azurewebsites.net/swagger)

2. Перейдите на /Partner API и сделайте запрос POST со следующей полезной нагрузкой ввода:

   ```json
   {  
 
     "dockerDetails": {  
       "credentials": { 
         "username": "<credentials to access private docker - not required for public docker>", 
         "password": "<credentials to access private docker – not required for public docker>"  
       },  
       "imageName" : "<docker image name. Default is azurefarmbeats/fambeats-noaa>",
       "imageTag" : "<docker image tag, default:latest>",
       "azureBatchVMDetails": {  
         "batchVMSKU" : "<VM SKU. Default is standard_d2_v2>",  
         "dedicatedComputerNodes" : 1,
         "nodeAgentSKUID": "<Node SKU. Default is batch.node.ubuntu 18.04>"  
       }
     },
     "partnerCredentials": {  
       "key1": "value1",  
       "key2": "value2"  
     },  
     "partnerType": "Weather",  
     "name": "<Name of the partner>",  
     "description": "<Description>",
     "properties": {  }  
   }  
   ```

   Например, для получения метеорологических данных от NOAA с помощью открытых наборов данных Azure, используйте полезную нагрузку ниже. Вы можете изменить имя и описание в своих предпочтениях.

   ```json
   {
 
     "dockerDetails": {
       "imageName": "azurefarmbeats/farmbeats-noaa",
       "imageTag": "latest",
       "azureBatchVMDetails": {
         "batchVMSKU": "standard_d2_v2",
         "dedicatedComputerNodes": 1,
         "nodeAgentSKUID": "batch.node.ubuntu 18.04"
       }
     },
     "partnerType": "Weather",
     "name": "ods-noaa",
     "description": "NOAA data from Azure Open Datasets registered as a Weather Partner"
   }  
   ```

   > [!NOTE]
   > Для получения дополнительной информации [Appendix](get-weather-data-from-weather-partner.md#appendix) об объекте Партнера см.

   На предыдущем этапе будут выделены ресурсы для работы докера в среде FarmBeats клиента.  

   Предоставление вышеуказанных ресурсов занимает около 10-15 минут.

3. Проверьте состояние объекта /Партнера, созданного в шаге 2. Для этого сделайте запрос GET на /Partner API и проверьте **состояние** объекта-партнера. Как только FarmBeats успешно обеспечивает партнера, статус устанавливается **для Active**.

4. Перейдите на API /JobType и сделайте запрос GET на том же. Проверьте погодные задания, которые создаются в рамках процесса добавления Партнеров в шаге 2. Поле **трубопровода** в метеоусловиях будет следующего формата: "партнер-name_partner-type_job-имя".

5. Теперь в экземпляре FarmBeats есть активный партнер по метеорологическим данным, и вы можете заводить задания для запроса метеорологических данных для определенного местоположения (широта/долгота) и диапазона дат. JobType (ы) будет иметь подробную информацию о том, какие параметры необходимы для выполнения погодных заданий.

   Например, для данных NOAA из открытых наборов данных Azure будут созданы следующие данные JobType(ы):

   - get_weather_data (Получить данные о погоде ISD/исторические погодные данные)
   - get_weather_forecast_data (Получить GFS / прогноз погоды)

6. Сделать заметку **id** и параметров JobType (ы).

7. Перейдите на /Jobs API и сделайте запрос POST на /Jobs со следующей полезной нагрузкой ввода:

   ```json
    {
         "typeId": "<id of the JobType>",
         "arguments": {
           "additionalProp1": {},
           "additionalProp2": {},
           "additionalProp3": {}
         },
         "name": "<name of the job>",
         "description": "<description>",
         "properties": {}
       }
   ```

   Например, для запуска **get_weather_data**используйте следующую полезную нагрузку:

   ```json
   {
 
         "typeId": "<id of the JobType>",
         "arguments": {
               "latitude": 47.620422,
               "longitude": -122.349358,
               "start_date": "yyyy-mm-dd",
               "end_date": "yyyy-mm-dd"
         },
         "name": "<name of the job>",
         "description": "<description>",
         "properties": {}
   }
   ```

8. На предыдущем этапе будут запущены задания погоды, определенные в докере-партнере, и глотать данные о погоде в FarmBeats. Вы можете проверить состояние задания, сделав запрос GET на /Jobs и посмотреть **текущеесостояние** в ответе. После завершения текущего состояния устанавливается для **успешного.**

## <a name="query-ingested-weather-data"></a>Запрос попадает данные о погоде

После завершения заданий погоды можно задать запрос по погоде для создания моделей или настойчивых сведений. Существует два способа доступа к данным о погоде и запросах от FarmBeats:

- API и
- Исследования временных рядов (TSI).

### <a name="query-using-rest-api"></a>Запрос с помощью REST API

Чтобы задать запрос метеорологических данных с помощью API FarmBeats REST, выполните ниже:

1. В вашем FarmBeats данныхhttps://yourdatahub.azurewebsites.net/swagger)концентратор чванство ( , перейти к / WeatherDataLocation API и сделать запрос GET. Ответ будет иметь /WeatherDataLocation объект (ы), созданный для местоположения (широта / долгота), который был указан как часть выполнения задания. Сделать к сведению **идентификатор** и **weatherDataModelId** объекта (ы).

2. Сделайте GET /id на /WeatherDataModel API для **weatherDataModelId,** как отмечено в шаге 1. "Модель данных погоды" имеет все метаданные и сведения о проглоченных данных о погоде. Например, в измерении **погоды** в объекте **Weather Data Model** есть сведения о том, какая информация о погоде поддерживается и в каких типах и единицах. Например,

   ```json
   {
       "name": "Temperature <name of the weather measure - this is what we will receive as part of the queried weather data>",
       "dataType": "Double <Data Type - eg. Double, Enum>",
       "type": "AmbientTemperature <Type of measure eg. AmbientTemperature, Wind speed etc.>",
       "unit": "Celsius <Unit of measure eg. Celsius, Percentage etc.>",
       "aggregationType": "None <either of None, Average, Maximum, Minimum, StandardDeviation, Sum, Total>",
       "description": "<Description of the measure>"
   }
   ```

   Обратите внимание на ответ от вызова GET / «Id» для модели данных о погоде.

3. Перейдите на API **телеметрии** и сделайте запрос POST со следующей полезной нагрузкой ввода:

   ```json
   {
       "weatherDataLocationId": "<id from step 1 above>",
     "searchSpan": {
       "from": "2020-XX-XXT07:30:00Z",
       "to": "2020-XX-XXT07:45:00Z"
     }
   }
   ```

4. Ответ, содержащий метеорологические данные, доступные для указанного диапазона времени, будет выглядеть следующим образом:

   ```json
   {
     "timestamps": [
       "2020-XX-XXT07:30:00Z",
       "2020-XX-XXT07:45:00Z"
     ],
     "properties": [
       {
         "values": [
           "<id of the weatherDataLocation>",
           "<id of the weatherDataLocation>"
         ],
         "name": "Id",
         "type": "String"
       },
       {
         "values": [
           29.1,
           30.2
         ],
         "name": "Temperature <name of the WeatherMeasure as defined in the WeatherDataModel object>",
         "type": "Double <Data Type of the value - eg. Double>"
       }
     ]
   }
   ```

В предыдущем примере ответ имеет данные по двум меткам времени, а также названию измерения ("Температура") и значениям данных о погоде в двух меткях времени. Для интерпретации типа и единицы сообщенных значений необходимо сослаться на связанную модель данных о погоде (описанную в шаге 2 выше).

### <a name="query-using-azure-time-series-insights-tsi"></a>Запрос с использованием Azure Time Series Исследования (TSI)

FarmBeats использует [Azure Time Series Insights (TSI)](https://azure.microsoft.com/services/time-series-insights/) для поглаживания, хранения, запроса и визуализации данных в масштабе IoT — данных, которые высоко контекстуализированы и оптимизированы для временных рядов.

Данные о погоде поступают на EventHub, а затем перемещаются в среду TSI в группе ресурсов FarmBeats. Данные могут быть заданы непосредственно из TSI. Для получения дополнительной [информации](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-explorer)см.

Выполните последующие действия по визуализации данных о TSI:

1. Перейдите на **портал** > Azure**FarmBeats DataHub, группу ресурсов >** выбрать среду Time Series **Insights** (tsi-xxxx) > **политики доступа к данным.** Добавить пользователя с доступом Reader или Contributor.

2. Перейдите на страницу **Обзор** **среды Time Series Insights** (tsi-xxxx) и выберите **URL-адрес Time Series Insights Explorer.** Теперь вы можете визуализировать просаженные данные о погоде.

Помимо хранения, запроса и визуализации метеорологических данных, TSI также позволяет интегрироваться в панель мониторинга Power BI. Для получения дополнительной информации смотрите [визуализировать данные из Time Series Исследования в Power BI](https://docs.microsoft.com/azure/time-series-insights/how-to-connect-power-bi).

## <a name="appendix"></a>Приложение

|        Партнер   |  Сведения   |
| ------- | -------             |
|     DockerDetails - imageName         |          Имя докера. Например, docker.io/azurefarmbeats/farmbeats-noaa (изображение в hub.docker.com) или myazureacr.azurecr.io/mydockerimage (изображение в реестре контейнеров Azure) и так далее. Если реестр не предоставляется, по умолчанию hub.docker.com      |
|          DockerDetails - imageTag             |         Название тегов изображения докера. По умолчанию является "последним"     |
|  DockerDetails - учетные данные      |  Полномочия для доступа к частному докеру. Это будет предоставлено партнером клиенту   |
|  DockerDetails - azureBatchVMDetails - batchVMSKU     |    Azure Batch VM SKU. Смотрите [здесь](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) для всех Linux виртуальных машин доступны. Действительные значения: "Маленький", "ЭкстраБольшой", "Большой", "A8", "A9", "Средний", "А5", "А6", "А7", "STANDARD_D1", "STANDARD_D2", "STANDARD_D3", "STANDARD_D4", "STANDARD_D11", "STANDARD_D12", "STANDARD_D13", "STANDARD_D14", "А10", "А11", "STANDARD_D1_V2", "STANDARD_D2_V2", "STANDARD_D3_V2", "STANDARD_D4_V2", "STANDARD_D12_V2 STANDARD_D11_V2 STANDARD_D4_V2", "STANDARD_G4 STANDARD_G3 STANDARD_G2 STANDARD_G1 STANDARD_D14_V2 STANDARD_D13_V2", "STANDARD_D4_V2", "", "", "", "", "", "", "", "", "", "", "", "", "", "", "", "", "", "", "", "", "", "", "", "", "", "", "", "", "", "", "", "", "", "", "", "", "", "", "", "", "", "", "", "", "", "", "", "", "", "", "", "", "", "", "", "", "", "", " , «STANDARD_G5», «STANDARD_D5_V2», «BASIC_A1», «BASIC_A2», «BASIC_A3», «BASIC_A4», «STANDARD_A1», «STANDARD_A2», «STANDARD_A3», «STANDARD_A4», «STANDARD_A5», «STANDARD_A6», «STANDARD_A7», «STANDARD_A8», «STANDARD_A9», «STANDARD_A10», «STANDARD_A11», «STANDARD_D15_V2», «STANDARD_F4 STANDARD_F2 STANDARD_F1», «STANDARD_NC24r STANDARD_NC24 STANDARD_NC6 STANDARD_NV24 STANDARD_NV12 STANDARD_NV6 STANDARD_F16 STANDARD_F8 STANDARD_NC12», «STANDARD_NC12», «STANDARD_NC12», «STANDARD_NC12», «STANDARD_NC12», «STANDARD_NC12», «STANDARD_NC12», «STANDARD_NC12», «STANDARD_NC12», «STANDARD_NC12», «STANDARD_NC12», «STANDARD_NC12», «STANDARD_NC12», «STANDARD_NC12», «STANDARD_NC12», «STANDARD_NC12», «STANDARD_NC12», «STANDARD_NC12», «STANDARD_NC12», «STANDARD_NC12», «STANDARD_NC12», «STANDARD_NC12», «STANDARD_NC12», «STANDARD_NC12», «STANDARD_NC12», «STANDARD_NC12», «STANDARD_NC12», «STANDARD_NC12», «STANDARD_NC12», «STANDARD_NC12», «STANDARD_NC12», «STANDARD_NC12», «STANDARD_NC12», «STANDARD_NC12», «STANDARD_NC12», «STANDARD_NC12», «STANDARD_NC12», «STANDARD_NC12», «STANDARD_NC12», «STANDARD_NC12», «STANDARD_NC12», «STANDARD_NC12», «STANDARD_NC12», «STANDARD_NC12», «STANDARD_NC12», «STANDARD_NC12», «STANDARD_NC12», «STANDARD_NC12», «STANDARD_NC12», «STANDARD_NC12», «STANDARD_NC12», «STANDARD_NC12», «STANDARD_NC12», «STANDARD_NC12», «STANDARD_NC12», «STANDARD_NC12», «STANDARD_NC12», «STANDARD_NC12», «STANDARD_NC12», «STANDARD_NC12», «STANDARD_NC12», «STANDARD_NC12», «STANDARD_NC12», «STANDARD_NC12», «STANDARD_NC12», «STANDARD_NC12», «STANDARD_NC12», «STANDARD_NC12», «STANDARD_NC12», «STANDARD_NC12», «STANDARD_NC12», «STANDARD_NC12», «STANDARD_NC12», «STANDARD_NC12», «STANDARD_NC12», «STANDARD_NC12», «STANDARD_NC12», «STANDARD_NC12», «STANDARD_NC12», «STANDARD_NC12», «STANDARD_NC12», «STANDARD_NC12», «STANDARD_NC12», «STANDARD_NC12», «STANDARD_NC12», «STANDARD_NC12», «STANDARD_NC12», «STANDARD_NC12», «STANDARD_NC12», «STANDARD_NC12», «STANDARD_NC12», «STANDARD_NC12 ,STANDARD_H8,, "STANDARD_H8m", "STANDARD_H16", "STANDARD_H16m", "STANDARD_H16mr", "STANDARD_H16r", "STANDARD_A1_V2", "STANDARD_A2_V2", "STANDARD_A4_V2", "STANDARD_A8_V2", "STANDARD_A2m_V2", "STANDARD_A4m_V2", "STANDARD_A8m_V2", "STANDARD_M64ms", "STANDARD_M128s", "STANDARD_D2_V3". **По умолчанию является "standard_d2_v2"**  |
|    DockerDetails - azureBatchVMDetails - посвященныйComputerNodes   |  Нет. выделенных компьютерных узлов для пакетного пула. Значение по умолчанию — 1. |
|    DockerDetails - azureBatchVMDetails - nodeAgentSKUID          |    Идентификатор SKU агента узла пакетов Azure. В настоящее время поддерживается только агент пакетного узла "batch.node.ubuntu 18.04".    |
| DockerDetails - partnerCredentials | учетные данные для вызова API-партнера в docker. Партнер должен предоставить эту информацию своим клиентам на основе auth механизм, который поддерживается, например. Имя пользователя/пароль или клавиши API. |
| partnerType | "Погода" (Другие типы партнеров в FarmBeats являются "Sensor" и "Imagery")  |
|  name   |   Пожеланное имя партнера в системе FarmBeats   |
|  description |  Описание   |

## <a name="next-steps"></a>Дальнейшие действия

Теперь вы запросили данные датчиков из экземпляра Azure FarmBeats. Теперь узнайте, как [создавать карты](generate-maps-in-azure-farmbeats.md#generate-maps) для ваших ферм.
