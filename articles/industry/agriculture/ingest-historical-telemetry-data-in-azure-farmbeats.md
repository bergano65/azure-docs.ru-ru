---
title: Прием архивных данных телеметрии
description: В этой статье описывается, как глотать исторические данные телеметрии.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: e0a5e89f256b562ce5f702e9ff1388cb4d021bf5
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437693"
---
# <a name="ingest-historical-telemetry-data"></a>Прием архивных данных телеметрии

В этой статье описывается, как глотать данные об исторических датчиках в Azure FarmBeats.

Использование исторических данных из ресурсов Интернета вещей (IoT), таких как устройства и датчики, является распространенным сценарием в FarmBeats. Вы создаете метаданные для устройств и датчиков, а затем глотаете исторические данные в FarmBeats в каноническом формате.

## <a name="before-you-begin"></a>Подготовка к работе

Прежде чем приступить к этой статье, убедитесь, что вы установили FarmBeats и собрали исторические данные с устройств IoT. Вы также должны включить доступ к партнеру, как указано в следующих шагах.

## <a name="enable-partner-access"></a>Включить доступ к партнеру

Необходимо включить интеграцию партнеров в экземпляр Azure FarmBeats. Этот шаг создает клиента, который имеет доступ к экземпляру Azure FarmBeats в качестве партнера устройства, и предоставляет вам следующие значения, необходимые в последующих шагах:

- Конечная точка API: Это URL Datahub,\<например, https:// Datahub>.azurewebsites.net
- Tenant ID
- Идентификатор клиента
- Секрет клиента
- Строка соединения EventHub

Выполните следующие действия.

> [!NOTE]
> Вы должны быть администратором, чтобы сделать следующие шаги.

1. Выполните вход в https://portal.azure.com/.

2. **Если вы находитесь на FarmBeats версии 1.2.7 или позже, пропустить шаги a, b и c, и перейти к шагу 3.** Вы можете проверить версию FarmBeats, выбрав значок **настроек** в правом верхнем углу uI FarmBeats.

      а.  Перейти к > **регистрации приложений** Active **Directory Azure**

      b. Выберите **регистрацию приложений,** которая была создана в рамках развертывания FarmBeats. Он будет иметь то же имя, что и ваш Datahub FarmBeats.

      c. Выберите **Expose API** > выберите **Добавить клиентское приложение** и введите **04b07795-8ddb-461a-bbee-02f9e1bf7b46** и проверьте **Возможности Scope**. Это даст доступ к Azure CLI (Cloud Shell) для выполнения нижеприведенных шагов:

3. Откройте Cloud Shell. Эта опция доступна на панели инструментов в правом верхнем углу портала Azure.

    ![Панель инструментов портала Azure](./media/get-drone-imagery-from-drone-partner/navigation-bar-1.png)

4. Убедитесь, что среда настроена на **PowerShell.** По умолчанию, он установлен на Bash.

    ![Настройка панели инструментов PowerShell](./media/get-sensor-data-from-sensor-partner/power-shell-new-1.png)

5. Иди в свой домашний каталог.

    ```azurepowershell-interactive 
    cd  
    ```

6. Выполните следующую команду. Это позволит загрузить сценарий в ваш домашний каталог.

    ```azurepowershell-interactive 

    wget –q https://aka.ms/farmbeatspartnerscriptv3 -O ./generatePartnerCredentials.ps1

    ```

7. Выполните следующий сценарий. Скрипт запрашивает идентификатор арендатора, который можно получить на странице Active Directory > **Overview** **Azure.**

    ```azurepowershell-interactive 

    ./generatePartnerCredentials.ps1   

    ```

8. Следуйте инструкциям на экране, чтобы захватить значения для **API Endpoint**, **Идентификатор клиента**, **Клиент ID**, **Секрет клиента**, и **EventHub подключения строки**.


