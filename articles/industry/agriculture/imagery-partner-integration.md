---
title: Интеграция с участниками образа
description: Описание интеграции с участниками образа
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: c76fd151bf70bfff6eed3b45a673f94777e59467
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/07/2019
ms.locfileid: "73797911"
---
# <a name="imagery-partner-integration"></a>Интеграция с участниками образа

В этой статье описывается, как использовать компонент Azure Фармбеатс Translator для отправки данных с изображениями в Фармбеатс. Сельскохозяйственноеные данные могут быть из различных источников, включая камеры мултиспектрал, спутники и дроны. Партнеры по образам сельскохозяйственное могут интегрироваться с Фармбеатс, чтобы предоставить клиентам созданные пользователем карты для своих ферм.

Данные, будучи доступными, могут быть отображены с помощью ускорителя Фармбеатс и могут использоваться для создания данных и (Машинное обучение/искусственного интеллекта), здания или моделирования машинного обучения сельскохозяйственное предприятия или системных интеграторов.

Фармбеатс предоставляет следующие возможности:

- Определение пользовательских типов изображений, источника, формата файлов с помощью API-интерфейсов расширенного типа
- Прием данных с изображениями из различных источников через сцену & API Сценефиле.

Приведенные ниже сведения посвящены получению любой формы изображений в систему Фармбеатс.

При выборе раздела изображения помощью Дронов открывается всплывающее окно, в котором отображается изображение помощью Дронов орсомосаик с высоким разрешением. Вы можете получить доступ к программному обеспечению Partner, которое помогает планировать помощью дроновные авиарейсы и получать необработанные данные. Вы по-прежнему будете использовать программное обеспечение партнера для планирования пути и сшивания изображений орсомосаик.

Партнеры помощью Дронов должны включить клиентов, чтобы связать их учетную запись клиента с их экземпляром Фармбеатс в Azure.

Для связывания Фармбеатс необходимо использовать следующие учетные данные в программном обеспечении для партнеров помощью Дронов:

- Конечная точка API
- Tenant ID
- Идентификатор клиента
- Секрет клиента

## <a name="api-development"></a>Разработка API

