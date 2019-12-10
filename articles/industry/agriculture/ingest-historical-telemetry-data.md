---
title: Прием архивных данных телеметрии
description: В этой статье описывается прием исторических данных телеметрии.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: a3e4f543be2f01e0c649d5f9bcc9287dedc275f1
ms.sourcegitcommit: b5ff5abd7a82eaf3a1df883c4247e11cdfe38c19
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/09/2019
ms.locfileid: "74941650"
---
# <a name="ingest-historical-telemetry-data"></a>Прием архивных данных телеметрии

В этой статье описывается прием исторических данных датчика в Azure Фармбеатс.

Получение исторических данных из ресурсов "Интернет вещей" (IoT), таких как устройства и датчики, является распространенным сценарием в Фармбеатс. Вы создаете метаданные для устройств и датчиков, а затем принимаете исторические данные в Фармбеатс в каноническом формате.

## <a name="before-you-begin"></a>Перед началом работы

Прежде чем продолжить работу с этой статьей, убедитесь, что вы установили Фармбеатс и собрали исторические данные из Интернета вещей.
Также необходимо включить Партнерский доступ, как упоминалось на следующих шагах.

## <a name="enable-partner-access"></a>Включить доступ партнеров

Необходимо включить интеграцию партнера с экземпляром Azure Фармбеатс. На этом шаге создается клиент, имеющий доступ к экземпляру Azure Фармбеатс в качестве партнера по устройствам, и предоставляются следующие значения, необходимые для последующих шагов:

- Конечная точка API. это URL-адрес Датахуб, например https://\<датахуб >. azurewebsites. NET.
- Tenant ID
- Идентификатор клиента
- Секрет клиента
- Строка подключения EventHub

Выполните следующие действия.

>[!NOTE]
> Чтобы выполнить следующие действия, необходимо быть администратором.

