---
title: Экспорт данных в хранилище BLOB-объектов Azure | Документация Майкрософт
description: Как экспортировать данные из приложения Azure IoT Central в хранилище BLOB-объектов Azure
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 09/26/2019
ms.topic: conceptual
ms.service: iot-central
manager: corywink
ms.openlocfilehash: 9ac650273a53da715b89e3233b30cf50710cfcfd
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/05/2019
ms.locfileid: "71973263"
---
# <a name="export-your-data-to-azure-blob-storage-preview-features"></a>Экспорт данных в хранилище BLOB-объектов Azure (Предварительная версия функций)

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

*Эта статья предназначена для администраторов.*

В этой статье описывается, как использовать функцию непрерывного экспорта данных в Azure IoT Central для периодического экспорта данных в **учетную запись хранения BLOB-объектов Azure** или в **учетную запись хранения Azure Data Lake Storage 2-го поколения**. Вы можете экспортировать данные **телеметрии**, **устройств**и **шаблонов устройств** в файлы в формате JSON. Экспортированные данные можно использовать для анализа холодного пути, например для обучения моделей в службе "Машинное обучение Azure" и долгосрочного анализа тенденций в Microsoft Power BI.

> [!Note]
> С момента включения экспорта непрерывных данных вы получаете только поступающие данные. Сейчас невозможно получить данные за то время, когда непрерывный экспорт данных был отключен. Чтобы сохранить больше исторических данных, включите экспорт непрерывных данных раньше.


## <a name="prerequisites"></a>Предварительные требования

- Вы должны быть администратором в своем приложении IoT Central.

## <a name="create-storage-account"></a>Создайте учетную запись хранения
Если у вас нет существующего хранилища для экспорта, выполните следующие действия.

