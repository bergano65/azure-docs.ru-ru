---
title: Развертывание модулей в масштабе с помощью Azure CLI Azure IoT Edge
description: Создание задач автоматического развертывания для групп устройств IoT Edge с помощью расширения IoT для Azure CLI
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/17/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 14c4ddd5d95abb223fb30e2ce07496e7f2773257
ms.sourcegitcommit: 57eb9acf6507d746289efa317a1a5210bd32ca2c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/01/2019
ms.locfileid: "74666024"
---
# <a name="deploy-and-monitor-iot-edge-modules-at-scale-using-the-azure-cli"></a>Развертывание и мониторинг большого числа модулей IoT Edge с помощью Azure CLI

Создайте **IOT Edge автоматическое развертывание** с помощью интерфейса командной строки Azure, чтобы управлять текущими развертываниями для нескольких устройств одновременно. Автоматические развертывания для IoT Edge входят в функцию [автоматического управления устройствами](/azure/iot-hub/iot-hub-automatic-device-management) в центре Интернета вещей. Развертывания — это динамические процессы, которые позволяют развертывать несколько модулей на нескольких устройствах, контролировать состояние и работоспособность модулей, а также вносить изменения при необходимости. 

Дополнительные сведения см. в статье [IOT Edge автоматическое развертывание для отдельных устройств или в масштабе](module-deployment-monitoring.md).

С помощью этого руководства вы настроите Azure CLI и расширение Интернета вещей. Затем вы узнаете, как развертывать модули в наборе IoT Edge устройств и отслеживать ход выполнения с помощью доступных команд CLI.

## <a name="cli-prerequisites"></a>Технические условия CLI

