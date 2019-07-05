---
title: Создание задачи автоматического развертывания с помощью командной строки — Azure IoT Edge | Документация Майкрософт
description: Создание задач автоматического развертывания для групп устройств IoT Edge с помощью расширения IoT для Azure CLI
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/17/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 39edbc77215d3a4f6477beae3be9d7d47cbba4f0
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/03/2019
ms.locfileid: "67540921"
---
# <a name="deploy-and-monitor-iot-edge-modules-at-scale-using-the-azure-cli"></a>Развертывание и мониторинг большого числа модулей IoT Edge с помощью Azure CLI

Создание **автоматического развертывания IoT Edge** с помощью интерфейса командной строки Azure, чтобы управлять развертыванием для многих устройств за один раз. Автоматическое развертывание IoT Edge являются частью [управления автоматической](/azure/iot-hub/iot-hub-automatic-device-management) функции центра Интернета вещей. Развертывания — это динамические процессы, которые позволяют развертывать несколько модулей на несколько устройств, отслеживать состояние и работоспособность модулей и вносить изменения при необходимости. 

Дополнительные сведения см. в разделе [автоматического развертывания понять IoT Edge для отдельных устройств или в требуемом масштабе](module-deployment-monitoring.md).

С помощью этого руководства вы настроите Azure CLI и расширение Интернета вещей. Затем вы узнаете, как развертывание модулей на наборе устройств IoT Edge и отслеживать ход выполнения, с помощью доступных команд интерфейса командной строки.

## <a name="cli-prerequisites"></a>Технические условия CLI

* [Центр Интернета вещей](../iot-hub/iot-hub-create-using-cli.md) в подписке Azure. 
* [Устройство IoT Edge](how-to-register-device-cli.md) с установленной средой выполнения IoT Edge.
* [Интерфейс командной строки Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) в вашей среде. Вам понадобится как минимум Azure CLI версии 2.0.24 или более поздней. Для проверки используйте `az –-version`. Эта версия поддерживает команды расширения az и представляет собой платформу команд Knack. 
* [Расширение Интернета вещей для Azure CLI](https://github.com/Azure/azure-iot-cli-extension).

## <a name="configure-a-deployment-manifest"></a>Настройка манифеста развертывания

Манифест развертывания — это документ JSON, в котором определены развертываемые модули, способ передачи данных между этими модулями и требуемые свойства для двойников модулей. Дополнительные сведения см. в разделе [сведения о развертывании модулей и устанавливать маршруты в IoT Edge](module-composition.md).

Чтобы развернуть модули с помощью Azure CLI, сохраните манифест развертывания на локальный компьютер в формате TXT. Вы можете использовать путь к файлу в следующем разделе, при выполнении команды, чтобы применить конфигурацию к устройству. 

Вот пример простого манифеста развертывания с одним модулем.

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
            "tempSensor": {
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
      "tempSensor": {
        "properties.desired": {}
      }
    }
  }
}
```

## <a name="identify-devices-using-tags"></a>Определение устройств с помощью тегов

Перед созданием развертывания необходимо указать устройства, на которые вы хотите повлиять. Azure IoT Edge определяет устройства с помощью **тегов** в двойнике устройства. Каждое устройство может иметь несколько тегов, которые определяют способом, который лучше всего подходит для вашего решения. Например, при управлении кампусом интеллектуальных зданий можно добавлять к устройству следующие теги.

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

Используйте [создать развертывание iot edge az](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-create) команду, чтобы создать развертывание:

```cli
az iot edge deployment create --deployment-id [deployment id] --hub-name [hub name] --content [file path] --labels "[labels]" --target-condition "[target query]" --priority [int]
```

Развертывания создайте команда принимает следующие параметры: 

* **--deployment-id** — имя развертывания, которое будет создано в центре IoT. Присвойте своему развертыванию уникальное имя, содержащее до 128 букв в нижнем регистре. Не используйте пробелы и следующие недопустимые символы: `& ^ [ ] { } \ | " < > /`.
* **--hub-name** — имя центра IoT, в котором будет создано развертывание. Центр должен быть в текущей подписке. Изменение текущей подписки с `az account set -s [subscription name]` команды.
* **--content** — путь к файлу манифеста развертывания JSON. 
* **--labels** — добавление меток для отслеживания развертываний. Метка представляет собой пару имя и значение, которые описывают развертывание. Метки имеют формат JSON для имен и значений. Например: `{"HostPlatform":"Linux", "Version:"3.0.1"}`
* **--target-condition** — введите целевое условие, чтобы определить, для каких устройств будет предназначено это развертывание. Условие основано на тегах двойника устройства или его сообщаемых свойствах и должно соответствовать формату выражения. Например, `tags.environment='test' and properties.reported.devicemodel='4000x'`. 
* **--priority** — положительные целые числа. Если одному устройству назначены два или более развертывания, будет применяться развертывание с самым высоким числовым значением параметра Priority.