1. Создайте [учетную запись хранения на портале Azure](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM). Вы можете узнать больше о создании новых [учетных записей хранения BLOB-объектов Azure](https://aka.ms/blobdocscreatestorageaccount) или [учетных записей хранения Azure Data Lake Storage v2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-quickstart-create-account).

    > [!Note] 
    > Если вы решили экспортировать данные в учетную запись хранения ADLS v2, необходимо выбрать **тип учетной записи** **блобстораже**. 

    > [!Note] 
    > Вы можете экспортировать данные в учетные записи хранения в подписках, отличных от подписки с оплатой по мере использования IoT Central приложении. В этом случае вы подключаетесь с использованием строки подключения.

2. Создайте контейнер в учетной записи хранения. Войдите в свою учетную запись хранения. Выберите **Обзор BLOB-объектов** в разделе **Служба BLOB-объектов**. Выберите **+ Контейнер** в верхней части экрана, чтобы создать контейнер.


## <a name="set-up-continuous-data-export"></a>Настройка экспорта непрерывных данных

Теперь, когда у вас есть место хранения для экспорта данных, выполните следующие действия, чтобы настроить непрерывный экспорт данных. 

1. Войдите в приложение IoT Central.

2. В меню слева выберите **Экспорт данных**.

    > [!Note]
    > Если вы не видите элемент Экспорт данных в меню слева, вы не являетесь администратором в приложении. Обратитесь к администратору, чтобы настроить экспорт данных.

3. Нажмите кнопку **+ создать** в правом верхнем углу. Выберите **хранилище BLOB-объектов Azure** в качестве назначения для экспорта. 

    > [!NOTE] 
    > Максимальное число экспортов на каждое приложение равно пяти. 

    ![Создание непрерывного экспорта данных](media/howto-export-data-pnp/export-new2.png)

4. В раскрывающемся списке выберите **пространство имен учетной записи хранения**. Вы также можете выбрать последний вариант в списке — **Enter a connection string** (Ввести строку подключения). 

    > [!NOTE] 
    > Пространства имен учетных записей хранения будут отображаться только в той **же подписке, что и ваше приложение IOT Central**. Если вы хотите выполнить экспорт за пределы этой подписки, выберите **Enter a connection string** (Ввести строку подключения) и перейдите к шагу 5.

    > [!NOTE] 
    > Для приложений с 7-дневной бесплатной пробной версией единственным способом настроить непрерывный экспорт данных — использовать строку подключения. Это обусловлено тем, что приложения с 7-дневной бесплатной пробной версией не связаны с подпиской Azure.

    ![Создать новый экспорт в большой двоичный объект](media/howto-export-data-pnp/export-create-blob2.png)

5. (Необязательно.) Если вы выбрали **Enter a connection string** (Ввести строку подключения), открывается новое окно, в которое нужно вставить строку подключения. Чтобы получить строку подключения для учетной записи хранения, перейдите к учетной записи хранения в портал Azure:
    - В разделе **Параметры**выберите **ключи доступа** .
    - Скопируйте строку подключения key1 или строку подключения key2.
 
6. Выберите контейнер из раскрывающегося списка. Если у вас нет контейнера, перейдите к своей учетной записи хранения в портал Azure:
    - В разделе **Служба BLOB-объектов**выберите **BLOB-объекты**. Щелкните **+ Container (+ контейнер** ) и присвойте контейнеру имя. Выберите общий уровень доступа к данным (все будет работать с непрерывным экспортом данных). Дополнительные сведения см. в документации по [службе хранилища Azure](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal#create-a-container).

7.  В разделе **Data to export** (Данные для экспорта) укажите каждый тип данных для экспорта, задав для типа значение **Включено**.
   
    > [!NOTE] 
    > Данные экспортируются в формате JSON.

8. Чтобы включить непрерывный экспорт данных, убедитесь, что включен режим **экспорта данных** . Щелкните **Сохранить**.

   ![Настройка экспорта непрерывных данных](media/howto-export-data-pnp/export-list-blob2.png)

9. Через несколько минут данные будут отображаться в учетной записи хранения.


## <a name="path-structure"></a>Структура пути

Данные телеметрии, устройств и шаблонов устройств экспортируются в учетную запись хранения один раз в минуту, при этом каждый файл содержит пакет изменений с момента последнего экспортированного файла. Экспортированные данные помещаются в три папки в формате JSON. Пути по умолчанию в вашей учетной записи хранения:
- Данные телеметрии: {Container}/{АПП-ИД}/телеметри/{ИИИИ}/{мм}/{дд}/{ХХ}/{мм}/{филенаме}
- Устройства: {Container}/{АПП-ИД}/девицес/{ИИИИ}/{мм}/{дд}/{ХХ}/{мм}/{филенаме}
- Шаблоны устройств: {Container}/{АПП-ИД}/девицетемплатес/{ИИИИ}/{мм}/{дд}/{ХХ}/{мм}/{филенаме}

Вы можете просмотреть экспортированные файлы в портал Azure, перейдя к файлу и выбрав вкладку **Edit BLOB (изменить большой двоичный объект** ).

## <a name="data-format"></a>Формат данных
### <a name="telemetry"></a>Телеметрия

Экспортированные данные телеметрии имеют все новые сообщения, полученные IoT Central со всех устройств в течение этого времени. Экспортированные файлы используют тот же формат, что и файлы сообщений, экспортированные с использованием [маршрутизации сообщений с помощью Центра Интернета вещей](https://docs.microsoft.com/azure/iot-hub/iot-hub-csharp-csharp-process-d2c) в хранилище BLOB-объектов.

> [!NOTE]
> Убедитесь, что устройства отправляют сообщения с `contentType: application/JSON` и `contentEncoding:utf-8` (или `utf-16`, `utf-32`). Пример см. в [документации по центру Интернета вещей](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-routing-query-syntax#message-routing-query-based-on-message-body) .

> [!NOTE]
> Устройства, отправляющие данные телеметрии, представлены идентификаторами устройств (см. следующие разделы). Чтобы получить имена устройств, экспортируйте моментальные снимки устройств. Коррелируйте каждую запись сообщения с использованием идентификатора **connectionDeviceId**, который совпадает с идентификатором **deviceId** в записи об устройстве.

В следующем примере показана запись в формате JSON.

```json
{ 
  "EnqueuedTimeUtc":"2019-09-26T17:46:09.8870000Z",
  "Properties":{ 

  },
  "SystemProperties":{ 
    "connectionDeviceId":"<deviceid>",
    "connectionAuthMethod":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
    "connectionDeviceGenerationId":"637051167384630591",
    "contentType":"application/json",
    "contentEncoding":"utf-8",
    "enqueuedTime":"2019-09-26T17:46:09.8870000Z"
  },
  "Body":{ 
    "temp":49.91322758395974,
    "humid":49.61214852573155,
    "pm25":25.87332214661367
  }
}
```

### <a name="devices"></a>Устройств

При первом включении экспорта непрерывных данных экспортируется один моментальный снимок со всеми устройствами. Каждое устройство включает в себя:
- идентификатор (`@id`) устройства в IoT Central;
- имя (`name`) устройства;
- идентификатор устройства (`deviceId`) из [Службы подготовки устройств](https://aka.ms/iotcentraldocsdps);
- сведения о шаблоне устройства;
- Значения свойств

Новый моментальный снимок записывается один раз в минуту. Моментальный снимок содержит:

- новые устройства, добавленные с момента создания последнего моментального снимка;
- Устройства с измененными значениями свойств с момента последнего моментального снимка.

> [!NOTE]
> Устройства, удаленные с момента создания последнего моментального снимка, не экспортируются. Сейчас в моментальных снимках нет индикаторов удаленных устройств.
>
> Шаблон устройства, к которому принадлежит каждое устройство, представлен полем `instanceOf`. Чтобы получить имя шаблона устройства, экспортируйте моментальные снимки шаблонов устройств.

Экспортируемые файлы содержат одну строку для каждой записи. В следующем примере показана запись в формате JSON.

```json
{ 
  "@id":"<id-value>",
  "@type":"Device",
  "displayName":"Airbox",
  "data":{ 
    "$cloudProperties":{ 
        "Color":"blue"
    },
    "EnvironmentalSensor":{ 
      "thsensormodel":{ 
        "reported":{ 
          "value":"Neque quia et voluptatem veritatis assumenda consequuntur quod.",
          "$lastUpdatedTimestamp":"2019-09-30T20:35:43.8478978Z"
        }
      },
      "pm25sensormodel":{ 
        "reported":{ 
          "value":"Aut alias odio.",
          "$lastUpdatedTimestamp":"2019-09-30T20:35:43.8478978Z"
        }
      }
    },
    "urn_azureiot_DeviceManagement_DeviceInformation":{ 
      "totalStorage":{ 
        "reported":{ 
          "value":27900.9730905171,
          "$lastUpdatedTimestamp":"2019-09-30T20:35:43.8478978Z"
        }
      },
      "totalMemory":{ 
        "reported":{ 
          "value":4667.82916715811,
          "$lastUpdatedTimestamp":"2019-09-30T20:35:43.8478978Z"
        }
      }
    }
  },
  "instanceOf":"<template-id>",
  "deviceId":"<device-id>",
  "simulated":true
}
```

### <a name="device-templates"></a>Шаблоны устройств

При первом включении экспорта непрерывных данных экспортируется один моментальный снимок со всеми шаблонами устройств. Каждый шаблон устройства включает в себя:
- идентификатор (`@id`) шаблона устройства;
- имя (`name`) шаблона устройства;
- версию (`version`) шаблона устройства;
- Устройство `capabilityModel`, включая его `interfaces`, а также определения телеметрии, свойств и команд
- определения `cloudProperties`
- Переопределения и начальные значения, встроенные с `capabilityModel`

Новый моментальный снимок записывается один раз в минуту. Моментальный снимок содержит:

- новые шаблоны устройств, добавленные с момента создания последнего моментального снимка; Сюда входят новые версии шаблонов устройств.
- Шаблоны устройств с измененными переопределениями, начальными значениями и определениями облачных свойств с момента создания последнего моментального снимка.

> [!NOTE]
> Шаблоны устройств, удаленные с момента создания последнего моментального снимка, не экспортируются. Сейчас в моментальных снимках нет индикаторов удаленных шаблонов устройств.

Экспортируемые файлы содержат одну строку для каждой записи. В следующем примере показана запись в формате JSON.

```json
{ 
  "@id":"<template-id>",
  "@type":"DeviceModelDefinition",
  "displayName":"Airbox",
  "capabilityModel":{ 
    "@id":"<id>",
    "@type":"CapabilityModel",
    "implements":[ 
      { 
        "@id":"<id>",
        "@type":"InterfaceInstance",
        "name":"EnvironmentalSensor",
        "schema":{ 
          "@id":"<id>",
          "@type":"Interface",
          "comment":"Requires temperature and humidity sensors.",
          "description":"Provides functionality to report temperature, humidity. Provides telemetry, commands and read-write properties",
          "displayName":"Environmental Sensor",
          "contents":[ 
            { 
              "@id":"<id>",
              "@type":"Telemetry",
              "description":"Current temperature on the device",
              "displayName":"Temperature",
              "name":"temp",
              "schema":"double",
              "unit":"Units/Temperature/celsius",
              "valueDetail":{ 
                "@id":"<id>",
                "@type":"ValueDetail/NumberValueDetail",
                "minValue":{ 
                  "@value":"50"
                }
              },
              "visualizationDetail":{ 
                "@id":"<id>",
                "@type":"VisualizationDetail"
              }
            },
            { 
              "@id":"<id>",
              "@type":"Telemetry",
              "description":"Current humidity on the device",
              "displayName":"Humidity",
              "name":"humid",
              "schema":"integer"
            },
            { 
              "@id":"<id>",
              "@type":"Telemetry",
              "description":"Current PM2.5 on the device",
              "displayName":"PM2.5",
              "name":"pm25",
              "schema":"integer"
            },
            { 
              "@id":"<id>",
              "@type":"Property",
              "description":"T&H Sensor Model Name",
              "displayName":"T&H Sensor Model",
              "name":"thsensormodel",
              "schema":"string"
            },
            { 
              "@id":"<id>",
              "@type":"Property",
              "description":"PM2.5 Sensor Model Name",
              "displayName":"PM2.5 Sensor Model",
              "name":"pm25sensormodel",
              "schema":"string"
            }
          ]
        }
      },
      { 
        "@id":"<id>",
        "@type":"InterfaceInstance",
        "name":"urn_azureiot_DeviceManagement_DeviceInformation",
        "schema":{ 
          "@id":"<id>",
          "@type":"Interface",
          "displayName":"Device information",
          "contents":[ 
            { 
              "@id":"<id>",
              "@type":"Property",
              "comment":"Total available storage on the device in kilobytes. Ex. 20480000 kilobytes.",
              "displayName":"Total storage",
              "name":"totalStorage",
              "displayUnit":"kilobytes",
              "schema":"long"
            },
            { 
              "@id":"<id>",
              "@type":"Property",
              "comment":"Total available memory on the device in kilobytes. Ex. 256000 kilobytes.",
              "displayName":"Total memory",
              "name":"totalMemory",
              "displayUnit":"kilobytes",
              "schema":"long"
            }
          ]
        }
      }
    ],
    "displayName":"AAEONAirbox52"
  },
  "solutionModel":{ 
    "@id":"<id>",
    "@type":"SolutionModel",
    "cloudProperties":[ 
      { 
        "@id":"<id>",
        "@type":"CloudProperty",
        "displayName":"Color",
        "name":"Color",
        "schema":"string",
        "valueDetail":{ 
          "@id":"<id>",
          "@type":"ValueDetail/StringValueDetail"
        },
        "visualizationDetail":{ 
          "@id":"<id>",
          "@type":"VisualizationDetail"
        }
      }
    ]
  }
}
```

## <a name="next-steps"></a>Следующие шаги

Теперь, когда вы знаете, как экспортировать данные, перейдите к следующему шагу:

> [!div class="nextstepaction"]
> [Использование моста устройств IoT Central для подключения других облаков Интернета вещей](howto-build-iotc-device-bridge.md)
