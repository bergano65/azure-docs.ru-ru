---
title: Развертывание модулей из Azure CLI командной строки Azure IoT Edge
description: Используйте Azure CLI с расширением Azure IoT, чтобы отправить модуль IoT Edge из центра Интернета вещей на устройство IoT Edge, настроенное манифестом развертывания.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/16/2019
ms.topic: conceptual
ms.reviewer: menchi
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: fbd0d65624852737c424128e9125b8370b870d4d
ms.sourcegitcommit: edccc241bc40b8b08f009baf29a5580bf53e220c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/24/2020
ms.locfileid: "82133944"
---
# <a name="deploy-azure-iot-edge-modules-with-azure-cli"></a>Развертывание модулей IoT Edge Azure с помощью интерфейса командной строки Azure

Создав модули IoT Edge с в соответствии с определенной бизнес-логикой, вы наверняка захотите развернуть их на устройствах, которые будут работать в граничной среде. Если у вас есть несколько модулей, которые работают вместе для сбора и обработки данных, вы можете развернуть их одновременно и объявить правила маршрутизации, которые их соединяют.

[Azure CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) — это кроссплатформенная программа командной строки с открытым кодом для управления ресурсами Azure (например, IoT Edge). Она позволяет управлять ресурсами Центра Интернета вещей Azure, экземплярами службы подготовки устройств и связанными концентраторами без дополнительной настройки. Новое расширение Интернета вещей расширяет функции интерфейса командной строки Azure (например, функция управления устройствами) и добавляет возможности IoT Edge.

В этой статье показано, как создать манифест развертывания JSON и применить этот файл для отправки этого развертывания на устройство IoT Edge. Информацию о создании развертываний, предназначенных для нескольких устройств с определенными значениями тегов, см. в разделе [Развертывание и мониторинг модулей IoT Edge в нужном масштабе (предварительная версия)](how-to-deploy-cli-at-scale.md)

## <a name="prerequisites"></a>Предварительные условия

* [Центр Интернета вещей](../iot-hub/iot-hub-create-using-cli.md) в подписке Azure.
* [Устройство IoT Edge](how-to-register-device.md#register-with-the-azure-cli) с установленной средой выполнения IoT Edge.
* [Интерфейс командной строки Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) в вашей среде. По крайней мере, Azure CLI версия должна быть 2.0.70 или выше. Для проверки используйте `az --version`. Эта версия поддерживает команды расширения az и представляет собой платформу команд Knack.
* [Расширение IOT для Azure CLI](https://github.com/Azure/azure-iot-cli-extension).

## <a name="configure-a-deployment-manifest"></a>Настройка манифеста развертывания

Манифест развертывания — это документ JSON, в котором определены развертываемые модули, способ передачи данных между этими модулями и требуемые свойства для двойников модулей. Дополнительные сведения о работе манифестов развертывания и об их создании см. в руководстве по [использованию, настройке и повторном использовании модулей Azure IoT Edge](module-composition.md).

Чтобы развернуть модули с помощью интерфейса командной строки Azure, сохраните манифест развертывания на локальном компьютере в формате JSON. В следующем разделе вам потребуется путь к файлу для запуска команды, применяющей конкретную конфигурацию к устройству.

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
              "registryCredentials": {}
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
                "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"5671/tcp\":[{\"HostPort\":\"5671\"}],\"8883/tcp\":[{\"HostPort\":\"8883\"}],\"443/tcp\":[{\"HostPort\":\"443\"}]}}}"
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
            "upstream": "FROM /messages/* INTO $upstream"
          },
          "storeAndForwardConfiguration": {
            "timeToLiveSecs": 7200
          }
        }
      },
      "SimulatedTemperatureSensor": {
        "properties.desired": {
          "SendData": true,
          "SendInterval": 5
        }
      }
    }
  }
}
```

## <a name="deploy-to-your-device"></a>Развертывание на устройстве

Для развертывания модулей на устройстве следует применить манифест развертывания, в который были заранее внесены сведения о модулях.

Откройте папку, в которой сохранен манифест развертывания. Если вы применили один из шаблонов IoT Edge в VS Code, используйте файл `deployment.json` в папке **config** каталога решения, а не файл `deployment.template.json`.

Следующая команда применяет конфигурацию к устройству IoT Edge:

   ```azurecli
   az iot edge set-modules --device-id [device id] --hub-name [hub name] --content [file path]
   ```

В параметре идентификатора устройства учитывается регистр. Параметр content указывает на сохраненный ранее файл манифеста развертывания.

   ![Выходные данные команды az iot edge set-modules](./media/how-to-deploy-cli/set-modules.png)

## <a name="view-modules-on-your-device"></a>Просмотр модулей, установленных на устройстве

Завершив развертывание модулей на устройстве, вы можете просмотреть их список с помощью следующей команды:

Просмотрите модули на устройстве IoT Edge:

   ```azurecli
   az iot hub module-identity list --device-id [device id] --hub-name [hub name]
   ```

В параметре идентификатора устройства учитывается регистр.

   ![Выходные данные команды az iot hub module-identity list](./media/how-to-deploy-cli/list-modules.png)

## <a name="next-steps"></a>Следующие шаги

Изучите раздел [Развертывание и мониторинг модулей IoT Edge в нужном масштабе (предварительная версия)](how-to-deploy-at-scale.md)