## <a name="monitor-a-deployment"></a>Мониторинг развертывания

Используйте [az iot edge развертывания №](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-show) команду, чтобы отобразить сведения о одного развертывания:

```cli
az iot edge deployment show --deployment-id [deployment id] --hub-name [hub name]
```

Развертывание Показать команда принимает следующие параметры:
* **--deployment-id** — имя существующего развертывания в центре IoT.
* **--hub-name** — имя концентратора IoT, в котором существует развертывание. Центр должен быть в текущей подписке. Переключитесь на нужную подписку с помощью команды `az account set -s [subscription name]`

Проверьте развертывание в командном окне. Свойство **metrics** перечисляет количество каждой метрики, которое оценивается каждым центром.

* **targetedCount** — это система метрики, которая определяет количество двойников устройств в Центре Интернета вещей, которые соответствуют условию назначения.
* **appliedCount** — это система метрики, которая определяет количество устройств, в которых применено содержимое развертывания к двойникам модулей в Центре Интернета вещей.
* **reportedSuccessfulCount** -метрики устройства, указывающее количество устройств IoT Edge в развертывании, сообщение об успехе от в клиентскую среду выполнения IoT Edge.
* **reportedFailedCount** -метрики устройства, указывающее количество устройств IoT Edge в развертывании, ошибкой в клиентскую среду выполнения IoT Edge.

Можно вывести список кодов устройств или объекты для каждого из показателей с помощью [az iot edge развертывания show метрика](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-show-metric) команды:

```cli
az iot edge deployment show-metric --deployment-id [deployment id] --metric-id [metric id] --hub-name [hub name] 
```

Команда show метрика развертывания со следующими параметрами: 
* **--deployment-id** — имя существующего развертывания в центре IoT.
* **--metric-id** — имя метрики, для которой нужно отобразить список идентификаторов устройств, например `reportedFailedCount`
* **--hub-name** — имя концентратора IoT, в котором существует развертывание. Центр должен быть в текущей подписке. Переключитесь на нужную подписку с помощью команды `az account set -s [subscription name]`

## <a name="modify-a-deployment"></a>Изменение развертывания

При изменении развертывания изменения немедленно реплицируются во все целевые устройства. 

Если обновить целевое условие, произойдут следующие обновления:

* Если устройство не соответствует предыдущему условию назначения, однако соответствует новому и это развертывание имеет самый высокий приоритет для устройства, то оно будет применено к устройству. 
* Если устройство, на котором сейчас выполняется развертывание, больше не соответствует условию назначения, оно удаляет это развертывание и выполняет следующее развертывание с самым высоким приоритетом. 
* Если устройство, на котором сейчас выполняется развертывание, больше не соответствует условию назначения, а также условию назначения любого другого развертывания, в устройстве не происходит никаких изменений. Устройство продолжает выполнять текущие модули в их актуальном состоянии, однако больше не управляется как часть этого развертывания. Если устройство соответствует условию назначения любого другого развертывания, оно удаляет это развертывание и выполняет новое. 

Используйте [az iot edge deployment обновлением](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-update) команду для обновления развертывания:

```cli
az iot edge deployment update --deployment-id [deployment id] --hub-name [hub name] --set [property1.property2='value']
```

Команда обновления развертывания со следующими параметрами:
* **--deployment-id** — имя существующего развертывания в центре IoT.
* **--hub-name** — имя концентратора IoT, в котором существует развертывание. Центр должен быть в текущей подписке. Переключитесь на нужную подписку с помощью команды `az account set -s [subscription name]`
* **--set** — обновление свойства в развертывании. Можно обновлять следующие свойства.
  * targetCondition — например `targetCondition=tags.location.state='Oregon'`
  * метки; 
  * приоритет


## <a name="delete-a-deployment"></a>Удаление развертывания

При удалении развертывания все устройства выполняют следующее развертывание, которое имеет наивысший приоритет. Если устройства не соответствуют условиям назначения любого другого развертывания, то модули не будут удалены при удалении развертывания. 

Используйте [удалить развертывание iot edge az](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-delete) команду, чтобы удалить развертывание:

```cli
az iot edge deployment delete --deployment-id [deployment id] --hub-name [hub name] 
```

Команда delete развертывания со следующими параметрами: 
* **--deployment-id** — имя существующего развертывания в центре IoT.
* **--hub-name** — имя концентратора IoT, в котором существует развертывание. Центр должен быть в текущей подписке. Переключитесь на нужную подписку с помощью команды `az account set -s [subscription name]`

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о [развертывании модулей на устройствах IoT Edge](module-deployment-monitoring.md).
