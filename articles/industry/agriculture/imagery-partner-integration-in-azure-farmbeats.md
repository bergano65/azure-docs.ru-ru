---
title: Интеграция с партнерскими решениями для получения снимков
description: В этой статье описывается интеграция партнеров изображений.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 62e5b363f8008380a61e24c0549573a30ecaeb73
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77131855"
---
# <a name="imagery-partner-integration"></a>Интеграция с партнерскими решениями для получения снимков

В этой статье описывается, как использовать компонент переводчика Azure FarmBeats для отправки данных изображений в FarmBeats. Данные сельскохозяйственных изображений могут быть получены из различных источников, таких как многоспектральные камеры, спутники и беспилотные летательные аппараты. Партнеры по сельскохозяйственным изображениям могут интегрироваться с FarmBeats, предоставляя клиентам пользовательские карты для своих ферм.

Данные, по месту использования, могут быть визуализированы через accelerator FarmBeats и потенциально использоваться для моделирования синтеза данных и машинного обучения/искусственного интеллекта (ML/AI) сельскохозяйственными предприятиями или интеграторами систем клиентов.

FarmBeats обеспечивает возможность:

- Определите пользовательские типы изображений, источник и формат файлов с помощью AI-файлов расширенного типа.
- Ingest данные изображений из различных источников через /Scene и /SceneFile AIS.

Следующая информация фокусируется на получении любой формы изображений в системе FarmBeats.

При выборе раздела **Drone Imagery** открывается всплывающее окно, чтобы показать изображение ортомонизаии с высоким разрешением. Вы можете получить доступ к партнеру программное обеспечение, которое помогает планировать полеты беспилотных летательных аппаратов и получать необработанные данные. Вы будете продолжать использовать программное обеспечение партнера для планирования путей и ортомозаики изображения сшивания.

Партнерам по дронам необходимо предоставить клиентам возможность связать свою учетную запись с экземпляром FarmBeats в Azure.

Вы должны использовать следующие учетные данные в программном обеспечении партнера дрона, чтобы связать FarmBeats:

- Конечная точка API
- Tenant ID
- Идентификатор клиента
- Секрет клиента

## <a name="api-development"></a>Разработка API

