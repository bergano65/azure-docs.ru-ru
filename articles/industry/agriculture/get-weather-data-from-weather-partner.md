---
title: Получение данных о погоде от партнеров по погоде
description: В этой статье описывается получение данных о погоде от партнеров.
author: sunasing
ms.topic: article
ms.date: 03/31/2020
ms.author: sunasing
ms.openlocfilehash: 7666ee1a81c2ed93ee5e246b3ec79f056f9d63ab
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/09/2020
ms.locfileid: "86187784"
---
# <a name="get-weather-data-from-weather-partners"></a>Получение данных о погоде от партнеров по погоде

Azure Фармбеатс поможет вам перенести данные о погоде из ваших поставщиков данных погоды с помощью инфраструктуры соединителя на основе DOCKER. С помощью этой платформы поставщики данных Weather реализуют DOCKER, который можно интегрировать с Фармбеатс. В настоящее время поддерживаются следующие поставщики данных погоды:

  ![дтн](./media/get-sensor-data-from-sensor-partner/dtn-logo.png)

  [дтн](https://www.dtn.com/dtn-content-integration/)

Данные погоды можно использовать для создания ценных сведений и создания моделей AI/ML на Фармбеатс.

## <a name="before-you-start"></a>Перед началом работы

Чтобы получить данные о погоде, убедитесь, что установлен Фармбеатс. **Интеграция с погодой поддерживается в версии 1.2.11 или более поздней**. Чтобы установить Azure Фармбеатс, см. раздел [Install фармбеатс](https://aka.ms/farmbeatsinstalldocumentation).

## <a name="enable-weather-integration-with-farmbeats"></a>Включение интеграции погоды с Фармбеатс

Чтобы начать получать данные о погоде в центре данных Фармбеатс, выполните следующие действия:

1. Перейдите в центр данных Фармбеатс Swagger (https://farmbeatswebsite-api.azurewebsites.net/swagger)

2. Перейдите к API/партнер и выполните запрос POST со следующими входными полезными данными:

   ```json
   {  
 
     "dockerDetails": {  
       "credentials": { 
         "username": "<credentials to access private docker - not required for public docker>", 
         "password": "<credentials to access private docker – not required for public docker>"  
       },  
       "imageName" : "<docker image name",
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

   Например, чтобы получить данные о погоде из ДТН, используйте полезные данные ниже. Имя и описание можно изменить в соответствии с вашими предпочтениями.

   > [!NOTE]
   > Для приведенного ниже шага требуется ключ API. обратитесь в ДТН, чтобы получить такой же адрес для подписки ДТН.

   ```json
   {
 
     "dockerDetails": {
       "imageName": "dtnweather/dtn-farm-beats",
       "imageTag": "latest",
       "azureBatchVMDetails": {
         "batchVMSKU": "standard_d2_v2",
         "dedicatedComputerNodes": 1,
         "nodeAgentSKUID": "batch.node.ubuntu 18.04"
       }
     },
     "partnerCredentials": {
      "apikey": "<API key from DTN>"
      },
     "partnerType": "Weather",
     "name": "dtn-weather",
     "description": "DTN registered as a Weather Partner in FarmBeats"
   }  
   ```

   > [!NOTE]
   > Дополнительные сведения об объекте Partner см. в [приложении](get-weather-data-from-weather-partner.md#appendix)

   На предыдущем шаге будут подготавливаться ресурсы, позволяющие запускать DOCKER в среде Фармбеатс клиента.  

   На предоставление указанных выше ресурсов занимает около 10-15 минут.

3. Проверьте состояние объекта/партнер, созданного на шаге 2. Для этого выполните запрос GET в API/партнер и проверьте **состояние** объекта Partner. После того как Фармбеатс успешно подготавливает партнера, ему будет присвоено состояние **активно**.

4. Перейдите к API/Жобтипе и выполните запрос GET. Проверьте наличие заданий погоды, созданных в рамках процесса добавления партнеров на шаге 2. Поле **пипелиненаме** в заданиях Weather будет иметь следующий формат: "partner-name_partner-type_job-Name".

5. Теперь у экземпляра Фармбеатс есть активный партнер по погоде и вы можете запускать задания для запроса данных о погоде для определенного расположения (широты и долготы) и диапазона дат. JobType (s) будет иметь подробные сведения о параметрах, необходимых для выполнения заданий погоды.

   Например, для ДТН будут созданы следующие JobType:
   
   - get_dtn_daily_observations (получение ежедневных наблюдений за расположением и периодом времени)
   - get_dtn_daily_forecasts (получение ежедневных прогнозов по местоположению и периоду времени)
   - get_dtn_hourly_observations (получение ежечасных наблюдений за расположение и период времени)
   - get_dtn_hourly_forecasts (получение почасовых прогнозов по местоположению и периоду времени)

6. Запишите **идентификатор** и параметры JobType (s).

7. Перейдите к API вызовом/Jobs и выполните запрос POST для вызовом/Jobs со следующими входными полезными данными:

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

   Например, чтобы запустить **get_dtn_daily_observations**, используйте следующие полезные данные:

   ```json
   { 
         "typeId": "<id of the JobType>",
         "arguments": {
               "latitude": 47.620422,
               "longitude": -122.349358,
               "days": 5
         },
         "name": "<name of the job>",
         "description": "<description>",
         "properties": {}
   }
   ```

8. На предыдущем шаге будут выполнены задания Weather, определенные в партнерской DOCKER, и данные о погоде поступают в Фармбеатс. Чтобы проверить состояние задания, сделайте запрос GET на вызовом/Jobs и найдите в ответе **currentState** . По завершении для currentState устанавливается значение **успех**.

## <a name="query-ingested-weather-data"></a>Запрос полученных данных о погоде

После завершения заданий Weather можно запросить полученные данные о погоде, чтобы создать модели или практическую аналитику с помощью интерфейсов API Фармбеатс Датахуб.

### <a name="query-using-rest-api"></a>Запрос с помощью REST API

Чтобы запросить данные о погоде с помощью Фармбеатс REST API, выполните следующие действия:

1. В Фармбеатсе Swagger центра данных ( https://yourdatahub.azurewebsites.net/swagger) перейдите к API/веасердаталокатион и выполните запрос GET. Ответ будет иметь объекты/Веасердаталокатион, созданные для расположения (широты и долготы), которое было указано в ходе выполнения задания. Запишите **идентификатор** и **веасердатамоделид** для объектов.

2. Выполните GET/{ID} в API/Веасердатамодел для **веасердатамоделид** , как указано в шаге 1. "Модель данных Weather" содержит все метаданные и сведения о полученных данных о погоде. Например, **мера Weather** в объекте **модели данных Weather** содержит сведения о том, какие сведения о погоде поддерживаются и в каких типах и единицах. Например,

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

   Запишите ответ вызова GET/{ID} для модели данных weather.

3. Перейдите к API **телеметрии** и выполните запрос POST со следующими входными полезными данными:

   ```json
   {
       "weatherDataLocationId": "<id from step 1 above>",
     "searchSpan": {
       "from": "2020-XX-XXT07:30:00Z",
       "to": "2020-XX-XXT07:45:00Z"
     }
   }
   ```

4. Ответ, содержащий данные о погоде, доступные для указанного диапазона времени, будет выглядеть следующим образом:

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

В предыдущем примере ответ содержит данные для двух меток времени, а также имя меры ("температура") и значения сообщаемых данных о погоде в двух отметках времени. Чтобы интерпретировать тип и единицу сообщаемых значений, необходимо обратиться к связанной модели данных погоды (как описано на шаге 2 выше).

## <a name="troubleshoot-job-failures"></a>Устранение ошибок заданий

Чтобы устранить неполадки заданий, можно проверить журналы заданий. Выполните действия, [описанные](troubleshoot-azure-farmbeats.md#weather-data-job-failures) в этом разделе.


## <a name="appendix"></a>Приложение

|        Партнер   |  Сведения   |
| ------- | -------             |
|     Доккердетаилс — imageName         |          Имя образа DOCKER. Например, docker.io/mydockerimage (изображение в hub.docker.com) или myazureacr.azurecr.io/mydockerimage (образ в реестре контейнеров Azure) и т. д. Если реестр не указан, по умолчанию используется hub.docker.com      |
|          Доккердетаилс — Имажетаг             |         Имя тега образа DOCKER. Значение по умолчанию — "Последняя"     |
|  Доккердетаилс — учетные данные      |  Учетные данные для доступа к частному DOCKER. Это будет предоставлено партнером клиенту.   |
|  Доккердетаилс-Азуребатчвмдетаилс-Батчвмску     |    Номер SKU виртуальной машины пакетной службы Azure. Дополнительные сведения о всех доступных виртуальных машинах Linux см. [здесь](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) . Допустимые значения: "Small", "ExtraLarge", "крупный", "A8", "A9", "Medium", "A5", "A6", "A7", "STANDARD_D1", "STANDARD_D2", "STANDARD_D3", "STANDARD_D4 ', ' STANDARD_D11 ', ' STANDARD_D12 ', ' STANDARD_D13 ', ' STANDARD_D14 ', ' A10 ', ' A11 ', ' STANDARD_D1_V2 ', ' STANDARD_D2_V2 ', ' STANDARD_D3_V2 ', ' STANDARD_D4_V2 ', ' STANDARD_D11_V2 ', ' STANDARD_D12_V2 ', ' STANDARD_D13_V2 ', ' STANDARD_D14_V2 ', ' STANDARD_G1 ' , "STANDARD_G5", "STANDARD_D5_V2", "BASIC_A1", "BASIC_A2", "BASIC_A3", "BASIC_A4", "STANDARD_A1", "STANDARD_A2", "STANDARD_A3", "STANDARD_A4", "STANDARD_A5", "STANDARD_A6", "STANDARD_A7", "STANDARD_A8", "STANDARD_A9", "STANDARD_A10", "STANDARD_A11", "STANDARD_D15_V2", "STANDARD_F1", "STANDARD_F2", "STANDARD_F4", "STANDARD_F8", "STANDARD_F16", "STANDARD_NV6", "STANDARD_NV12", "STANDARD_NV24", "STANDARD_NC6" , "STANDARD_H8", "STANDARD_H8m", "STANDARD_H16", "STANDARD_H16m", "STANDARD_H16mr", "STANDARD_H16r", "STANDARD_A1_V2", "STANDARD_A2_V2", "STANDARD_A4_V2", "STANDARD_A8_V2", "STANDARD_A2m_V2", "STANDARD_A4m_V2", "STANDARD_A8m_V2", "STANDARD_M64ms", "STANDARD_M128s", "STANDARD_D2_V3". **Значение по умолчанию — "standard_d2_v2"**  |
|    Доккердетаилс-Азуребатчвмдетаилс-Дедикатедкомпутернодес   |  Нет. выделенных узлов компьютеров для пула пакетной службы. Значение по умолчанию — 1. |
|    Доккердетаилс-Азуребатчвмдетаилс-Нодеажентскуид          |    Идентификатор SKU агента узла пакетной службы Azure. Сейчас поддерживается только агент узла пакетной службы "пакет. Node. Ubuntu 18,04".    |
| Доккердетаилс — Партнеркредентиалс | учетные данные для вызова API-партнера в DOCKER. Партнер должен предоставить эти сведения своим клиентам на основе механизма проверки подлинности, поддерживаемого например. Имя пользователя, пароль или ключи API. |
| партнертипе | "Погода" (другие типы партнеров в Фармбеатс — "Sensor" и "Images")  |
|  name   |   Требуемое имя участника в Фармбеатс System   |
|  описание |  Описание   |

## <a name="next-steps"></a>Дальнейшие действия

Теперь вы запросили данные датчиков из экземпляра Azure Фармбеатс. Теперь Узнайте, как [создавать карты](generate-maps-in-azure-farmbeats.md#generate-maps) для ферм.
