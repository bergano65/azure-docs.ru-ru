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
ms.openlocfilehash: 68cc82733bb264eedb96239e7353ac30224bda64
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/24/2019
ms.locfileid: "74457395"
---
# <a name="deploy-and-monitor-iot-edge-modules-at-scale-using-the-azure-cli"></a>Развертывание и мониторинг большого числа модулей IoT Edge с помощью Azure CLI

Create an **IoT Edge automatic deployment** using the Azure command-line interface to manage ongoing deployments for many devices at once. Automatic deployments for IoT Edge are part of the [automatic device management](/azure/iot-hub/iot-hub-automatic-device-management) feature of IoT Hub. Deployments are dynamic processes that enable you to deploy multiple modules to multiple devices, track the status and health of the modules, and make changes when necessary. 

For more information, see [Understand IoT Edge automatic deployments for single devices or at scale](module-deployment-monitoring.md).

С помощью этого руководства вы настроите Azure CLI и расширение Интернета вещей. You then learn how to deploy modules to a set of IoT Edge devices and monitor the progress using the available CLI commands.

## <a name="cli-prerequisites"></a>Технические условия CLI

* [Центр Интернета вещей](../iot-hub/iot-hub-create-using-cli.md) в подписке Azure. 
* [Устройство IoT Edge](how-to-register-device.md#prerequisites-for-the-azure-cli) с установленной средой выполнения IoT Edge.
* [Интерфейс командной строки Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) в вашей среде. Вам понадобится как минимум Azure CLI версии 2.0.24 или более поздней. Для проверки используйте `az --version`. Эта версия поддерживает команды расширения az и представляет собой платформу команд Knack. 
* [Расширение Интернета вещей для Azure CLI](https://github.com/Azure/azure-iot-cli-extension).

## <a name="configure-a-deployment-manifest"></a>Настройка манифеста развертывания

Манифест развертывания — это документ JSON, в котором определены развертываемые модули, способ передачи данных между этими модулями и требуемые свойства для двойников модулей. For more information, see [Learn how to deploy modules and establish routes in IoT Edge](module-composition.md).

Чтобы развернуть модули с помощью Azure CLI, сохраните манифест развертывания на локальный компьютер в формате TXT. You use the file path in the next section when you run the command to apply the configuration to your device. 

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

Перед созданием развертывания необходимо указать устройства, на которые вы хотите повлиять. Azure IoT Edge определяет устройства с помощью **тегов** в двойнике устройства. Each device can have multiple tags that you define in any way that makes sense for your solution. Например, при управлении кампусом интеллектуальных зданий можно добавлять к устройству следующие теги:

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

Use the [az iot edge deployment create](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-create) command to create a deployment:

```cli
az iot edge deployment create --deployment-id [deployment id] --hub-name [hub name] --content [file path] --labels "[labels]" --target-condition "[target query]" --priority [int]
```

The deployment create command takes the following parameters: 

* **--deployment-id** — имя развертывания, которое будет создано в центре IoT. Присвойте своему развертыванию уникальное имя, содержащее до 128 букв в нижнем регистре. Не используйте пробелы и следующие недопустимые символы: `& ^ [ ] { } \ | " < > /`.
* **--hub-name** — имя центра IoT, в котором будет создано развертывание. Центр должен быть в текущей подписке. Change your current subscription with the `az account set -s [subscription name]` command.
* **--content** — путь к файлу манифеста развертывания JSON. 
* **--labels** — добавление меток для отслеживания развертываний. Метка представляет собой пару имя и значение, которые описывают развертывание. Метки имеют формат JSON для имен и значений. Например `{"HostPlatform":"Linux", "Version:"3.0.1"}`.
* **--target-condition** — введите целевое условие, чтобы определить, для каких устройств будет предназначено это развертывание. The condition is based on device twin tags or device twin reported properties and should match the expression format. For example, `tags.environment='test' and properties.reported.devicemodel='4000x'`. 
* **--priority** — положительные целые числа. Если одному устройству назначены два или несколько развертываний, будет выбрано развертывание с самым высоким числовым значением параметра приоритета.

## <a name="monitor-a-deployment"></a>Мониторинг развертывания

Use the [az iot edge deployment show](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-show) command to display the details of a single deployment:

```cli
az iot edge deployment show --deployment-id [deployment id] --hub-name [hub name]
```

The deployment show command takes the following parameters:
* **--deployment-id** — имя существующего развертывания в центре IoT.
* **--hub-name** — имя концентратора IoT, в котором существует развертывание. Центр должен быть в текущей подписке. Переключитесь на нужную подписку с помощью команды `az account set -s [subscription name]`

Проверьте развертывание в командном окне. The **metrics** property lists a count for each metric that is evaluated by each hub:

* **targetedCount** — это система метрики, которая определяет количество двойников устройств в Центре Интернета вещей, которые соответствуют условию назначения.
* **appliedCount** — это система метрики, которая определяет количество устройств, в которых применено содержимое развертывания к двойникам модулей в Центре Интернета вещей.
* **reportedSuccessfulCount** - A device metric that specifies the number of IoT Edge devices in the deployment reporting success from the IoT Edge client runtime.
* **reportedFailedCount** - A device metric that specifies the number of IoT Edge devices in the deployment reporting failure from the IoT Edge client runtime.

You can show a list of device IDs or objects for each of the metrics by using the [az iot edge deployment show-metric](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-show-metric) command:

```cli
az iot edge deployment show-metric --deployment-id [deployment id] --metric-id [metric id] --hub-name [hub name] 
```

The deployment show-metric command takes the following parameters: 
* **--deployment-id** — имя существующего развертывания в центре IoT.
* **--metric-id** — имя метрики, для которой нужно отобразить список идентификаторов устройств, например `reportedFailedCount`
* **--hub-name** — имя концентратора IoT, в котором существует развертывание. Центр должен быть в текущей подписке. Переключитесь на нужную подписку с помощью команды `az account set -s [subscription name]`

## <a name="modify-a-deployment"></a>Изменение развертывания

При изменении развертывания изменения немедленно реплицируются во все целевые устройства. 

Если обновить целевое условие, произойдут следующие обновления:

* Если устройство не соответствует предыдущему условию назначения, однако соответствует новому и это развертывание имеет самый высокий приоритет для устройства, то оно будет применено к устройству. 
* Если устройство, на котором сейчас выполняется развертывание, больше не соответствует условию назначения, оно удаляет это развертывание и выполняет следующее развертывание с самым высоким приоритетом. 
* Если устройство, на котором сейчас выполняется развертывание, больше не соответствует условию назначения, а также условию назначения любого другого развертывания, в устройстве не происходит никаких изменений. Устройство продолжает выполнять текущие модули в их актуальном состоянии, однако больше не управляется как часть этого развертывания. Если устройство соответствует условию назначения любого другого развертывания, оно удаляет это развертывание и выполняет новое. 

Use the [az iot edge deployment update](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-update) command to update a deployment:

```cli
az iot edge deployment update --deployment-id [deployment id] --hub-name [hub name] --set [property1.property2='value']
```

The deployment update command takes the following parameters:
* **--deployment-id** — имя существующего развертывания в центре IoT.
* **--hub-name** — имя концентратора IoT, в котором существует развертывание. Центр должен быть в текущей подписке. Переключитесь на нужную подписку с помощью команды `az account set -s [subscription name]`
* **--set** — обновление свойства в развертывании. Можно обновлять следующие свойства.
  * targetCondition — например `targetCondition=tags.location.state='Oregon'`
  * метки; 
  * priority


## <a name="delete-a-deployment"></a>Удаление развертывания

При удалении развертывания все устройства выполняют следующее развертывание, которое имеет наивысший приоритет. Если устройства не соответствуют условиям назначения любого другого развертывания, то модули не будут удалены при удалении развертывания. 

Use the [az iot edge deployment delete](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-delete) command to delete a deployment:

```cli
az iot edge deployment delete --deployment-id [deployment id] --hub-name [hub name] 
```

The deployment delete command takes the following parameters: 
* **--deployment-id** — имя существующего развертывания в центре IoT.
* **--hub-name** — имя концентратора IoT, в котором существует развертывание. Центр должен быть в текущей подписке. Переключитесь на нужную подписку с помощью команды `az account set -s [subscription name]`

## <a name="next-steps"></a>Дальнейшие действия

Learn more about [Deploying modules to IoT Edge devices](module-deployment-monitoring.md).