API-интерфейсы содержат техническую документацию по Swagger. Изучите сведения об API и соответствующих запросах и ответах в [Swagger](https://aka.ms/FarmBeatsDatahubSwagger) .

## <a name="authentication"></a>Аутентификация

Фармбеатс использует [Active Directory]((https://docs.microsoft.com/azure/app-service/overview-authentication-authorization))Microsoft Azure. Служба приложений Azure для проверки подлинности предоставляет встроенную поддержку проверки подлинности и авторизации. 

Дополнительные сведения о см. в разделе [Azure Active Directory](https://docs.microsoft.com/azure/app-service/overview-authentication-authorization).   

Фармбеатс Data Hub использует проверку подлинности носителя, для которой требуются следующие учетные данные:

- Идентификатор клиента
- Секрет клиента
- Tenant ID

Используя указанные выше учетные данные, вызывающий объект может запросить маркер доступа, который должен быть отправлен в последующих запросах API, в разделе заголовка следующим образом:

```
headers = {"Authorization": "Bearer " + access_token, …} 
```

Ниже приведен пример кода Python, который извлекает маркер доступа. Затем можно использовать маркер для последующих вызовов API к Фармбеат:   
Импорт Azure 

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

## <a name="http-request-headers"></a>Заголовки HTTP-запросов

Ниже приведены наиболее распространенные заголовки запросов, которые необходимо указать при вызове API в Фармбеатс Data Hub:

**Верхняя часть** | **Описание и пример**
--- | ---
Content-Type  | Формат запроса (Content-Type: Application/<format>) для формата API концентратора данных Фармбеатс — JSON. Тип содержимого: Application/JSON
Авторизация | Указывает маркер доступа, необходимый для выполнения вызова API. Авторизация: носитель < доступ — токен >
Принять  | Формат ответа. Для интерфейсов API концентратора данных Фармбеатс форматом приема JSON является Application/JSON.


## <a name="api-requests"></a>Запросы API

Чтобы выполнить запрос REST API, необходимо объединить метод HTTP (GET/POST/ПОМЕЩАЕМ), URL-адрес службы API, URI ресурса (для запроса, отправки данных, обновления или удаления) и один или несколько заголовков HTTP-запроса.

При необходимости можно включить параметры запроса на вызов GET для фильтрации, ограничить размер и отсортировать данные в ответах.

Ниже приведен пример запроса для получения списка устройств:

```
curl -X GET "https://microsoft-farmbeats.azurewebsites.net/Device" -H
"Content-Type: application/json" -H
"Authorization: Bearer <Access-Token>”
```

Для большинства вызовов GET, POST и постановки требуется текст запроса JSON.

Приведенный ниже пример запроса заключается в создании устройства (у него есть входные данные JSON с текстом запроса).


```json
curl -X POST "https://microsoft-farmbeats.azurewebsites.net/Device" -H  
"accept: application/json" -H  
"Content-Type: application/json" -H "Authorization: Bearer <Access-Token>" -d
"{  \"deviceModelId\": \"ID123\",  \"hardwareId\": \"MHDN123\",  \"reportingInterval\": 900,  
\"name\": \"Device123\",  \"description\": \"Test Device 123\",}"
```

## <a name="data-format"></a>Формат данных

JSON (нотация объектов JavaScript) — это распространенный, не зависящий от языка формат данных, предоставляющий простое текстовое представление произвольных структур данных. Дополнительные сведения см. в разделе [JSON org](https://JSON.org).

## <a name="ingesting-imagery-into-farmbeats"></a>Прием изображений в Фармбеатс

После того как у партнера есть учетные данные для подключения к концентратору данных Фармбеатс, участник выполняет следующие действия в компоненте Translator:

1.  Создайте новый расширенный тип для следующих полей в соответствии с типом изображений, которые будут переданы:

    - Источник сцены: например, < drone_partner_name >
    - Тип сцены: например, <drone>
    - Тип файла сцены: например, <chlorophyll index>
    - Тип содержимого файла сцены: например, < Image/TIFF >

2.  Вызовите API ферм, чтобы получить список ферм из системы Azure Фармбеатс.
3.  Предоставьте клиенту возможность выбора отдельной фермы из списка ферм.

    Партнерская система должна показывать ферму в рамках программного обеспечения партнера, чтобы выполнить планирование пути и помощью Дронов рейса и коллекцию образов.

4.  Вызовите API сцены и укажите необходимые сведения для создания новой сцены с уникальным Сценеид.
5.  Получите URL-адрес SAS для большого двоичного объекта, чтобы передать необходимые образы в центр данных Фармбеатс в контексте выбранной фермы в систему Фармбеатс.

Ниже приведена подробная последовательность вызовов API:

### <a name="step-1-extendedtype"></a>Шаг 1. ExtendedType

Проверьте API ExtendedType, если тип и источник файла доступны в Фармбеатс. Это можно сделать, вызвав GET в API/Екстендедтипе.

Ниже приведены системные значения, определяемые системой.

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
Это будет однократная настройка, и область этого нового сценетипе будет ограничена подпиской, в которой развернут проект Фармбеатс.

Пример. чтобы добавить Сценесаурце: "Слантранже", вы помещаете идентификатор/Екстендедтипе с ключом: "Сценесаурце" входными данными:

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

Зеленое поле — это новое добавление к системным исходным значениям сцены.

### <a name="step-2-get-farmdetails"></a>Шаг 2. получение Фармдетаилс

Сцены (TIFF-или CSV-файлы) будут находиться в контексте фермы. Необходимо получить сведения о ферме, выполнив команду Get On/Фарм API. API возвратит список ферм, доступных в Фармбеатс, и вы можете выбрать ферму, для которой нужно получить данные.

Получить ответ/Фарм:

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

### <a name="step-3-create-ascene-id-post-call"></a>Шаг 3. Создание идентификатора или сцены (вызов Post)

Создайте новую сцену (TIFF или CSV-файл) с заданными данными, указав дату, последовательность и идентификатор фермы, с которыми будет связана сцена. Метаданные, связанные с сценой, могут быть определены в свойствах, включая длительность и тип меры.

При этом создается новый Сценеид, который будет связан с фермой. После создания Сценеид пользователь может использовать то же самое для создания нового файла (TIFF или CSV) & сохранения содержимого файла.

Пример входных полезных данных для API-интерфейса POST/сцены

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

**Create/Сценефиле**

Идентификатор сцены, возвращаемый на шаге 3, является входными данными для Сценефиле. Сценефиле возвращает маркер URL-адреса SAS, который действителен в течение 24 часов.

Если пользователю требуется программный способ отправки потока изображений, можно использовать пакет SDK хранилища BLOB-объектов для определения метода с помощью идентификатора Сценефиле, Location & URL.

Пример входных полезных данных для вызова POST в API/Сценефиле:

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

При вызове метода POST в API/Сценефиле возвращается URL-адрес отправки SAS, который можно использовать для отправки файла CSV или TIFF с помощью клиента или библиотеки хранилища BLOB-объектов Azure.


## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения об интеграции на основе API-интерфейса RESTful см. в разделе [REST API](references-for-farmbeats.md#rest-api).