1. Скачайте этот [сценарий](https://aka.ms/farmbeatspartnerscript)и извлеките его на локальный диск. В ZIP-файле находятся два файла.
2. Войдите на [портал Azure](https://portal.azure.com/) и откройте Azure Cloud Shell. Этот параметр доступен на панели инструментов в правом верхнем углу портала.

    ![Панель инструментов портал Azure](./media/for-tutorials/navigation-bar-1.png)

3. Убедитесь, что для среды задано значение **PowerShell**.

    ![Параметр PowerShell](./media/for-tutorials/power-shell-new-1.png)

4. Отправьте два файла, скачанные из шага 1 в экземпляре Cloud Shell.

    ![Кнопка "Отправить" на панели инструментов](./media/for-tutorials/power-shell-two-1.png)

5. Перейдите в каталог, в который были отправлены файлы.

   >[!NOTE]
   > По умолчанию файлы передаются в корневой каталог, домашнюю страницу или имя пользователя.
6. Выполните сценарий с помощью следующей команды:

    ```azurepowershell-interactive
    ./generateCredentials.ps1
    ```

7. Следуйте инструкциям на экране, чтобы выполнить процедуру.

## <a name="create-device-or-sensor-metadata"></a>Создание метаданных устройства или датчика

 Теперь, когда у вас есть необходимые учетные данные, можно определить устройство и датчики. Для этого создайте метаданные с помощью API-интерфейсов Фармбеатс.

 Фармбеатс Датахуб содержит следующие API, позволяющие создавать метаданные устройства или датчика и управлять ими.

- /**DeviceModel**: DeviceModel соответствует метаданным устройства, например изготовителю, и типу устройства, которое является либо шлюзом, либо узлом.
- **устройство**/: устройство соответствует физическому устройству, присутствующему в ферме.
- /**сенсормодел**: сенсормодел соответствует метаданным датчика, например изготовителю, типу датчика, который является либо аналоговым, либо цифровым, и измерением датчика, например, окружающей температуре и нажимом.
- **датчик**/: Датчик соответствует физическому датчику, записывающему значения. Датчик обычно подключается к устройству с ИДЕНТИФИКАТОРом устройства.  


|        DeviceModel   |  Варианты поиска   |
| ------- | -------             |
|     Тип (узел, шлюз)        |          1 звезда      |
|          Производитель            |         2 звезды     |
|  ProductCode                    |  Код продукта устройства или имя или номер модели. Например, Енвиромонитор # 6800.  |
|            порты;          |     Имя и тип порта, которые являются цифровыми или аналоговыми.
|     Name                 |  Имя, идентифицирующее ресурс. Например, имя модели или название продукта.
      Описание     | Введите понятное описание модели.
|    properties          |    Дополнительные свойства от производителя.   |
|    **Устройство**             |                      |
|   девицемоделид     |     ИДЕНТИФИКАТОР связанной модели устройства.  |
|  хардвареид          | Уникальный идентификатор устройства, например MAC-адрес.
|  ReportingInterval        |   Интервал составления отчета в секундах.
|  Location            |  Широта устройства (от-90 до + 90), Долгота (от-180 до 180) и повышение уровня (в метрах).   
|парентдевицеид       |    Идентификатор родительского устройства, к которому подключено это устройство. Например, узел, подключенный к шлюзу. Узел имеет Парентдевицеид в качестве шлюза.  |
|    Name            | Имя, идентифицирующее ресурс. Партнеры устройств должны отправить имя, которое соответствует имени устройства на стороне партнера. Если имя устройства партнера определено пользователем, то такое же определяемое пользователем имя должно распространяться на Фармбеатс.|
|     Описание       |      Введите осмысленное описание. |
|     properties    |  Дополнительные свойства от производителя.
|     **сенсормодел**        |          |
|       Тип (аналоговый, цифровой)          |      Тип датчика, будь то аналоговый или цифровой.       |
|          Производитель            |       Производитель датчика.     |
|     ProductCode| Код продукта или имя или номер модели. Например, RS-CO2-N01. |
|       Имя > Сенсормеасурес       | Имя меры датчика. Поддерживается только строчные буквы. Для измерений с различной глубиной укажите глубину. Например, soil_moisture_15cm. Это имя должно соответствовать данным телеметрии.  |
|          Тип данных Сенсормеасурес >       |Тип данных телеметрии. В настоящее время поддерживается Double.|
|    Тип > Сенсормеасурес    |Тип измерения данных телеметрии датчика. Определяемые системой типы: Амбиенттемпературе, CO2, Depth, Електрикалкондуктивити, Леафветнесс, Length, Ликуидлевел, нитрате, O2, PH, фосфате, PointInTime, potassium, давление, RainGauge, RelativeHumidity, Salinity, SoilMoisture, Соилтемпературе, Соларрадиатион, State, Тимедуратион, Уврадиатион, Увиндекс, том, Винддиректион, Виндрун, WindSpeed, Evapotranspiration, номинал. Чтобы добавить дополнительные сведения, обратитесь к API/Екстендедтипе.|
|        Единица > Сенсормеасурес              | Единица данных телеметрии датчика. Определяемые системой единицы измерения: "ед., Цельсия, Фаренгейта, Кельвина, Ранкине, Pascal, Меркурий, PSI, миллиметр, сантиметры, метры, дюймы, футы, км, километр, Милесперхаур, Милесперсеконд, Кмперхаур, Кмперсеконд, Метерсперхаур, Метерсперсеконд, градусы WattsPerSquareMeter, KiloWattsPerSquareMeter, MilliWattsPerSquareCentiMeter, MilliJoulesPerSquareCentiMeter, VolumetricWaterContent, процент, PartsPerMillion, MicroMol, MicroMolesPerLiter, SiemensPerSquareMeterPerMole, Миллисиеменсперцентиметер, Центибар, ДеЦисиеменсперметер, Килопаскал, ВолуметриЦионконтент, Liter, MilliLiter, seconds, UnixTimestamp, MicroMolPerMeterSquaredPerSecond, InchesPerHour чтобы добавить дополнительные сведения, см. в API-интерфейсе/ExtendedType.|
|    Сенсормеасурес > AggregationType    |  Значения могут быть "None", "Average", "Maximum", "минимум" или "стандартное отклонение".  |
|          Name            | Имя, идентифицирующее ресурс. Например, имя модели или название продукта.  |
|    Описание        | Введите понятное описание модели.  |
|   properties       |  Дополнительные свойства от производителя.  |
|    **Датчика**      |          |
| хардвареид          |   Уникальный идентификатор для датчика, установленного производителем. |
|  сенсормоделид     |    ИДЕНТИФИКАТОР связанной модели датчика.   |
| Location          |  Датчик широты (от-90 до + 90), Долгота (от-180 до 180) и повышение уровня (в метрах).|
|   Имя > порта        |  Имя и тип порта, к которому подключен датчик на устройстве. Это имя должно совпадать с именем, определенным в модели устройства. |
|    DeviceID  |    ИДЕНТИФИКАТОР устройства, к которому подключен датчик.     |
| Name            |   Имя, идентифицирующее ресурс. Например, имя датчика или название продукта, номер модели или код продукта.|
|    Описание      | Введите осмысленное описание. |
|    properties        |Дополнительные свойства от производителя. |

Дополнительные сведения об объектах см. в разделе [Swagger](https://aka.ms/FarmBeatsDatahubSwagger).

### <a name="api-request-to-create-metadata"></a>Запрос API для создания метаданных

Чтобы выполнить запрос API, необходимо объединить метод HTTP (POST), URL-адрес службы API и URI для запроса, отправки данных в запрос, создания или удаления запроса. Затем добавьте один или несколько заголовков HTTP-запроса. URL-адрес службы API — это конечная точка API, то есть URL-адрес Датахуб (https://\<йоурдатахуб >. azurewebsites. NET).  

### <a name="authentication"></a>Authentication

Фармбеатс Датахуб использует проверку подлинности носителя, для которой требуются следующие учетные данные, созданные в предыдущем разделе.

- Идентификатор клиента
- Секрет клиента
- Tenant ID

Используя эти учетные данные, вызывающий объект может запросить маркер доступа. Маркер должен быть отправлен в последующих запросах API в разделе заголовка следующим образом:

```
headers = *{"Authorization": "Bearer " + access_token, …}*
```

В следующем примере кода Python предоставляется маркер доступа, который можно использовать для последующих вызовов API к Фармбеатс: 

```python
import azure 

from azure.common.credentials import ServicePrincipalCredentials 
import adal 
#FarmBeats API Endpoint 
ENDPOINT = "https://<yourdatahub>.azurewebsites.net" [Azure website](https://<yourdatahub>.azurewebsites.net)
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


**Заголовки HTTP-запросов**

Ниже приведены наиболее распространенные заголовки запросов, которые необходимо указать при выполнении вызова API в Фармбеатс Датахуб:

- **Тип содержимого**: Application/JSON
- **Авторизация**: носитель < доступ — токен >
- **Принять**: приложение/JSON

### <a name="input-payload-to-create-metadata"></a>Входные полезные данные для создания метаданных

DeviceModel


```json
{
  "type": "Node",
  "manufacturer": "string",
  "productCode": "string",
  "ports": [
    {
      "name": "string",
      "type": "Analog"
    }
  ],
  "name": "string",
  "description": "string",
  "properties": {
    "additionalProp1": {},
    "additionalProp2": {},
    "additionalProp3": {}
  }
}
```

Устройство

```json
{
  "deviceModelId": "string",
  "hardwareId": "string",
  "farmId": "string",
  "reportingInterval": 0,
  "location": {
    "latitude": 0,
    "longitude": 0,
    "elevation": 0
  },
  "parentDeviceId": "string",
  "name": "string",
  "description": "string",
  "properties": {
    "additionalProp1": {},
    "additionalProp2": {},
    "additionalProp3": {}
  }
}
```

сенсормодел

```json
{
  "type": "Analog",
  "manufacturer": "string",
  "productCode": "string",
  "sensorMeasures": [
    {
      "name": "string",
      "dataType": "Double",
      "type": "string",
      "unit": "string",
      "aggregationType": "None",
      "depth": 0,
      "description": "string"
    }
  ],
  "name": "string",
  "description": "string",
  "properties": {
    "additionalProp1": {},
    "additionalProp2": {},
    "additionalProp3": {}
  }
}

```
Датчик

```json
{
  "hardwareId": "string",
  "sensorModelId": "string",
  "location": {
    "latitude": 0,
    "longitude": 0,
    "elevation": 0
  },
  "depth": 0,
  "port": {
    "name": "string",
    "type": "Analog"
  },
  "deviceId": "string",
  "name": "string",
  "description": "string",
  "properties": {
    "additionalProp1": {},
    "additionalProp2": {},
    "additionalProp3": {}
  }
}
```
Следующий пример запроса создает устройство. Этот запрос содержит входные данные JSON в качестве полезных данных с текстом запроса.

```bash
curl -X POST "https://<datahub>.azurewebsites.net/Device" -H  
"accept: application/json" -H  "Content-Type: application/json" -H
"Authorization: Bearer <Access-Token>" -d "{  \"deviceModelId\": \"ID123\",  \"hardwareId\": \"MHDN123\",  
\"reportingInterval\": 900,  \"name\": \"Device123\",  
\"description\": \"Test Device 123\"}" *
```

> [!NOTE]
> API-интерфейсы возвращают уникальные идентификаторы для каждого созданного экземпляра. Необходимо хранить идентификаторы для отправки соответствующих сообщений телеметрии.

### <a name="send-telemetry"></a>Отправка данных телеметрии

Теперь, когда вы создали устройства и датчики в Фармбеатс, вы можете отправить связанные сообщения телеметрии.

### <a name="create-a-telemetry-client"></a>Создание клиента телеметрии

Необходимо отправить данные телеметрии в концентраторы событий Azure для обработки. Концентраторы событий Azure — это служба, которая обеспечивает прием данных (телеметрии) в режиме реального времени от подключенных устройств и приложений. Чтобы отправить данные телеметрии в Фармбеатс, создайте клиент, отправляющий сообщения в концентратор событий в Фармбеатс. Дополнительные сведения об отправке телеметрии см. в статье [концентраторы событий Azure](https://docs.microsoft.com/azure/event-hubs/event-hubs-dotnet-standard-getstarted-send).

### <a name="send-a-telemetry-message-as-the-client"></a>Отправка сообщения телеметрии в качестве клиента

После подключения, установленного в качестве клиента концентраторов событий, можно отправить сообщения в концентратор событий в формате JSON.

Ниже приведен пример кода Python, который отправляет данные телеметрии в качестве клиента в указанный концентратор событий:

```python
import azure
from azure.eventhub import EventHubClient, Sender, EventData, Receiver, Offset
EVENTHUBCONNECTIONSTRING = "<EventHub Connection String provided by customer>"
EVENTHUBNAME = "<EventHub Name provided by customer>"

write_client = EventHubClient.from_connection_string(EVENTHUBCONNECTIONSTRING, eventhub=EVENTHUBNAME, debug=False)
sender = write_client.add_sender(partition="0")
write_client.run()
for i in range(5):
    telemetry = "<Canonical Telemetry message>"
    print("Sending telemetry: " + telemetry)
    sender.send(EventData(telemetry))
write_client.stop()

```

Преобразуйте формат исторических данных датчика в канонический формат, распознаваемый Azure Фармбеатс. Канонический формат сообщений выглядит следующим образом:

```json
{
"deviceid": "<id of the Device created>",
"timestamp": "<timestamp in ISO 8601 format>",
"version" : "1",
"sensors": [
    {
      "id": "<id of the sensor created>",
      "sensordata": [
        {
          "timestamp": "< timestamp in ISO 8601 format >",
          "<sensor measure name (as defined in the Sensor Model)>": "<value>"
        },
        {
          "timestamp": "<timestamp in ISO 8601 format>",
          "<sensor measure name (as defined in the Sensor Model)>": "<value>"
        }
      ]
    }
 ]
}
```

После добавления соответствующих устройств и датчиков получите идентификатор устройства и идентификатор датчика в сообщении телеметрии, как описано в предыдущем разделе.

Ниже приведен пример сообщения телеметрии.


 ```json
{
  "deviceid": "7f9b4b92-ba45-4a1d-a6ae-c6eda3a5bd12",
  "timestamp": "2019-06-22T06:55:02.7279559Z",
  "version": "1",
  "sensors": [
    {
      "id": "d8e7beb4-72de-4eed-9e00-45e09043a0b3",
      "sensordata": [
        {
          "timestamp": "2019-06-22T06:55:02.7279559Z",
          "hum_max": 15
        },
        {
          "timestamp": "2019-06-22T06:55:02.7279559Z",
          "hum_min": 42
        }
      ]
    },
    {
      "id": "d8e7beb4-72de-4eed-9e00-45e09043a0b3",
      "sensordata": [
        {
          "timestamp": "2019-06-22T06:55:02.7279559Z",
          "hum_max": 20
        },
        {
          "timestamp": "2019-06-22T06:55:02.7279559Z",
          "hum_min": 89
        }
      ]
    }
  ]
}
```


## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения об интеграции на основе REST API см. в разделе [REST API](references-for-farmbeats.md#rest-api).
