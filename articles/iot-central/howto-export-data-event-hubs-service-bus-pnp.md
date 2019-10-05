---
title: Экспорт данных в Центры событий Azure и Служебную шину Azure | Документация Майкрософт
description: Как экспортировать данные из приложения Azure IoT Central в Центры событий Azure и Служебную шину Azure
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 07/09/2019
ms.topic: conceptual
ms.service: iot-central
manager: peterpr
ms.openlocfilehash: fed9c924274cb66671e233a7dc6d431d81e0dbfb
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/05/2019
ms.locfileid: "71973222"
---
# <a name="export-your-data-in-azure-iot-central-preview-features"></a>Экспорт данных в IoT Central Azure (Предварительная версия функций)

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

*Эта статья предназначена для администраторов.*

В этой статье описывается, как использовать функцию непрерывного экспорта данных в Azure IoT Central для экспорта данных в собственные **концентраторы событий Azure**и экземпляры служебной **шины Azure** . Вы можете экспортировать **телеметрию**, **устройства**и **шаблоны устройств** в собственное место назначения для получения сведений о теплом пути и аналитики. Сюда входит активация настраиваемых правил в Azure Stream Analytics, активация пользовательских рабочих процессов в Azure Logic Apps или передача их через функции Azure для преобразования.

> [!Note]
> После включения непрерывного экспорта данных вы получаете только данные, поступающие с момента включения. Сейчас невозможно получить данные за то время, когда непрерывный экспорт данных был отключен. Чтобы сохранить больше исторических данных, включите экспорт непрерывных данных раньше.

## <a name="prerequisites"></a>Предварительные требования

- Вы должны быть администратором в своем приложении IoT Central.

## <a name="set-up-export-destination"></a>Настройка места назначения экспорта

Если у вас нет существующих концентраторов событий или служебной шины для экспорта, выполните следующие действия, чтобы создать ее.

### <a name="create-event-hubs-namespace"></a>Создание пространства имен Центров событий