AIS содержат техническую документацию Swagger. Для получения информации об AIS и соответствующих запросах или ответах [см.](https://aka.ms/FarmBeatsDatahubSwagger)

## <a name="authentication"></a>Проверка подлинности

FarmBeats использует [активный каталог](https://docs.microsoft.com/azure/app-service/overview-authentication-authorization) Microsoft Azure (Azure AD). Служба приложений Azure предоставляет встроенную поддержку аутентификации и авторизации. 

Дополнительные сведения об Azure AD см. в [этой статье](https://docs.microsoft.com/azure/app-service/overview-authentication-authorization).   

FarmBeats Datahub использует проверку подлинности носителя, которая требует следующих учетных данных:

- Идентификатор клиента
- Секрет клиента
- Tenant ID

Используя предыдущие учетные данные, вызываец может запросить токен доступа, который должен быть отправлен в последующих запросах API, в разделе заголовка, следующим образом:

```
headers = {"Authorization": "Bearer " + access_token, …} 
```

Следующий образец кода Python получает токен доступа. Затем можно использовать токен для последующих вызовов API в FarmBeats.

```python
from azure.common.credentials import ServicePrincipalCredentials 
import adal 
#FarmBeats API Endpoint 
ENDPOINT = "https://<yourdatahub>.azurewebsites.net"   
CLIENT_ID = "<Your Client ID>"   
CLIENT_SECRET = "<Your Client Secret>"   
TENANT_ID = "<Your Tenant ID>" 
AUTHORITY_HOST = 'https://login.microsoftonline.com' 
AUTHORITY = AUTHORITY_HOST + '/' + TENANT_ID 
#Authenticating with the credentials 
context = adal.AuthenticationContext(AUTHORITY) 
token_response = context.acquire_token_with_client_credentials(ENDPOINT, CLIENT_ID, CLIENT_SECRET) 
#Should get an access token here 
access_token = token_response.get('accessToken') 
```

## <a name="http-request-headers"></a>Заголовки запросов HTTP

Вот наиболее распространенные заголовки запросов, которые должны быть указаны при вызове API в FarmBeats Datahub.

**Заголовка** | **Описание и пример**
--- | ---
Content-Type  | Формат запроса (Content-Type:<format>application/ ). Для ApIs FarmBeats Datahub формат JSON. Content-Type: application/json
Авторизация | Опознавательный маркер доступа, необходимый для вызова API. Авторизация:> <доступа
Принять  | Формат ответа. Для ApIs FarmBeats Datahub формат JSON. Принять: приложение/json


## <a name="api-requests"></a>Запросы API

Чтобы сделать запрос REST API, вы объединяете:

- Метод HTTP (GET, POST и PUT).
- URL-адрес службы API.
- Ресурс URI (для запроса, отправки данных, обновления или удаления).
- Один или несколько заголовков запросов HTTP.

Дополнительно можно включить параметры запроса в вызовы GET для фильтрации, ограничения размера и сортировки данных в ответах.

Следующий запрос образца, чтобы получить список устройств:

```bash
curl -X GET "https://microsoft-farmbeats.azurewebsites.net/Device" -H
"Content-Type: application/json" -H
"Authorization: Bearer <Access-Token>”
```

Большинство вызовов GET, POST и PUT требуют органа запроса JSON.

Следующий запрос образца заключается в создании устройства. Этот образец имеет входный JSON с органом запроса.


```bash
curl -X POST "https://microsoft-farmbeats.azurewebsites.net/Device" -H  
"accept: application/json" -H  
"Content-Type: application/json" -H "Authorization: Bearer <Access-Token>" -d
"{  \"deviceModelId\": \"ID123\",  \"hardwareId\": \"MHDN123\",  \"reportingInterval\": 900,  
\"name\": \"Device123\",  \"description\": \"Test Device 123\",}"
```

## <a name="data-format"></a>Формат данных

JSON является общим форматом данных, независимым от языка, который обеспечивает простое текстовое представление произвольных структур данных. Для получения дополнительной информации [см.](https://JSON.org)

## <a name="ingest-imagery-into-farmbeats"></a>Проглочение изображения в FarmBeats

После того, как партнер имеет учетные данные для подключения к FarmBeats Datahub, партнер делает следующие шаги в компоненте Переводчика.

1.  Создайте новый расширенный тип для следующих полей в соответствии с типом изображений, которые будут загружены:

    - **Источник сцены**: Например, drone_partner_name
    - **Тип сцены**: Например, дрон
    - **Тип файла сцены**: Например, индекс хлорофилла
    - **Тип содержимого файла сцены**: Например, изображение/тифф

2.  Позвоните в API /Farms, чтобы получить список ферм в системе Azure FarmBeats.
3.  Предоставьте клиенту возможность выбрать одну ферму из списка ферм.

    Партнерская система должна показать ферме в партнерском программном обеспечении, чтобы сделать планирование пути и беспилотный полет и сбор изображений.

4.  Позвоните в /Scene API и предоставьте необходимые детали для создания новой сцены с уникальным идентификатором сцены.
5.  Получите URL-адрес blob SAS для загрузки необходимых изображений в FarmBeats Datahub в контексте выбранной фермы в системе FarmBeats.

Вот подробный поток на вызовах API.

### <a name="step-1-extendedtype"></a>Шаг 1: РасширенныйТип

Проверьте aPI /ExtendedType, чтобы узнать, доступны ли тип и источник файлов на FarmBeats. Для этого позвоните в GET на API /ExtendedType.

Вот системные значения:

```json
{
  "items": [
    {
      "id": "ae2aa527-7d27-42e2-8fcf-841937c651d7",
      "createdAt": "2019-06-17T07:32:30.1135716Z",
      "lastModifiedAt": "2019-10-25T09:47:42.5910344Z",
      "key": "SceneFileType",
      "value": [
        "evi",
        "ndvi",
        "ndwi",
        "tci",
        "soil-moisture",
        "sensor-placement",
        "sentinel-b01",
        "sentinel-b02",
        "sentinel-b03",
        "sentinel-b04",
        "sentinel-b05",
        "sentinel-b06",
        "sentinel-b07",
        "sentinel-b08",
        "sentinel-b8a",
        "sentinel-b09",
        "sentinel-b10",
        "sentinel-b11",
        "sentinel-b12",
        "cloud-mask",
        "farm-mask",
        "image/tiff",
        "orthomosaic"
      ],
      "description": "List of scene file types available in system. User can add more values."
    },
    {
      "id": "b7824d2e-3e43-46d3-8fd4-ec7ba8918d84",
      "createdAt": "2019-06-17T07:32:30.1135716Z",
      "lastModifiedAt": "2019-10-25T09:45:44.8914691Z",
      "key": "SceneFileContentType",
      "value": [
        "image/tiff",
        "image/png",
        "image/jpeg",
        "text/csv",
        "text/plain",
        "text/tab-separated-values",
        "application/json",
        "application/octet-stream",
        "orthomosaic"
      ],
      "description": "List of scene file content types available in system. User can add more values."
    },
    {
      "id": "21d2924d-8b3e-4ce4-a7f8-318e85c0f7f2",
      "createdAt": "2019-06-17T07:32:30.1135716Z",
      "lastModifiedAt": "2019-10-25T09:45:37.5096445Z",
      "key": "SceneSource",
      "value": [
        "sentinel-l1c",
        "sentinel-l2a",
        "farmbeats-model",
        "dji",
        "SlantRange-3P",
        "DJI"
      ],
      "description": "List of scene sources available in system. User can add more values."
    },
    {
      "id": "6e09ca69-cacf-4b53-b63a-53c659aae4a4",
      "createdAt": "2019-06-17T07:32:30.1135716Z",
      "lastModifiedAt": "2019-10-25T09:45:34.7483899Z",
      "key": "SceneType",
      "value": [
        "base-bands",
        "sensor-placement",
        "soil-moisture",
        "evi",
        "ndwi",
        "ndvi",
        "drone",
        "orthomosaic-drone"
      ],
      "description": "List of scene types available in system. User can add more values."
    },
    {
      "id": "55d8436b-3a86-4bea-87ab-e1b51226525f",
      "createdAt": "2019-06-17T07:32:30.1135716Z",
      "lastModifiedAt": "2019-06-17T07:32:30.1135716Z",
      "key": "SensorMeasureType",
      "value": [
        "AmbientTemperature",
        "CO2",
        "Depth",
        "ElectricalConductivity",
        "LeafWetness",
        "Length",
        "LiquidLevel",
        "Nitrate",
        "O2",
        "PH",
        "Phosphate",
        "PointInTime",
        "Potassium",
        "Pressure",
        "RainGauge",
        "RelativeHumidity",
        "Salinity",
        "SoilMoisture",
        "SoilTemperature",
        "SolarRadiation",
        "State",
        "TimeDuration",
        "UVRadiation",
        "UVIndex",
        "Volume",
        "WindDirection",
        "WindRun",
        "WindSpeed",
        "Evapotranspiration",
        "PAR"
      ],
      "description": "List of sensor measure types available in system. User can add more values."
    },
    {
      "id": "0dfd6e6b-df58-428f-9ab8-a0674bfdcbe5",
      "createdAt": "2019-06-17T07:32:30.1135716Z",
      "lastModifiedAt": "2019-06-17T07:32:30.1135716Z",
      "key": "SensorMeasureUnit",
      "value": [
        "NoUnit",
        "Celsius",
        "Fahrenheit",
        "Kelvin",
        "Rankine",
        "Pascal",
        "Mercury",
        "PSI",
        "MilliMeter",
        "CentiMeter",
        "Meter",
        "Inch",
        "Feet",
        "Mile",
        "KiloMeter",
        "MilesPerHour",
        "MilesPerSecond",
        "KMPerHour",
        "KMPerSecond",
        "MetersPerHour",
        "MetersPerSecond",
        "Degree",
        "WattsPerSquareMeter",
        "KiloWattsPerSquareMeter",
        "MilliWattsPerSquareCentiMeter",
        "MilliJoulesPerSquareCentiMeter",
        "VolumetricWaterContent",
        "Percentage",
        "PartsPerMillion",
        "MicroMol",
        "MicroMolesPerLiter",
        "SiemensPerSquareMeterPerMole",
        "MilliSiemensPerCentiMeter",
        "Centibar",
        "DeciSiemensPerMeter",
        "KiloPascal",
        "VolumetricIonContent",
        "Liter",
        "MilliLiter",
        "Seconds",
        "UnixTimestamp",
        "MicroMolPerMeterSquaredPerSecond",
        "InchesPerHour"
      ],
      "description": "List of sensor measure units available in system. User can add more values."
    }
  ]
}
```

Этот шаг является одноразовой установкой. Область действия нового типа сцены ограничена подпиской, в которой установлены Azure FarmBeats.

Например, чтобы добавить SceneSource: "SlantRange", вы делаете PUT на Идентификаторе /ExtendedType API с ключевой полезной нагрузкой ввода "SceneSource".

```json
{

      "key": "SceneSource",
      "value": [
        "sentinel-l1c",
        "sentinel-l2a",
        "farmbeats-model",
        "dji",
        "SlantRange-3P"
      ],
      "description": "List of scene sources available in system. User can add more values."
}

```

Зеленое поле является новым дополнением к системным значениям источника исходной сцены.

### <a name="step-2-get-farm-details"></a>Шаг 2: Получить сведения о ферме

Сцены (.tiff или .csv файлы) находятся в контексте фермы. Вы должны получить сведения о ферме, делая GET на /Farm API. API возвращает список ферм, доступных в FarmBeats. Вы можете выбрать ферму, для которых вы хотите глотать данные.

GET /Farm ответ:

```json
{
  "id": "07f3e09c-89aa-4619-8d50-e57fb083d8f9",
  "createdAt": "2019-11-01T13:55:41.8804663Z",
  "lastModifiedAt": "2019-11-01T13:55:41.8804663Z",
  "geometry": {
    "type": "Polygon",
    "coordinates": [
      [
        [
          78.34252251428694,
          17.402556352862675
        ],
        [
          78.34278000635095,
          17.407920852463022
        ],
        [
          78.34883106989963,
          17.40878079576528
        ],
        [
          78.3498181228054,
          17.403539173730607
        ],
        [
          78.34805859369493,
          17.39977166504127
        ],
        [
          78.34252251428694,
          17.402556352862675
        ]
      ]
    ]
  },
  "name": "Samplefarm",
  "properties": {
    "crops": "Corn",
    "address": "Sample street"
  }
}
 ```

### <a name="step-3-create-a-scene-id-post-call"></a>Шаг 3: Создание идентификатора сцены (POST вызова)

Создайте новую сцену (.tiff или файл .csv) с данной информацией, которая предоставляет дату, последовательность и идентификатор фермы, с которым связана сцена. Метаданные, связанные со сценой, могут быть определены по свойствам, которые включают продолжительность и тип измерения.

Создание новой сцены создает новый идентификатор сцены, который связан с фермой. После создания идентификатора сцены пользователь может использовать то же самое для создания нового файла (.tiff или .csv) и хранения содержимого файла.

Пример полезной нагрузки ввода для вызова POST на API /Scene:

```json
{
  "sceneId": "a0505928-c480-491b-ba31-d38285a28c1d",
  "type": "newtype",
  "contentType": "image/tiff",
  "name": "test scene file",
  "description": "test scene file description",
  "properties": {
    "additionalProp1": {},
    "additionalProp2": {},
    "additionalProp3": {}
  }
}
```

Ответ API:

```json
{
  "id": "a0505928-c480-491b-ba31-d38285a28c1d",
  "createdAt": "2019-10-04T16:19:12.4838584Z",
  "lastModifiedAt": "2019-10-04T16:19:12.4838584Z",
  "type": "new type",
  "source": "SlantRange-3P",
  "farmId": "d41a33e7-b73e-480e-9279-0fcb3207332b",
  "date": "2019-10-04T16:13:39.064Z",
  "sequence": 5,
  "name": "test scene",
  "description": "test scene description",
  "properties": {}
}

```

**Создание файла сцены**

Идентификатор сцены, возвращенный в шаге 3, является входной для файла сцены. Файл сцены возвращает токен SAS URL, который действителен в течение 24 часов.

Если пользователю требуется программный способ загрузки потока изображений, SDK для хранения кабелей может быть использован для определения метода с помощью идентификатора файла сцены, местоположения и URL-адреса.

Пример полезной нагрузки ввода для вызова POST на API /SceneFile:

```json
{
  "sceneId": "a0505928-c480-491b-ba31-d38285a28c1d",
  "type": "newtype",
  "contentType": "image/tiff",
  "name": "test scene file",
  "description": "test scene file description",
  "properties": {
    "additionalProp1": {},
    "additionalProp2": {},
    "additionalProp3": {}
  }
}
```
Ответ API:

```json
{
  "uploadSASUrl": "https://storagej2lho.blob.core.windows.net/farm-scene/2019/a0505928-c480-491b-ba31-d38285a28c1d/e91139a7-4ebd-4e2f-b17c-c677822dc840?sv=2018-03-28&sr=b&sig=%2F1426JkDcIFE5g3d%2BjOevCVMIn%2FJo9YKwBn3La5zL8Y%3D&se=2019-10-05T16%3A23%3A57Z&sp=w",
  "id": "e91139a7-4ebd-4e2f-b17c-c677822dc840",
  "createdAt": "2019-10-04T16:23:57.1192916Z",
  "lastModifiedAt": "2019-10-04T16:23:57.1192916Z",
  "blobUrl": "https://storagej2lho.blob.core.windows.net/farm-scene/2019/a0505928-c480-491b-ba31-d38285a28c1d/e91139a7-4ebd-4e2f-b17c-c677822dc840",
  "sceneId": "a0505928-c480-491b-ba31-d38285a28c1d",
  "type": "newtype",
  "contentType": "image/tiff",
  "name": "test scene file",
  "description": "test scene file description",
  "properties": {}
}


```

Вызов POST на /SceneFile API возвращает URL-адрес загрузки SAS, который можно использовать для загрузки файла .csv или .tiff с помощью клиента или библиотеки хранения Azure Blob.


## <a name="next-steps"></a>Дальнейшие действия

Для получения дополнительной информации о деталях [REST API](rest-api-in-azure-farmbeats.md)интеграции на основе REST API см.
