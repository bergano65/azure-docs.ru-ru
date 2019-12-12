---
title: Взаимодействие с устройством IoT Plug and Play (предварительная версия) из решения Интернета вещей Azure | Документация Майкрософт
description: В этой статье разработчики решений узнают, как взаимодействовать с устройствами IoT Plug and Play с помощью пакета SDK для служб.
author: Philmea
ms.author: philmea
ms.date: 07/24/2019
ms.topic: tutorial
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: 543f332087aef4147c9274ca980cb56543a68112
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/10/2019
ms.locfileid: "74977596"
---
# <a name="connect-to-and-interact-with-an-iot-plug-and-play-preview-device"></a>Подключение к устройству IoT Plug and Play (предварительная версия) и взаимодействие с ним

В этом руководстве описано, как использовать примеры в пакете SDK для служб Node, демонстрирующие, как ваше решение Интернета вещей может взаимодействовать с устройствами IoT Plug and Play (предварительная версия).

Если вы еще не выполнили инструкции из краткого руководства по [подключению устройства IoT Plug and Play к решению](quickstart-connect-pnp-device-solution.md), это следует сделать сейчас. В кратком руководстве показано, как скачать и установить пакет SDK, а также запустить некоторые примеры.

Прежде чем запустить примеры служб, откройте новый терминал, перейдите в корневую папку клонированного репозитория, после этого в папку **digitaltwins/quickstarts/service**, а затем выполните следующую команду, чтобы установить зависимости:

```cmd/sh
npm install
```

## <a name="run-the-service-samples"></a>Запуск примеров служб

Изучите возможности пакета SDK для службы Node.js с помощью следующих примеров. Задайте переменную среды `IOTHUB_CONNECTION_STRING` в используемой оболочке:

### <a name="retrieve-a-digital-twin-and-list-the-interfaces"></a>Получение цифрового двойника и вывод списка интерфейсов

**get_digital_twin.js** возвращает цифровой двойник, связанный с устройством, и выводит его компонент в командной строке. Для этого не требуется запускать пример устройства.

**get_digital_twin_interface_instance.js** возвращает один экземпляр интерфейса цифрового двойника, связанный с устройством, и выводит его в командной строке. Для этого не требуется запускать пример устройства.

### <a name="get-and-set-properties-using-the-node-service-sdk"></a>Получение и установка свойств с помощью пакета SDK для службы Node

**update_digital_twin.js** обновляет доступное для записи свойство на цифровом двойнике устройства с использованием полного исправления. При необходимости можно обновить несколько свойств в нескольких интерфейсах. Для этого одновременно необходимо запустить пример устройства. В случае успешного запуска пример устройства выводит какие-либо сведения об обновлении свойства, а пример службы выводит обновленный цифровой двойник в терминале.

### <a name="send-a-command-and-retrieve-the-response-using-the-node-service-sdk"></a>Отправка команды и получение ответа с помощью пакета SDK для службы Node

**invoke_command.js** вызывает синхронную команду на цифровом двойнике устройства. Для этого одновременно необходимо запустить пример устройства. В случае успешного запуска пример устройства выводит какие-либо данные о подтверждении команды, а клиент службы выводит результат команды в терминале.

### <a name="connect-to-the-public-repository-and-retrieve-a-model-definition-using-the-node-service-sdk"></a>Подключение к общедоступному репозиторию и получение определения модели с помощью пакета SDK для службы Node

Необходимо задать следующую переменную среды, используя одни и те же инструкции для примеров служб и устройств:

* `AZURE_IOT_MODEL_REPOSITORY_CONNECTION_STRING`

Эту строку подключения можно найти на [портале Microsoft Azure Certified for IoT](https://preview.catalog.azureiotsolutions.com) на вкладке **Строки подключения** для **репозитория компании**.

Строка подключения выглядит следующим образом:

```text
HostName={repo host name};RepositoryId={repo ID};SharedAccessKeyName={repo key ID};SharedAccessKey={repo key secret}
```

После настройки этих четырех переменных среды запустите пример так же, как и остальные примеры:

```cmd/sh
node model_repo.js
```

Этот пример скачивает интерфейс **ModelDiscovery** и выводит эту модель в терминале.

### <a name="run-queries-in-iot-hub-based-on-capability-models-and-interfaces"></a>Выполнение запросов в Центре Интернета вещей на основе моделей возможностей и интерфейсов

Язык запросов Центра Интернета вещей поддерживает `HAS_INTERFACE` и `HAS_CAPABILITYMODEL`, как показано в следующих примерах:

```sql
select * from devices where HAS_INTERFACE('id without version', version)
```

```sql
select * from devices where HAS_CAPABILITYMODEL('id without version', version)
```

### <a name="creating-digital-twin-routes"></a>Создание маршрутов цифрового двойника

Ваше решение может получать уведомления о событиях изменения цифрового двойника. Чтобы подписаться на эти уведомления, используйте [функцию маршрутизации Центра Интернета вещей](../iot-hub/iot-hub-devguide-endpoints.md) для отправки уведомлений в конечную точку, например в хранилище BLOB-объектов, Центры событий или очередь служебной шины.

Чтобы создать маршрут цифрового двойника:

1. Найдите нужный ресурс Центра Интернета вещей на портале Azure.
1. Выберите **Маршрутизация сообщений**.
1. На вкладке **Маршруты** выберите **Добавить**.
1. Введите значение в поле **Имя** и выберите **Конечная точка**. Если вы еще не настроили конечную точку, выберите **Добавить конечную точку**.
1. В раскрывающемся списке **Источник данных** выберите пункт **Digital Twin Change Events** (События изменения цифрового двойника).
1. Щелкните **Сохранить**.

В следующем коде JSON показан пример события изменения цифрового двойника:

```json
{
  "interfaces": {
    "urn_azureiot_ModelDiscovery_DigitalTwin": {
      "name": "urn_azureiot_ModelDiscovery_DigitalTwin",
      "properties": {
        "modelInformation": {
          "reported": {
            "value": {
              "modelId": "urn:domain:capabilitymodel:TestCapability:1",
              "interfaces": {
                "MyInterfaceFoo": "urn:domain:interfaces:FooInterface:1",
                "urn_azureiot_ModelDiscovery_DigitalTwin": "urn:azureiot:ModelDiscovery:DigitalTwin:1"
              }
            }
          }
        }
      }
    },
    "MyInterfaceFoo": {
      "name": "MyInterfaceFoo",
      "properties": {
        "property_1": { "desired": { "value": "value_1" } },
        "property_2": {
          "desired": { "value": 20 },
          "reported": {
            "value": 10,
            "desiredState": {
              "code": 200,
              "version": 22,
              "subCode": 400,
              "description": ""
            }
          }
        },
        "property_3": { "reported": { "value": "value_3" } }
      }
    }
  },
  "version": 4
}
```

## <a name="next-steps"></a>Дополнительная информация

Теперь, когда вы узнали о решениях службы, взаимодействующих с устройствами IoT Plug and Play, далее мы рекомендуем ознакомиться с [обнаружением моделей](concepts-model-discovery.md).