* [Центр Интернета вещей](../iot-hub/iot-hub-create-using-cli.md) в подписке Azure. 
* [Устройство IoT Edge](how-to-register-device.md#prerequisites-for-the-azure-cli) с установленной средой выполнения IoT Edge.
* [Интерфейс командной строки Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) в вашей среде. Вам понадобится как минимум Azure CLI версии 2.0.24 или более поздней. Для проверки используйте `az --version`. Эта версия поддерживает команды расширения az и представляет собой платформу команд Knack. 
* [Расширение Интернета вещей для Azure CLI](https://github.com/Azure/azure-iot-cli-extension).

## <a name="configure-a-deployment-manifest"></a>Настройка манифеста развертывания

Манифест развертывания — это документ JSON, в котором определены развертываемые модули, способ передачи данных между этими модулями и требуемые свойства для двойников модулей. Дополнительные сведения см. в статьях о [развертывании модулей и установке маршрутов в IOT Edge](module-composition.md).

Чтобы развернуть модули с помощью Azure CLI, сохраните манифест развертывания на локальный компьютер в формате TXT. Путь к файлу используется в следующем разделе при выполнении команды для применения конфигурации к устройству. 

Вот пример простого манифеста развертывания с одним модулем:

```json
{
  "content": {
    "modulesContent": {
      "$edgeAgent": {
        "properties.desired": {
          "schemaVersion": "1.0",
          "runtime": {
            "type": "docker",
            "settings": {
              "minDockerVersion": "v1.25",
              "loggingOptions": "",
              "registryCredentials": {
                "registryName": {
                  "username": "",
                  "password": "",
                  "address": ""
                }
              }
            }
          },
          "systemModules": {
            "edgeAgent": {
              "type": "docker",
              "settings": {
                "image": "mcr.microsoft.com/azureiotedge-agent:1.0",
                "createOptions": "{}"
              }
            },
            "edgeHub": {
              "type": "docker",
              "status": "running",
              "restartPolicy": "always",
              "settings": {
                "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
                "createOptions": "{}"
              }
            }
          },
          "modules": {
            "SimulatedTemperatureSensor": {
              "version": "1.0",
              "type": "docker",
              "status": "running",
              "restartPolicy": "always",
              "settings": {
                "image": "mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0",
                "createOptions": "{}"
              }
            }
          }
        }
      },
      "$edgeHub": {
        "properties.desired": {
          "schemaVersion": "1.0",
          "routes": {
            "route": "FROM /* INTO $upstream"
          },
          "storeAndForwardConfiguration": {
            "timeToLiveSecs": 7200
          }
        }
      },
      "SimulatedTemperatureSensor": {
        "properties.desired": {}
      }
    }
  }
}
```

## <a name="identify-devices-using-tags"></a>Определение устройств с помощью тегов

Перед созданием развертывания необходимо указать устройства, на которые вы хотите повлиять. Azure IoT Edge определяет устройства с помощью **тегов** в двойнике устройства. Каждое устройство может иметь несколько тегов, которые определяются любым способом, имеющим смысл для вашего решения. Например, при управлении кампусом интеллектуальных зданий можно добавлять к устройству следующие теги:

```json
"tags":{
  "location":{
    "building": "20",
    "floor": "2"
  },
  "roomtype": "conference",
  "environment": "prod"
}
```

Дополнительные сведения о двойниках устройств и тегах см. в статье [Общие сведения о двойниках устройств и их использование в Центре Интернета вещей](../iot-hub/iot-hub-devguide-device-twins.md).

## <a name="create-a-deployment"></a>Создание развертывания

Развертывание модулей для целевых устройств происходит при помощи создания развертывания, которое состоит из манифеста развертывания, а также других параметров. 

Для создания развертывания используйте команду [AZ IOT ребра Deployment Create](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-create) .

```cli
az iot edge deployment create --deployment-id [deployment id] --hub-name [hub name] --content [file path] --labels "[labels]" --target-condition "[target query]" --priority [int]
```

Команда развертывания создать принимает следующие параметры: 

* **--deployment-id** — имя развертывания, которое будет создано в центре IoT. Присвойте своему развертыванию уникальное имя, содержащее до 128 букв в нижнем регистре. Не используйте пробелы и следующие недопустимые символы: `& ^ [ ] { } \ | " < > /`.
* **--hub-name** — имя центра IoT, в котором будет создано развертывание. Центр должен быть в текущей подписке. Измените текущую подписку с помощью команды `az account set -s [subscription name]`.
* **--content** — путь к файлу манифеста развертывания JSON. 
* **--labels** — добавление меток для отслеживания развертываний. Метка представляет собой пару имя и значение, которые описывают развертывание. Метки имеют формат JSON для имен и значений. Например `{"HostPlatform":"Linux", "Version:"3.0.1"}`.
* **--target-condition** — введите целевое условие, чтобы определить, для каких устройств будет предназначено это развертывание. Условие основано на тегах двойникаа устройства или сообщаемых свойствах двойникаа устройства и должно соответствовать формату выражения. Например, `tags.environment='test' and properties.reported.devicemodel='4000x'`. 
* **--priority** — положительные целые числа. Если одному устройству назначены два или несколько развертываний, будет выбрано развертывание с самым высоким числовым значением параметра приоритета.

## <a name="monitor-a-deployment"></a>Мониторинг развертывания

Используйте команду [AZ IOT ребра Deployment Показать](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-show) , чтобы отобразить сведения об одном развертывании:

```cli
az iot edge deployment show --deployment-id [deployment id] --hub-name [hub name]
```

Команда развернуть показ принимает следующие параметры:
* **--deployment-id** — имя существующего развертывания в центре IoT.
* **--hub-name** — имя концентратора IoT, в котором существует развертывание. Центр должен быть в текущей подписке. Переключитесь на нужную подписку с помощью команды `az account set -s [subscription name]`

Проверьте развертывание в командном окне. Свойство **метрики** содержит счетчик для каждой метрики, оцениваемой каждым концентратором.

* **targetedCount** — это система метрики, которая определяет количество двойников устройств в Центре Интернета вещей, которые соответствуют условию назначения.
* **appliedCount** — это система метрики, которая определяет количество устройств, в которых применено содержимое развертывания к двойникам модулей в Центре Интернета вещей.
* **репортедсукцессфулкаунт** — метрика устройства, указывающая количество IOT Edge устройств в развертывании, сообщающих об успешном выполнении из среды выполнения клиента IOT Edge.
* **репортедфаиледкаунт** — метрика устройства, указывающая количество IOT Edge устройств в случае сбоя отчетов о развертывании из среды выполнения клиента IOT Edge.

Список идентификаторов устройств или объектов для каждой из метрик можно просмотреть с помощью команды [AZ IOT ребра Deployment-метрика](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-show-metric) :

```cli
az iot edge deployment show-metric --deployment-id [deployment id] --metric-id [metric id] --hub-name [hub name] 
```

Команда Deployment демонстрация — метрика принимает следующие параметры: 
* **--deployment-id** — имя существующего развертывания в центре IoT.
* **--metric-id** — имя метрики, для которой нужно отобразить список идентификаторов устройств, например `reportedFailedCount`
* **--hub-name** — имя концентратора IoT, в котором существует развертывание. Центр должен быть в текущей подписке. Переключитесь на нужную подписку с помощью команды `az account set -s [subscription name]`

## <a name="modify-a-deployment"></a>Изменение развертывания

При изменении развертывания изменения немедленно реплицируются во все целевые устройства. 

Если обновить целевое условие, произойдут следующие обновления:

* Если устройство не соответствует предыдущему условию назначения, однако соответствует новому и это развертывание имеет самый высокий приоритет для устройства, то оно будет применено к устройству. 
* Если устройство, на котором сейчас выполняется развертывание, больше не соответствует условию назначения, оно удаляет это развертывание и выполняет следующее развертывание с самым высоким приоритетом. 
* Если устройство, на котором сейчас выполняется развертывание, больше не соответствует условию назначения, а также условию назначения любого другого развертывания, в устройстве не происходит никаких изменений. Устройство продолжает выполнять текущие модули в их актуальном состоянии, однако больше не управляется как часть этого развертывания. Если устройство соответствует условию назначения любого другого развертывания, оно удаляет это развертывание и выполняет новое. 

Для обновления развертывания используйте команду [AZ IOT ребра Update](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-update) .

```cli
az iot edge deployment update --deployment-id [deployment id] --hub-name [hub name] --set [property1.property2='value']
```

Команда обновления развертывания принимает следующие параметры:
* **--deployment-id** — имя существующего развертывания в центре IoT.
* **--hub-name** — имя концентратора IoT, в котором существует развертывание. Центр должен быть в текущей подписке. Переключитесь на нужную подписку с помощью команды `az account set -s [subscription name]`
* **--set** — обновление свойства в развертывании. Можно обновлять следующие свойства.
  * targetCondition — например `targetCondition=tags.location.state='Oregon'`
  * метки; 
  * priority


## <a name="delete-a-deployment"></a>Удаление развертывания

При удалении развертывания все устройства выполняют следующее развертывание, которое имеет наивысший приоритет. Если устройства не соответствуют условиям назначения любого другого развертывания, то модули не будут удалены при удалении развертывания. 

Чтобы удалить развертывание, используйте команду [AZ IOT ребра Delete](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-delete) :

```cli
az iot edge deployment delete --deployment-id [deployment id] --hub-name [hub name] 
```

Команда удаления развертывания принимает следующие параметры: 
* **--deployment-id** — имя существующего развертывания в центре IoT.
* **--hub-name** — имя концентратора IoT, в котором существует развертывание. Центр должен быть в текущей подписке. Переключитесь на нужную подписку с помощью команды `az account set -s [subscription name]`

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о [развертывании модулей на IOT Edge устройствах](module-deployment-monitoring.md).