## <a name="create-device-or-sensor-metadata"></a>Создание метаданных устройства или датчика

 Теперь, когда у вас есть необходимые учетные данные, вы можете определить устройство и датчики. Для этого создайте метаданные, позвонив в ApIs FarmBeats. Убедитесь в том, чтобы вызвать AI в качестве клиентского приложения, которое вы создали в вышеуказанном разделе.

 FarmBeats Datahub имеет следующие AI, которые позволяют создавать и управленца метаданными устройств или датчиков.

 > [!NOTE]
 > Как партнер у вас есть доступ только для чтения, создания и обновления метаданных; **опция удаления ограничена партнером.**

- /**DeviceModel**: DeviceModel соответствует метаданным устройства, таким как производитель и тип устройства, которое является либо шлюзом, либо узла.
- /**Устройство**: Устройство соответствует физическому устройству, присутствуютму на ферме.
- /**SensorModel**: SensorModel соответствует метаданным датчика, таким как производитель, тип датчика, который является либо аналоговым, либо цифровым, и измерению датчика, таким как температура окружающей среды и давление.
- /**Датчик**: Датчик соответствует физическому датчику, который записывает значения. Датчик обычно подключается к устройству с идентификатором устройства.  


|        DeviceModel   |  Предложения   |
| ------- | -------             |
|     Тип (узла, шлюза)        |          Тип устройства - узла или шлюза      |
|          Производитель            |         Наименование производителя    |
|  Код продукта                    |  Код продукта устройства или название модели или номер. Например, EnviroMonitor-6800.  |
|            порты;          |     Название порта и тип, который является цифровым или аналоговым.
|     Имя                 |  Имя для идентификации ресурса. Например, название модели или название продукта.
      Описание     | Предоставьте осмысленное описание модели.
|    Свойства          |    Дополнительные свойства от производителя.   |
|    **Устройство**             |                      |
|   DeviceModelId     |     Идентификатор модели связанного устройства.  |
|  ОборудованиеId          | Уникальный идентификатор для устройства, например адрес MAC.
|  ReportingInterval        |   Интервал отчетности в секундах.
|  Расположение            |  Широта устройства (от 90 до 90 евро), долгота (-180 до 180) и высота (в метрах).   
|ParentDeviceId       |    Идентификатор родительского устройства, к которому подключено это устройство. Например, узла, подключенного к шлюзу. Узла имеет parentDeviceId в качестве шлюза.  |
|    Имя            | Имя для идентификации ресурса. Партнеры устройства должны отправить имя, которое соответствует названию устройства на стороне партнера. Если имя устройства партнера определено пользователем, то одно и то же имя, определенное пользователем, должно распространяться на FarmBeats.|
|     Описание       |      Предоставьте содержательное описание. |
|     Свойства    |  Дополнительные свойства от производителя.
|     **SensorModel**        |          |
|       Тип (аналоговый, цифровой)          |      Тип датчика, будь то аналоговый или цифровой.       |
|          Производитель            |       Производитель датчика.     |
|     Код продукта| Код продукта или название модели или номер. Например, RS-CO2-N01. |
|       SensorMeasures > имя       | Название датчика измерения. Поддерживается только нижний регистр. Для измерений из разных глубин укажите глубину. Например, soil_moisture_15cm. Это имя должно соответствовать данным телеметрии.  |
|          SensorMeasures > DataType       |Тип данных телеметрии. В настоящее время двойника поддерживается.|
|    ДатчикМеры > типа    |Измерение типа данных телеметрии датчика. Системно-определяемые типы: AmbientTemperature, CO2, Depth, ElectricalConductivity, LeafWetness, Length, LiquidLevel, Nitrate, O2, PH, Phosphate, PointInTime, Калий, Давление, RainGauge, RelativeHumidity, Salinity, SoilMoisture, SoilTemperature, SolarRadiation, State, TimeDuration, UVRadiation, UVIndex, Volume, WindDirection, Wind. Чтобы добавить больше, обратитесь к /ExtendedType API.|
|        SensorMeasures > Unit              | Единица данных сенсорной телеметрии. Системно-определяемые единицы: NoUnit, Celsius, Fahrenheit, Кельвин, Ранкин, Паскаль, Меркурий, PSI, MilliMeter, CentiMeter, Метр, Инч, Ноги, Миля, Километр, MilesPerHour, MilesPerSecond, KMPerHour, Метры, Метры, MilliJoulesPerSquareCentiMeter, VolumetricWaterContent, Процент, PartsPerMillion, MicroMol, MicroMolesPerLiter, SiemensPerSquareMeterPerMole, Centibar, DeciSiemensPerMeter, KiloPascal, VolumetricIonContent, Liter, MilliLiter, Seconds, UnixTimestamp, MicroMolPer|
|    SensorMeasures > агрегацииТип    |  Значения могут быть не средними, максимальными, минимальными или StandardDeviation.  |
|          Имя            | Имя для идентификации ресурса. Например, название модели или название продукта.  |
|    Описание        | Предоставьте осмысленное описание модели.|
|   Свойства       |  Дополнительные свойства от производителя.|
|    **Датчик**      |          |
| ОборудованиеId          |   Уникальный идентификатор для датчика, установленного производителем.|
|  SensorModelId     |    Идентификатор модели связанного датчика.|
| Расположение          |  Датчик широты (от 90 до 90 евро), долгота (-180 до 180), и высота (в метрах).|
|   Название порт >        |  Наименование и тип порта, к которому подключен датчик на устройстве. Это должно быть то же имя, что и в модели устройства.|
|    DeviceID  |    Идентификатор устройства, к которому подключен датчик. |
| Имя            |   Имя для идентификации ресурса. Например, имя датчика или название продукта, номер модели или код продукта.|
|    Описание      | Предоставьте содержательное описание.|
|    Свойства        |Дополнительные свойства от производителя.|

Для получения дополнительной информации об объектах, [см.](https://aka.ms/FarmBeatsDatahubSwagger)

### <a name="api-request-to-create-metadata"></a>Запрос API на создание метаданных

Чтобы сделать запрос API, вы объедините метод HTTP (POST), URL-адрес службы API и URI с ресурсом для запроса, отправки данных, создания или удаления запроса. Затем вы добавляете один или несколько заголовков запросов HTTP. URL-адрес службы API является конечным пунктом API, т.е. URL Datahub (https://\<yourdatahub>.azurewebsites.net).  

### <a name="authentication"></a>Аутентификация

FarmBeats Datahub использует проверку подлинности носителя, которая требует следующих учетных данных, которые были созданы в предыдущем разделе:

- Идентификатор клиента
- Секрет клиента
- Tenant ID

Используя эти учетные данные, абонент может запросить токен доступа. Токен должен быть отправлен в последующих запросах API в разделе заголовок следующим образом:

```
headers = *{"Authorization": "Bearer " + access_token, …}*
```

Следующий пример кода Python дает токен доступа, который может быть использован для последующих вызовов API в FarmBeats: 

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

**Заголовки запросов HTTP**

Вот наиболее распространенные заголовки запросов, которые должны быть указаны при вызове API в FarmBeats Datahub:

- **Содержимое-Тип**: приложение/json
- **Авторизация**:> <доступа
- **Принять**: применение/json

### <a name="input-payload-to-create-metadata"></a>Полезная нагрузка ввода для создания метаданных

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

SensorModel

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

Следующий запрос образца создает устройство. Этот запрос имеет ввод JSON в качестве полезной нагрузки с органом запроса.

```bash
curl -X POST "https://<datahub>.azurewebsites.net/Device" -H  
"accept: application/json" -H  "Content-Type: application/json" -H
"Authorization: Bearer <Access-Token>" -d "{  \"deviceModelId\": \"ID123\",  \"hardwareId\": \"MHDN123\",  
\"reportingInterval\": 900,  \"name\": \"Device123\",  
\"description\": \"Test Device 123\"}" *
```

Ниже приведен пример кода в Python. Токен доступа, используемый в данном образце, является тем же, что и во время проверки подлинности.

```python
import requests
import json

# Got access token - Calling the Device Model API

headers = {
    "Authorization": "Bearer " + access_token,
    "Content-Type" : "application/json"
    }
payload = '{"type" : "Node", "productCode" : "TestCode", "ports": [{"name": "port1","type": "Analog"}], "name" : "DummyDevice"}'
response = requests.post(ENDPOINT + "/DeviceModel", data=payload, headers=headers)
```

> [!NOTE]
> AA возвращают уникальные иди для каждого созданного экземпляра. Для отправки соответствующих телеметрических сообщений необходимо сохранить идентификаторы.

### <a name="send-telemetry"></a>Отправка данных телеметрии

Теперь, когда вы создали устройства и датчики в FarmBeats, вы можете отправлять связанные с ними телеметрические сообщения.

### <a name="create-a-telemetry-client"></a>Создание телеметрического клиента

Для обработки необходимо отправить телеметрию в концентраторы событий Azure. Azure Event Hubs — это служба, которая позволяет в режиме реального времени получать данные (телеметрию) с подключенных устройств и приложений. Чтобы отправить данные телеметрии в FarmBeats, создайте клиента, который отправляет сообщения в концентратор событий в FarmBeats. Для получения дополнительной информации об отправке [телеметрии](https://docs.microsoft.com/azure/event-hubs/event-hubs-dotnet-standard-getstarted-send)см.

### <a name="send-a-telemetry-message-as-the-client"></a>Отправить телеметрическое сообщение в качестве клиента

После того как у вас есть соединение, созданное в качестве клиента Event Hubs, вы можете отправлять сообщения в концентратор событий как JSON.

Вот пример кода Python, который отправляет телеметрию в качестве клиента в определенный концентратор событий:

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

Преобразуйте формат данных исторического датчика в канонический формат, который понимает Azure FarmBeats. Формат канонического сообщения:

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
          "<sensor measure name (as defined in the Sensor Model)>": <value>
        },
        {
          "timestamp": "<timestamp in ISO 8601 format>",
          "<sensor measure name (as defined in the Sensor Model)>": <value>
        }
      ]
    }
 ]
}
```

После добавления соответствующих устройств и датчиков, получить идентификатор устройства и идентификатор датчика в телеметрическом сообщении, как описано в предыдущем разделе.

Вот пример телеметрического сообщения:


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

## <a name="troubleshooting"></a>Устранение неполадок

### <a name="cant-view-telemetry-data-after-ingesting-historicalstreaming-data-from-your-sensors"></a>Не уклониться от просмотра телеметрических данных после попавания исторических/потоковых данных с датчиков

**Симптом**: Устройства или датчики развернуты, и вы создали устройства / датчики на FarmBeats и попадает телеметрии в EventHub, но вы не можете получить или просмотреть телеметрические данные на FarmBeats.

**Корректирующее действие**:

1. Убедитесь, что вы сделали соответствующую регистрацию партнера - вы можете проверить это, перейдя к datahub чванство, перейдите на / Партнер API, Do Получить и проверить, если партнер зарегистрирован. Если нет, следуйте [шагам здесь,](get-sensor-data-from-sensor-partner.md#enable-device-integration-with-farmbeats) чтобы добавить партнера.

2. Убедитесь, что вы создали метаданные (DeviceModel, Device, SensorModel, Sensor) с помощью учетных данных клиента-партнера.

3. Убедитесь, что вы использовали правильный формат сообщения Телеметрии (как указано ниже):

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
          "<sensor measure name (as defined in the Sensor Model)>": <value>
        },
        {
          "timestamp": "<timestamp in ISO 8601 format>",
          "<sensor measure name (as defined in the Sensor Model)>": <value>
        }
      ]
    }
 ]
}
```

## <a name="next-steps"></a>Следующие шаги

Для получения дополнительной информации о деталях [REST API](rest-api-in-azure-farmbeats.md)интеграции на основе REST API см.
