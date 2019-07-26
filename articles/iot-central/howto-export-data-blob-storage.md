---
title: Экспорт данных в хранилище BLOB-объектов Azure | Документация Майкрософт
description: Как экспортировать данные из приложения Azure IoT Central в хранилище BLOB-объектов Azure
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 07/08/2019
ms.topic: conceptual
ms.service: iot-central
manager: peterpr
ms.openlocfilehash: 0fcce6bd6ee9461790ca7618f65be9a20a821afc
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/19/2019
ms.locfileid: "68360339"
---
# <a name="export-your-data-to-azure-blob-storage"></a>Экспорт данных в хранилище BLOB-объектов Azure

*Эта статья предназначена для администраторов.*

В этой статье описывается, как использовать функцию непрерывного экспорта данных в Azure IoT Central для периодического экспорта данных в **учетную запись хранилища BLOB-объектов Azure**. Вы можете экспортировать **измерения**, **устройства** и **шаблоны устройств** в файлы формата Apache Avro. Экспортированные данные можно использовать для анализа холодного пути, например для обучения моделей в службе "Машинное обучение Azure" и долгосрочного анализа тенденций в Microsoft Power BI.

> [!Note]
> После включения непрерывного экспорта данных вы получаете только данные, поступающие с момента включения. Сейчас невозможно получить данные за то время, когда непрерывный экспорт данных был отключен. Чтобы сохранить больше исторических данных, включите экспорт непрерывных данных раньше.


## <a name="prerequisites"></a>предварительные требования

- Вы должны быть администратором в своем приложении IoT Central.


## <a name="set-up-export-destination"></a>Настройка места назначения экспорта

Если у вас нет существующего хранилища для экспорта, выполните следующие действия.

## <a name="create-storage-account"></a>Создание учетной записи хранения