1. Создайте [пространство имен Центров событий на портале Azure](https://ms.portal.azure.com/#create/Microsoft.EventHub). Дополнительные сведения см. в [документации по Центрам событий Azure](https://docs.microsoft.com/azure/event-hubs/event-hubs-create).
2. Выберите подписку. 

    > [!Note] 
    > Теперь вы можете экспортировать данные в другие подписки, которые **отличаются** от подписки приложения IoT Central с оплатой по мере использования. В этом случае вы подключаетесь с использованием строки подключения.
3. Создайте концентратор событий в пространстве имен в Центрах событий. Перейдите к пространству имен и выберите **+ Концентратор событий** в верхней части, чтобы создать экземпляр концентратора событий.

### <a name="create-service-bus-namespace"></a>Создание пространства имен Служебной шины

1. Создайте [пространство имен Служебной шины на портале Azure](https://ms.portal.azure.com/#create/Microsoft.ServiceBus.1.0.5). Дополнительные сведения см. в [документации по Служебной шине Azure](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-create-namespace-portal).
2. Выберите подписку. 

    > [!Note] 
    > Теперь вы можете экспортировать данные в другие подписки, которые **отличаются** от подписки приложения IoT Central с оплатой по мере использования. В этом случае вы подключаетесь с использованием строки подключения.

3. Перейдите к пространству имен Служебной шины и выберите **+ Очередь** или **+ Раздел** в верхней части, чтобы создать очередь или раздел для экспорта.


## <a name="set-up-continuous-data-export"></a>Настройка экспорта непрерывных данных

Теперь, когда у вас есть концентраторы событий или назначение служебной шины для экспорта данных, выполните следующие действия, чтобы настроить непрерывный экспорт данных. 

1. Войдите в приложение IoT Central.

2. В меню слева выберите **Экспорт данных**.

    > [!Note]
    > Если вы не видите элемент Экспорт данных в меню слева, вы не являетесь администратором в приложении. Обратитесь к администратору, чтобы настроить экспорт данных.

3. Нажмите кнопку **+ создать** в правом верхнем углу. Выберите один из **концентраторов событий Azure** или **служебную шину Azure** в качестве назначения для экспорта. 

    > [!NOTE] 
    > Максимальное число экспортов на каждое приложение равно пяти. 

    ![Создание непрерывного экспорта данных](media/howto-export-data-pnp/export-new2.png)

4. В раскрывающемся списке выберите пространство имен **концентраторов событий или пространство имен служебной шины**. Вы также можете выбрать последний вариант в списке — **Enter a connection string** (Ввести строку подключения). 

    > [!NOTE] 
    > Вы увидите учетные записи хранения, пространства имен Центров событий или пространства имен Служебной шины, которые доступны **в той же подписке, что и ваше приложение IoT Central**. Если вы хотите выполнить экспорт за пределы этой подписки, выберите **Enter a connection string** (Ввести строку подключения) и перейдите к шагу 5.

    > [!NOTE] 
    > Для приложений с 7-дневной бесплатной пробной версией единственным способом настроить непрерывный экспорт данных — использовать строку подключения. Это обусловлено тем, что приложения с 7-дневной бесплатной пробной версией не связаны с подпиской Azure.

    ![Создание нового концентратора событий для непрерывного экспорта данных](media/howto-export-data-pnp/export-eh.png)

5. (Необязательно.) Если вы выбрали **Enter a connection string** (Ввести строку подключения), открывается новое окно, в которое нужно вставить строку подключения. Чтобы получить строку подключения, сделайте следующее:
    - Концентраторы событий или служебная шина, перейдите к пространству имен в портал Azure.
        - В разделе **Параметры**выберите **политики общего доступа** .
        - Выберите значение по умолчанию **RootManageSharedAccessKey** или создайте политику.
        - Скопируйте основную или дополнительную строку подключения.

6. Выберите концентратор событий, очередь или раздел из раскрывающегося списка.

7. В разделе **Data to export** (Данные для экспорта) укажите каждый тип данных для экспорта, задав для типа значение **Включено**.

8. Чтобы включить непрерывный экспорт данных, убедитесь, **что включен режим** **экспорта данных** . Щелкните **Сохранить**.

9. Через несколько минут ваши данные будут отображаться в выбранном назначении.


## <a name="data-format"></a>Формат данных

Данные телеметрии, устройств и шаблонов устройств экспортируются в концентратор событий или в очередь или раздел служебной шины практически в реальном времени. Экспортированные данные телеметрии содержат все сообщения, отправленные в IoT Central, а не только сами значения телеметрии. Данные экспортированных устройств содержат изменения свойств и метаданных всех устройств, а экспортированные шаблоны устройств содержат изменения во всех шаблонах устройств. Экспортированные данные находятся в формате JSON в свойстве body.

> [!NOTE]
> При выборе Служебной шины Azure как назначения экспорта для очередей и разделов **не должна быть включена функция обнаружения повторяющихся данных или сеансов**. Если любая из этих функций включена, некоторые сообщения не будут поступать в очередь или раздел.

### <a name="telemetry"></a>Телеметрия

Новое сообщение экспортируется сразу после того, как IoT Central получает сообщения с устройства. Каждое сообщение, экспортированное в Центры событий Azure и Служебную шину Azure, содержит полное сообщение, отправленное устройством, в свойстве body в формате JSON. 

> [!NOTE]
> Устройства, отправляющие данные телеметрии, представлены идентификаторами устройств (см. следующие разделы). Чтобы получить имена устройств, экспортируйте данные устройств и сопоставьте каждое сообщение на основе **connectionDeviceId**, соответствующего свойству **deviceId** в сообщении устройства.

В следующем примере показано сообщение о данных телеметрии, полученных в концентраторе событий или в очереди или разделе служебной шины.

```json
{ 
  "body":{ 
    "temp":67.96099945281145,
    "humid":58.51139305465015,
    "pm25":36.91162432340187
  },
  "annotations":{ 
    "iothub-connection-device-id":"<deviceId>",
    "iothub-connection-auth-method":"{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
    "iothub-connection-auth-generation-id":"<generationId>",
    "iothub-enqueuedtime":1539381029965,
    "iothub-message-source":"Telemetry",
    "x-opt-sequence-number":25325,
    "x-opt-offset":"<offset>",
    "x-opt-enqueued-time":1539381030200
  },
  "sequenceNumber":25325,
  "enqueuedTimeUtc":"2018-10-12T21:50:30.200Z",
  "offset":"<offset>",
  "properties":{ 
    "content_type":"application/json",
    "content_encoding":"utf-8"
  }
}
```

### <a name="devices"></a>Устройств

Сообщения, содержащие данные устройств, отправляются в концентратор событий либо очередь или раздел служебной шины каждые несколько минут. Это означает, что каждые несколько минут поступают пакеты сообщений с данными о:
- новых устройствах, которые были добавлены;
- Устройства с измененными значениями свойств

Каждое сообщение уведомляет об одном или нескольких изменениях устройства с момента последнего экспортированного сообщения. Данные, которые будут переданы в каждом сообщении:
- идентификатор (`@id`) устройства в IoT Central;
- имя (`name`) устройства;
- идентификатор устройства (`deviceId`) из [Службы подготовки устройств](https://aka.ms/iotcentraldocsdps);
- сведения о шаблоне устройства;
- Значения свойств

> [!NOTE]
> Устройства, удаленные с момента создания последнего пакета сообщений, не экспортируются. В настоящее время в сообщениях экспорта нет никаких индикаторов касательно удаленных устройств.
>
> Шаблон устройства, к которому принадлежит каждое устройство, представлен идентификатором шаблона устройства. Чтобы получить имя шаблона устройства, обязательно экспортируйте и данные шаблона устройства.

В следующем примере показано сообщение с данными устройства в концентраторе событий, очереди или разделе служебной шины:


```json
{ 
  "body":{ 
    "@id":"<id>",
    "@type":"Device",
    "displayName":"Airbox - 266d30aedn5",
    "data":{ 
      "$cloudProperties":{ 
        "Color":"blue"
      },
      "EnvironmentalSensor":{ 
        "thsensormodel":{ 
          "reported":{ 
            "value":"A1",
            "$lastUpdatedTimestamp":"2019-10-02T18:14:49.3820326Z"
          }
        },
        "pm25sensormodel":{ 
          "reported":{ 
            "value":"P1",
            "$lastUpdatedTimestamp":"2019-10-02T18:14:49.3820326Z"
          }
        }
      },
      "urn_azureiot_DeviceManagement_DeviceInformation":{ 
        "totalStorage":{ 
          "reported":{ 
            "value":3088.1959855710156,
            "$lastUpdatedTimestamp":"2019-10-02T18:14:49.3820326Z"
          }
        },
        "totalMemory":{ 
          "reported":{ 
            "value":16005.703586477555,
            "$lastUpdatedTimestamp":"2019-10-02T18:14:49.3820326Z"
          }
        }
      }
    },
    "instanceOf":"<templateId>",
    "deviceId":"<deviceId>",
    "simulated":true
  },
  "annotations":{ 
    "iotcentral-message-source":"devices",
    "x-opt-partition-key":"<partitionKey>",
    "x-opt-sequence-number":39740,
    "x-opt-offset":"<offset>",
    "x-opt-enqueued-time":1539274959654
  },
  "partitionKey":"<partitionKey>",
  "sequenceNumber":39740,
  "enqueuedTimeUtc":"2019-10-02T18:14:49.3820326Z",
  "offset":"<offset>"
}
```

### <a name="device-templates"></a>Шаблоны устройств

Сообщения, содержащие данные шаблонов устройств, отправляются в концентратор событий, очередь или раздел служебной шины каждые несколько минут. Это означает, что каждые несколько минут поступают пакеты сообщений с данными о:
- Новые добавленные или обновленные шаблоны устройств
- Шаблоны устройств с измененными Капабилитимоделс, Клаудпропертиес, переопределениями и начальными значениями

Каждое сообщение уведомляет об одном или нескольких изменениях шаблона устройства с момента последнего экспортированного сообщения. Данные, которые будут переданы в каждом сообщении:
- версию (`@id`) шаблона устройства;
- имя (`name`) шаблона устройства;
- версию (`version`) шаблона устройства;
- Устройство `capabilityModel`, включая его `interfaces`, а также определения телеметрии, свойств и команд
- определения `cloudProperties`
- Переопределения и начальные значения, встроенные с `capabilityModel`

> [!NOTE]
> Шаблоны устройств, удаленные с момента создания последнего пакета сообщений, не экспортируются. В настоящее время в сообщениях экспорта нет никаких индикаторов касательно удаленных шаблонов устройств.

В следующем примере показано сообщение шаблона устройства в концентраторе событий или в очереди или разделе служебной шины:

```json
{ 
  "body":{ 
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
    },
    "annotations":{ 
      "iotcentral-message-source":"deviceTemplates",
      "x-opt-partition-key":"<partitionKey>",
      "x-opt-sequence-number":25315,
      "x-opt-offset":"<offset>",
      "x-opt-enqueued-time":1539274985085
    },
    "partitionKey":"<partitionKey>",
    "sequenceNumber":25315,
    "enqueuedTimeUtc":"2019-10-02T16:23:05.085Z",
    "offset":"<offset>"
  }
}
```

## <a name="next-steps"></a>Следующие шаги

Теперь, когда вы знаете, как экспортировать данные в Центры событий Azure и Служебную шину Azure, переходите к следующему шагу:

> [!div class="nextstepaction"]
> [Активация Функций Azure с помощью веб-перехватчиков в Azure IoT Central](howto-trigger-azure-functions.md)
