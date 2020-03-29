---
title: Прием архивных данных телеметрии
description: В этой статье описывается, как глотать исторические данные телеметрии.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: b0b9d62e8761cfb67d0642d8e5a97e7d1f05af12
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80064456"
---
# <a name="ingest-historical-telemetry-data"></a>Прием архивных данных телеметрии

В этой статье описывается, как глотать данные об исторических датчиках в Azure FarmBeats.

Использование исторических данных из ресурсов Интернета вещей (IoT), таких как устройства и датчики, является распространенным сценарием в FarmBeats. Вы создаете метаданные для устройств и датчиков, а затем глотаете исторические данные в FarmBeats в каноническом формате.

## <a name="before-you-begin"></a>Перед началом

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

1. Скачать [почтовый файл,](https://aka.ms/farmbeatspartnerscriptv2)и извлечь его на локальном диске. Там будет один файл внутри почтового файла.

2. Войдите https://portal.azure.com/ в систему и перейдите на**регистрацию приложений** **Azure Active Directory.** > 

3. Выберите **регистрацию приложений,** которая была создана в рамках развертывания FarmBeats. Он будет иметь то же имя, что и ваш FarmBeats Datahub.

4. Выберите **Expose API** > Выберите **Добавить клиентское приложение** и введите **04b07795-8ddb-461a-bbee-02f9e1bf7b46** и проверьте **Возможности Scope**. Это даст доступ к Azure CLI (Cloud Shell) для выполнения следующих шагов:

5. Откройте Cloud Shell. Эта опция доступна на панели инструментов в правом верхнем углу портала Azure.

    ![Панель инструментов портала Azure](./media/get-drone-imagery-from-drone-partner/navigation-bar-1.png)

6. Убедитесь, что среда настроена на **PowerShell.** По умолчанию, он установлен на Bash.

    ![Настройка панели инструментов PowerShell](./media/get-sensor-data-from-sensor-partner/power-shell-new-1.png)

7. Загрузите файл со ступени 1 в экземпляре Облачной оболочки.

    ![Кнопка загрузки панели инструментов](./media/get-sensor-data-from-sensor-partner/power-shell-two-1.png)

8. Перейдите в каталог, где файл был загружен. По умолчанию файлы загружаются в домашний каталог под именем пользователя.

9. Выполните следующий сценарий. Скрипт запрашивает идентификатор арендатора, который можно получить на**странице Обзора** **active Directory** > Azure.

    ```azurepowershell-interactive

    ./generatePartnerCredentials.ps1   

    ```

10. Следуйте инструкциям на экране, чтобы захватить значения для **API Endpoint**, **Идентификатор клиента**, **Клиент ID**, **Секрет клиента**, и **EventHub подключения строки**.

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
|     name                 |  Имя для идентификации ресурса. Например, название модели или название продукта.
      Описание     | Предоставьте осмысленное описание модели.
|    Свойства          |    Дополнительные свойства от производителя.   |
|    **Устройство**             |                      |
|   DeviceModelId     |     Идентификатор модели связанного устройства.  |
|  ОборудованиеId          | Уникальный идентификатор для устройства, например адрес MAC.
|  ReportingInterval        |   Интервал отчетности в секундах.
|  Расположение            |  Широта устройства (от 90 до 90 евро), долгота (-180 до 180) и высота (в метрах).   
|ParentDeviceId       |    Идентификатор родительского устройства, к которому подключено это устройство. Например, узла, подключенного к шлюзу. Узла имеет parentDeviceId в качестве шлюза.  |
|    name            | Имя для идентификации ресурса. Партнеры устройства должны отправить имя, которое соответствует названию устройства на стороне партнера. Если имя устройства партнера определено пользователем, то одно и то же имя, определенное пользователем, должно распространяться на FarmBeats.|
|     Описание       |      Предоставьте содержательное описание. |
|     Свойства    |  Дополнительные свойства от производителя.
|     **SensorModel**        |          |
|       Тип (аналоговый, цифровой)          |      Тип датчика, будь то аналоговый или цифровой.       |
|          Производитель            |       Производитель датчика.     |
|     Код продукта| Код продукта или название модели или номер. Например, RS-CO2-N01. |
|       SensorMeasures > имя       | Название датчика измерения. Поддерживается только нижний регистр. Для измерений из разных глубин укажите глубину. Например, soil_moisture_15cm. Это имя должно соответствовать данным телеметрии.  |
|          SensorMeasures > DataType       |Тип данных телеметрии. В настоящее время двойника поддерживается.|
|    ДатчикМеры > типа    |Измерение типа данных телеметрии датчика. Системно-определяемые типы: AmbientTemperature, CO2, Глубина, Электрическая проводимость, LeafWetness, Длина, LiquidLevel, Нитрат, O2, PH, Фосфат, PointInTime, Калий, Давление, RainGauge, Относительнаявлажность, Полиность, Почва, SoilTemperature, SolarRadiation, Государство, TimeDuration, UVRadiation, UVIndex, Объем, WindDirection, WindRun, WindSpeed, Evapotranspiration, PAR. Чтобы добавить больше, обратитесь к /ExtendedType API.|
|        SensorMeasures > Unit              | Единица данных сенсорной телеметрии. Системные блоки: NoUnit, Celsius, Fahrenheit, Кельвин, Ранкин, Паскаль, Меркурий, PSI, MilliMeter, CentiMeter, Метр, Инч, Ноги, Миля, Километры, MilesPerHour, MilesPerSecond, KMPerHour, KMPerSecond, Метры, Метры, ИзмерениеВВВВ, Степень, ВаттсПерКВарМе, КиловаттсПерКМетр, МиллиВаттсPerSquareCentiMeter, MilliJolesPerCentiMeter, MilliJolesPerSquare MilliSiemensPerCentiMeter, Centibar, DeciSiemensPerMeter, KiloPascal, VolumetricIonContent, Liter, MilliLiter, Seconds, UnixTimestamp, MicroMolPerMeterSquaredPerSecond, InchesPerHour Чтобы добавить больше, обратитесь к /ExtendedType API.|
|    SensorMeasures > агрегацииТип    |  Значения могут быть не средними, максимальными, минимальными или StandardDeviation.  |
|          name            | Имя для идентификации ресурса. Например, название модели или название продукта.  |
|    Описание        | Предоставьте осмысленное описание модели.|
|   Свойства       |  Дополнительные свойства от производителя.|
|    **Датчик**      |          |
| ОборудованиеId          |   Уникальный идентификатор для датчика, установленного производителем.|
|  SensorModelId     |    Идентификатор модели связанного датчика.|
| Расположение          |  Датчик широты (от 90 до 90 евро), долгота (-180 до 180), и высота (в метрах).|
|   Название порт >        |  Наименование и тип порта, к которому подключен датчик на устройстве. Это должно быть то же имя, что и в модели устройства.|
|    DeviceID  |    Идентификатор устройства, к которому подключен датчик. |
| name            |   Имя для идентификации ресурса. Например, имя датчика или название продукта, номер модели или код продукта.|
|    Описание      | Предоставьте содержательное описание.|
|    Свойства        |Дополнительные свойства от производителя.|

Для получения дополнительной информации об объектах, [см.](https://aka.ms/FarmBeatsDatahubSwagger)

### <a name="api-request-to-create-metadata"></a>Запрос API на создание метаданных

Чтобы сделать запрос API, вы объедините метод HTTP (POST), URL-адрес службы API и URI с ресурсом для запроса, отправки данных, создания или удаления запроса. Затем вы добавляете один или несколько заголовков запросов HTTP. URL-адрес службы API является конечным пунктом API, т.е. URL Datahub (https://\<yourdatahub>.azurewebsites.net).  

### <a name="authentication"></a>Проверка подлинности

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
          "<sensor measure name (as defined in the Sensor Model)>": "<value>"
        },
        {
          "timestamp": "<timestamp in ISO 8601 format>",
          "<sensor measure name (as defined in the Sensor Model)>": "<values>"
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

## <a name="next-steps"></a>Дальнейшие действия

Для получения дополнительной информации о деталях [REST API](rest-api-in-azure-farmbeats.md)интеграции на основе REST API см.