1. Создайте [учетную запись хранения на портале Azure](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM). Дополнительные сведения см. в [документации по службе хранилища Azure](https://aka.ms/blobdocscreatestorageaccount).
2. Выберите тип учетной записи **Общее назначение** или **Хранилище BLOB-объектов**.
3. Выберите подписку. 

    > [!Note] 
    > Теперь вы можете экспортировать данные в другие подписки, которые **отличаются** от подписки приложения IoT Central с оплатой по мере использования. В этом случае вы подключаетесь с использованием строки подключения.

4. Создайте контейнер в учетной записи хранения. Войдите в свою учетную запись хранения. Выберите **Обзор BLOB-объектов** в разделе **Служба BLOB-объектов**. Выберите **+ контейнер** в верхней части, чтобы создать новый контейнер


## <a name="set-up-continuous-data-export"></a>Настройка экспорта непрерывных данных

Теперь, когда у вас есть место хранения для экспорта данных, выполните следующие действия, чтобы настроить непрерывный экспорт данных. 

1. Войдите в приложение IoT Central.

2. В меню слева выберите непрерывная **Экспорт данных**.

    > [!Note]
    > Если вы не видите этого пункта в меню слева, вы не являетесь администратором в приложении. Обратитесь к администратору, чтобы настроить экспорт данных.

    ![Создание нового концентратора событий для непрерывного экспорта данных](media/howto-export-data/export_menu1.png)

3. Нажмите кнопку **+ создать** в правом верхнем углу. Выберите **хранилище BLOB-объектов Azure** в качестве назначения для экспорта. 

    > [!NOTE] 
    > Максимальное число экспортов на каждое приложение равно пяти. 

    ![Создание непрерывного экспорта данных](media/howto-export-data/export_new1.png)

4. В раскрывающемся списке выберите **пространство имен учетной записи хранения**. Вы также можете выбрать последний вариант в списке — **Enter a connection string** (Ввести строку подключения). 

    > [!NOTE] 
    > Пространства имен учетных записей хранения будут отображаться только в той **же подписке, что и ваше приложение IOT Central**. Если вы хотите выполнить экспорт за пределы этой подписки, выберите **Enter a connection string** (Ввести строку подключения) и перейдите к шагу 5.

    > [!NOTE] 
    > Для приложений с 7-дневной бесплатной пробной версией единственным способом настроить непрерывный экспорт данных — использовать строку подключения. Это обусловлено тем, что приложения с 7-дневной бесплатной пробной версией не связаны с подпиской Azure.

    ![Создание нового концентратора событий для непрерывного экспорта данных](media/howto-export-data/export-create-blob.png)

5. (Необязательно.) Если вы выбрали **Enter a connection string** (Ввести строку подключения), открывается новое окно, в которое нужно вставить строку подключения. Чтобы получить строку подключения, сделайте следующее:
    - Учетная запись хранения, перейдите к учетной записи хранения в портал Azure.
        - В разделе **Параметры**выберите **ключи доступа** .
        - Скопируйте строку подключения key1 или строку подключения key2.
 
6. Выберите контейнер из раскрывающегося списка.

7. В разделе **Data to export** (Данные для экспорта) укажите каждый тип данных для экспорта, задав для типа значение **Включено**.

6. Чтобы включить непрерывный экспорт данных, убедитесь, что для параметра **Экспорт данных** установлено значение **Включено**. Щелкните **Сохранить**.

   ![Настройка экспорта непрерывных данных](media/howto-export-data/export-list-blob.png)

7. Через несколько минут ваши данные будут отображаться в выбранном назначении.


## <a name="export-to-azure-blob-storage"></a>Экспорт данных в хранилище BLOB-объектов Azure

Данные измерений, устройств и шаблонов устройств экспортируются в вашу учетную запись хранения раз в минуту. При этом каждый файл содержит пакет изменений с момента получения последнего экспортированного файла. Экспортированные данные находятся в формате [Apache Avro](https://avro.apache.org/docs/current/index.html) и будут экспортированы в три папки. Пути по умолчанию в вашей учетной записи хранения:
- Сообщения: {контейнер}/measurements/{имя центра}/{ГГГГ}/{ММ}/{дд}/{чч}/{мм}/{имя файла}.avro
- Устройства: {контейнер}/devices/{ГГГГ}/{ММ}/{дд}/{чч}/{мм}/{имя файла}.avro
- Шаблоны устройств: {контейнер}/deviceTemplates/{ГГГГ}/{ММ}/{дд}/{чч}/{мм}/{имя файла}.avro

### <a name="measurements"></a>Измерения

В данных экспортированных измерений содержатся все новые сообщения, полученные IoT Central со всех устройств в течение этого времени. Экспортированные файлы используют тот же формат, что и файлы сообщений, экспортированные с использованием [маршрутизации сообщений с помощью Центра Интернета вещей](https://docs.microsoft.com/azure/iot-hub/iot-hub-csharp-csharp-process-d2c) в хранилище BLOB-объектов.

> [!NOTE]
> Устройства, отправляющие измерения, представлены идентификаторами устройств (см. следующие разделы). Чтобы получить имена устройств, экспортируйте моментальные снимки устройств. Коррелируйте каждую запись сообщения с использованием идентификатора **connectionDeviceId**, который совпадает с идентификатором **deviceId** в записи об устройстве.

В следующем примере показана запись в декодированный файл Avro:

```json
{
    "EnqueuedTimeUtc": "2018-06-11T00:00:08.2250000Z",
    "Properties": {},
    "SystemProperties": {
        "connectionDeviceId": "<connectionDeviceId>",
        "connectionAuthMethod": "{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
        "connectionDeviceGenerationId": "<generationId>",
        "enqueuedTime": "2018-06-11T00:00:08.2250000Z"
    },
    "Body": "{\"humidity\":80.59100954598546,\"magnetometerX\":0.29451796907056726,\"magnetometerY\":0.5550332126050068,\"magnetometerZ\":-0.04116681874733441,\"connectivity\":\"connected\",\"opened\":\"triggered\"}"
}
```

### <a name="devices"></a>Устройств

При первом включении экспорта непрерывных данных экспортируется один моментальный снимок со всеми устройствами. Каждое устройство включает в себя:
- идентификатор (`id`) устройства в IoT Central;
- имя (`name`) устройства;
- идентификатор устройства (`deviceId`) из [Службы подготовки устройств](https://aka.ms/iotcentraldocsdps);
- сведения о шаблоне устройства;
- Значения свойств
- значения параметров.

Новый моментальный снимок записывается один раз в минуту. Моментальный снимок содержит:

- новые устройства, добавленные с момента создания последнего моментального снимка;
- устройства с измененными значениями свойств и параметров с момента создания последнего моментального снимка.

> [!NOTE]
> Устройства, удаленные с момента создания последнего моментального снимка, не экспортируются. Сейчас в моментальных снимках нет индикаторов удаленных устройств.
>
> Шаблон устройства, к которому принадлежит каждое устройство, представлен идентификатором шаблона устройства. Чтобы получить имя шаблона устройства, экспортируйте моментальные снимки шаблонов устройств.

Запись в декодированном файле Avro может выглядеть следующим образом:

```json
{
    "id": "<id>",
    "name": "Refrigerator 2",
    "simulated": true,
    "deviceId": "<deviceId>",
    "deviceTemplate": {
        "id": "<template id>",
        "version": "1.0.0"
    },
    "properties": {
        "cloud": {
            "location": "New York",
            "maintCon": true,
            "tempThresh": 20
        },
        "device": {
            "lastReboot": "2018-02-09T22:22:47.156Z"
        }
    },
    "settings": {
        "device": {
            "fanSpeed": 0
        }
    }
}
```

### <a name="device-templates"></a>Шаблоны устройств

При первом включении экспорта непрерывных данных экспортируется один моментальный снимок со всеми шаблонами устройств. Каждый шаблон устройства включает в себя:
- идентификатор (`id`) шаблона устройства;
- имя (`name`) шаблона устройства;
- версию (`version`) шаблона устройства;
- типы данных измерения, минимальные и максимальные значения;
- типы данных свойств и значения по умолчанию;
- типы данных параметров и значения по умолчанию.

Новый моментальный снимок записывается один раз в минуту. Моментальный снимок содержит:

- новые шаблоны устройств, добавленные с момента создания последнего моментального снимка;
- шаблоны устройств с измененными измерениями, свойствами и параметрами с момента создания последнего моментального снимка.

> [!NOTE]
> Шаблоны устройств, удаленные с момента создания последнего моментального снимка, не экспортируются. Сейчас в моментальных снимках нет индикаторов удаленных шаблонов устройств.

Запись в декодированном файле Avro может выглядеть следующим образом:

```json
{
    "id": "<id>",
    "name": "Refrigerated Vending Machine",
    "version": "1.0.0",
    "measurements": {
        "telemetry": {
            "humidity": {
                "dataType": "double",
                "name": "Humidity"
            },
            "magnetometerX": {
                "dataType": "double",
                "name": "Magnetometer X"
            },
            "magnetometerY": {
                "dataType": "double",
                "name": "Magnetometer Y"
            },
            "magnetometerZ": {
                "dataType": "double",
                "name": "Magnetometer Z"
            }
        },
        "states": {
            "connectivity": {
                "dataType": "enum",
                "name": "Connectivity"
            }
        },
        "events": {
            "opened": {
                "name": "Door Opened",
                "category": "informational"
            }
        }
    },
    "settings": {
        "device": {
            "fanSpeed": {
                "dataType": "double",
                "name": "Fan Speed",
                "initialValue": 0
            }
        }
    },
    "properties": {
        "cloud": {
            "location": {
                "dataType": "string",
                "name": "Location",
                "initialValue": "Seattle"
            },
            "maintCon": {
                "dataType": "boolean",
                "name": "Maintenance Contract",
                "initialValue": true
            },
            "tempThresh": {
                "dataType": "double",
                "name": "Temperature Alert Threshold",
                "initialValue": 30
            }
        },
        "device": {
            "lastReboot": {
                "dataType": "dateTime",
                "name": "Last Reboot"
            }
        }
    }
}
```

## <a name="read-exported-avro-files"></a>Чтение экспортированных файлов Avro

Avro — формат двоичных данных, поэтому файлы невозможно считать в необработанном состоянии. Файлы можно декодировать в формат JSON. Далее показаны примеры анализа файлов Avro с измерениями, устройствами и шаблонами устройств. Примеры соответствуют примерам, описанным в предыдущем разделе.

### <a name="read-avro-files-by-using-python"></a>Чтение файлов Avro с помощью Python

#### <a name="install-pandas-and-the-pandavro-package"></a>Установка пакета pandas и pandaavro

```python
pip install pandas
pip install pandavro
```

#### <a name="parse-a-measurements-avro-file"></a>Анализ файла Avro с измерениями

```python
import json
import pandavro as pdx
import pandas as pd


def parse(filePath):
    # Pandavro loads the Avro file into a pandas DataFrame
    # where each record is a single row.
    measurements = pdx.from_avro(filePath)

    # This example creates a new DataFrame and loads a series
    # for each column that's mapped into a column in our new DataFrame.
    transformed = pd.DataFrame()

    # The SystemProperties column contains a dictionary
    # with the device ID located under the connectionDeviceId key.
    transformed["device_id"] = measurements["SystemProperties"].apply(
        lambda x: x["connectionDeviceId"])

    # The Body column is a series of UTF-8 bytes that is stringified
    # and parsed as JSON. This example pulls the humidity property
    # from each column to get the humidity field.
    transformed["humidity"] = measurements["Body"].apply(
        lambda x: json.loads(bytes(x).decode('utf-8'))["humidity"])

    # Finally, print the new DataFrame with our device IDs and humidities.
    print(transformed)
```

#### <a name="parse-a-devices-avro-file"></a>Анализ файла Avro с устройствами

```python
import json
import pandavro as pdx
import pandas as pd


def parse(filePath):
    # Pandavro loads the Avro file into a pandas DataFrame
    # where each record is a single row.
    devices = pdx.from_avro(filePath)

    # This example creates a new DataFrame and loads a series
    # for each column that's mapped into a column in our new DataFrame.
    transformed = pd.DataFrame()

    # The device ID is available in the id column.
    transformed["device_id"] = devices["deviceId"]

    # The template ID and version are present in a dictionary under
    # the deviceTemplate column.
    transformed["template_id"] = devices["deviceTemplate"].apply(
        lambda x: x["id"])
    transformed["template_version"] = devices["deviceTemplate"].apply(
        lambda x: x["version"])

    # The fanSpeed setting value is located in a nested dictionary
    # under the settings column.
    transformed["fan_speed"] = devices["settings"].apply(
        lambda x: x["device"]["fanSpeed"])

    # Finally, print the new DataFrame with our device and template
    # information, along with the value of the fan speed.
    print(transformed)
```

#### <a name="parse-a-device-templates-avro-file"></a>Анализ файла Avro с шаблонами устройств

```python
import json
import pandavro as pdx
import pandas as pd


def parse(filePath):
    # Pandavro loads the Avro file into a pandas DataFrame
    # where each record is a single row.
    templates = pdx.from_avro(filePath)

    # This example creates a new DataFrame and loads a series
    # for each column that's mapped into a column in our new DataFrame.
    transformed = pd.DataFrame()

    # The template and version are available in the id and version columns.
    transformed["template_id"] = templates["id"]
    transformed["template_version"] = templates["version"]

    # The fanSpeed setting value is located in a nested dictionary
    # under the settings column.
    transformed["fan_speed"] = templates["settings"].apply(
        lambda x: x["device"]["fanSpeed"])

    # Finally, print the new DataFrame with our device and template
    # information, along with the value of the fan speed.
    print(transformed)
```

### <a name="read-avro-files-by-using-c"></a>Чтение файлов Avro с помощью C#

#### <a name="install-the-microsofthadoopavro-package"></a>Установка пакета Microsoft.Hadoop.Avro

```csharp
Install-Package Microsoft.Hadoop.Avro -Version 1.5.6
```

#### <a name="parse-a-measurements-avro-file"></a>Анализ файла Avro с измерениями

```csharp
using Microsoft.Hadoop.Avro;
using Microsoft.Hadoop.Avro.Container;
using Newtonsoft.Json;

public static async Task Run(string filePath)
{
    using (var fileStream = File.OpenRead(filePath))
    {
        using (var reader = AvroContainer.CreateGenericReader(fileStream))
        {
            // For one Avro container, where a container can contain multiple blocks,
            // loop through each block in the container.
            while (reader.MoveNext())
            {
                // Loop through the Avro records in the block and extract the fields.
                foreach (AvroRecord record in reader.Current.Objects)
                {
                    var systemProperties = record.GetField<IDictionary<string, object>>("SystemProperties");
                    var deviceId = systemProperties["connectionDeviceId"] as string;
                    Console.WriteLine("Device ID: {0}", deviceId);

                    using (var stream = new MemoryStream(record.GetField<byte[]>("Body")))
                    {
                        using (var streamReader = new StreamReader(stream, Encoding.UTF8))
                        {
                            var body = JsonSerializer.Create().Deserialize(streamReader, typeof(IDictionary<string, dynamic>)) as IDictionary<string, dynamic>;
                            var humidity = body["humidity"];
                            Console.WriteLine("Humidity: {0}", humidity);
                        }
                    }
                }
            }
        }
    }
}
```

#### <a name="parse-a-devices-avro-file"></a>Анализ файла Avro с устройствами

```csharp
using Microsoft.Hadoop.Avro;
using Microsoft.Hadoop.Avro.Container;

public static async Task Run(string filePath)
{
    using (var fileStream = File.OpenRead(filePath))
    {
        using (var reader = AvroContainer.CreateGenericReader(fileStream))
        {
            // For one Avro container, where a container can contain multiple blocks,
            // loop through each block in the container.
            while (reader.MoveNext())
            {
                // Loop through the Avro records in the block and extract the fields.
                foreach (AvroRecord record in reader.Current.Objects)
                {
                    // Get the field value directly. You can also yield return
                    // records and make the function IEnumerable<AvroRecord>.
                    var deviceId = record.GetField<string>("deviceId");

                    // The device template information is stored in a sub-record
                    // under the deviceTemplate field.
                    var deviceTemplateRecord = record.GetField<AvroRecord>("deviceTemplate");
                    var templateId = deviceTemplateRecord.GetField<string>("id");
                    var templateVersion = deviceTemplateRecord.GetField<string>("version");

                    // The settings and properties are nested two levels deep.
                    // The first level indicates settings or properties.
                    // The second level indicates the type of setting or property.
                    var settingsRecord = record.GetField<AvroRecord>("settings");
                    var deviceSettingsRecord = settingsRecord.GetField<IDictionary<string, dynamic>>("device");
                    var fanSpeed = deviceSettingsRecord["fanSpeed"];
                    
                    Console.WriteLine(
                        "Device ID: {0}, Template ID: {1}, Template Version: {2}, Fan Speed: {3}",
                        deviceId,
                        templateId,
                        templateVersion,
                        fanSpeed
                    );
                }
            }
        }
    }
}

```

#### <a name="parse-a-device-templates-avro-file"></a>Анализ файла Avro с шаблонами устройств

```csharp
using Microsoft.Hadoop.Avro;
using Microsoft.Hadoop.Avro.Container;

public static async Task Run(string filePath)
{
    using (var fileStream = File.OpenRead(filePath))
    {
        using (var reader = AvroContainer.CreateGenericReader(fileStream))
        {
            // For one Avro container, where a container can contain multiple blocks,
            // loop through each block in the container.
            while (reader.MoveNext())
            {
                // Loop through the Avro records in the block and extract the fields.
                foreach (AvroRecord record in reader.Current.Objects)
                {
                    // Get the field value directly. You can also yield return
                    // records and make the function IEnumerable<AvroRecord>.
                    var id = record.GetField<string>("id");
                    var version = record.GetField<string>("version");

                    // The settings and properties are nested two levels deep.
                    // The first level indicates settings or properties.
                    // The second level indicates the type of setting or property.
                    var settingsRecord = record.GetField<AvroRecord>("settings");
                    var deviceSettingsRecord = settingsRecord.GetField<IDictionary<string, dynamic>>("device");
                    var fanSpeed = deviceSettingsRecord["fanSpeed"];
                    
                    Console.WriteLine(
                        "ID: {1}, Version: {2}, Fan Speed: {3}",
                        id,
                        version,
                        fanSpeed
                    );
                }
            }
        }
    }
}
```

### <a name="read-avro-files-by-using-javascript"></a>Чтение файлов Avro с помощью JavaScript

#### <a name="install-the-avsc-package"></a>Установка пакета avsc

```javascript
npm install avsc
```

#### <a name="parse-a-measurements-avro-file"></a>Анализ файла Avro с измерениями

```javascript
const avro = require('avsc');

// Read the Avro file. Parse the device ID and humidity from each record.
async function parse(filePath) {
    const records = await load(filePath);
    for (const record of records) {
        // Fetch the device ID from the system properties.
        const deviceId = record.SystemProperties.connectionDeviceId;

        // Convert the body from a buffer to a string and parse it.
        const body = JSON.parse(record.Body.toString());

        // Get the humidty property from the body.
        const humidity = body.humidity;

        // Log the retrieved device ID and humidity.
        console.log(`Device ID: ${deviceId}`);
        console.log(`Humidity: ${humidity}`);
    }
}

function load(filePath) {
    return new Promise((resolve, reject) => {
        // The file decoder emits each record as a data event on a stream.
        // Collect the records into an array and return them at the end.
        const records = [];
        avro.createFileDecoder(filePath)
            .on('data', record => { records.push(record); })
            .on('end', () => resolve(records))
            .on('error', reject);
    });
}
```

#### <a name="parse-a-devices-avro-file"></a>Анализ файла Avro с устройствами

```javascript
const avro = require('avsc');

// Read the Avro file. Parse the device and template identification
// information and the fanSpeed setting for each device record.
async function parse(filePath) {
    const records = await load(filePath);
    for (const record of records) {
        // Fetch the device ID from the deviceId property.
        const deviceId = record.deviceId;

        // Fetch the template ID and version from the deviceTemplate property.
        const deviceTemplateId = record.deviceTemplate.id;
        const deviceTemplateVersion = record.deviceTemplate.version;

        // Get the fanSpeed from the nested device settings property.
        const fanSpeed = record.settings.device.fanSpeed;

        // Log the retrieved device ID and humidity.
        console.log(`deviceID: ${deviceId}, Template ID: ${deviceTemplateId}, Template Version: ${deviceTemplateVersion}, Fan Speed: ${fanSpeed}`);
    }
}

function load(filePath) {
    return new Promise((resolve, reject) => {
        // The file decoder emits each record as a data event on a stream.
        // Collect the records into an array and return them at the end.
        const records = [];
        avro.createFileDecoder(filePath)
            .on('data', record => { records.push(record); })
            .on('end', () => resolve(records))
            .on('error', reject);
    });
}
```

#### <a name="parse-a-device-templates-avro-file"></a>Анализ файла Avro с шаблонами устройств

```javascript
const avro = require('avsc');

// Read the Avro file. Parse the device and template identification
// information and the fanSpeed setting for each device record.
async function parse(filePath) {
    const records = await load(filePath);
    for (const record of records) {
        // Fetch the template ID and version from the id and verison properties.
        const templateId = record.id;
        const templateVersion = record.version;

        // Get the fanSpeed from the nested device settings property.
        const fanSpeed = record.settings.device.fanSpeed;

        // Log the retrieved device id and humidity.
        console.log(`Template ID: ${templateId}, Template Version: ${templateVersion}, Fan Speed: ${fanSpeed}`);
    }
}

function load(filePath) {
    return new Promise((resolve, reject) => {
        // The file decoder emits each record as a data event on a stream.
        // Collect the records into an array and return them at the end.
        const records = [];
        avro.createFileDecoder(filePath)
            .on('data', record => { records.push(record); })
            .on('end', () => resolve(records))
            .on('error', reject);
    });
}
```

## <a name="next-steps"></a>Следующие шаги

Теперь, когда вы знаете, как экспортировать данные, перейдите к следующему шагу:

> [!div class="nextstepaction"]
> [Visualize and analyze your Azure IoT Central data in a Power BI dashboard](howto-connect-powerbi.md) (Визуализация и анализ данных Azure IoT Central в Power BI)
