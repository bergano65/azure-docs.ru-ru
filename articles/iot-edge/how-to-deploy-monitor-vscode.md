---
title: Развертывание модулей в масштабе с помощью Visual Studio Code - Azure IoT Edge
description: Используйте расширение IoT для Visual Studio Code для создания автоматических развертываний для групп устройств IoT Edge.
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 1/8/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 08299a589dc6e8f768cba7ef976e109ef1fb69d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75774137"
---
# <a name="deploy-iot-edge-modules-at-scale-using-visual-studio-code"></a>Развертывание модулей IoT Edge в масштабе с помощью Visual Studio Code

Можно создать **автоматическое развертывание IoT Edge** с помощью Visual Studio Code для управления текущими развертываниями для многих устройств одновременно. Автоматическое развертывание IoT Edge является частью функции [автоматического управления устройствами](/azure/iot-hub/iot-hub-automatic-device-management) IoT Hub. Развертывания представляют собой динамические процессы, которые позволяют развертывать несколько модулей на нескольких устройствах. Вы также можете отслеживать состояние и работоспособность модулей, а при необходимости вносить изменения.

Для получения дополнительной информации [см.](module-deployment-monitoring.md)

В этой статье вы настроили Visual Studio Code и расширение IoT. Затем вы узнаете, как развертывать модули на набор устройств IoT Edge.

## <a name="prerequisites"></a>Предварительные требования

* [Концентратор IoT](../iot-hub/iot-hub-create-through-portal.md) в подписке Azure.
* [Устройство IoT Edge](how-to-register-device.md#register-with-visual-studio-code) с установленной средой выполнения IoT Edge.
* [Визуальный код студии](https://code.visualstudio.com/).
* [Инструменты Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools#overview) для визуального кода студии.

## <a name="sign-in-to-access-your-iot-hub"></a>Вход в систему для получения доступа к Центру Интернета вещей

Расширения Azure IoT для Visual Studio Code можно использовать для работы с концентратором. Чтобы эти операции работали, необходимо войти в учетную запись Azure и выбрать концентратор IoT, над которым работаете.

1. Откройте в Visual Studio Code представление **Explorer**.

1. В нижней части Explorer расширьте раздел **Azure IoT Hub.**

1. Нажмите на **раздел** **Azure IoT Hub.** Если значок многоточия не отображается, наведите курсор на заголовок.

1. Щелкните **Выбрать Центр Интернета вещей**.

1. Если вы не вошли в свою учетную запись Azure, следуйте подсказкам.

1. Выберите подписку Azure.

1. Выберите нужный Центр Интернета вещей.

## <a name="configure-a-deployment-manifest"></a>Настройка манифеста развертывания

Манифест развертывания — это документ JSON, описывающий, какие модули развертывать. В нем также описывается, как данные перетекают между модулями, и желаемые свойства близнецов модуля. Для получения дополнительной информации [см.](module-composition.md)

Чтобы развернуть модули с помощью Visual Studio Code, сохраните манифест развертывания на локальный компьютер в формате JSON. При запуске команды необходимо увеять его местоположение, чтобы применить конфигурацию к устройству.

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

Если вам нужно определить, какие устройства IoT Edge вы в настоящее время можете настроить, запустите команду **IoT Edge: Get Device Info.**

## <a name="identify-devices-with-target-conditions"></a>Определение устройств с целевыми условиями

Чтобы определить устройства IoT Edge, которые должны получить развертывание, необходимо указать целевое условие. Целевое условие выполняется, когда указанные критерии сопоставляются с deviceId, значением тегов или заявленным значением свойства.

Вы настраиваете теги в близнеце устройства. Вот пример устройства близнец, который имеет теги:

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

Это устройство получит развертывание, если целевое условие для развертывания содержит выражение, которое соответствует `tag.location.building = '20'`значениям тега, например

Если вы хотите настроить таргетинг на определенное устройство, независимо `deviceId` от его тегов или других значений, просто укажите для целевого состояния.

Вот еще несколько примеров:

* deviceId ='linuxprod1'
* deviceId и 'linuxprod1' ИЛИ устройствоId ' 'linuxprod2' ИЛИ устройствоId и 'linuxprod3'
* tags.environment ='prod'
* tags.environment - 'prod' И tags.location
* tags.environment - 'prod' OR tags.location
* tags.operator - 'Джон' И tags.environment - 'prod' И НЕ deviceId ' 'linuxprod1'

См [целевое условие](module-deployment-monitoring.md#target-condition) для получения подробной информации. Дополнительные сведения о двойниках устройств и тегах см. в статье [Общие сведения о двойниках устройств и их использование в Центре Интернета вещей](../iot-hub/iot-hub-devguide-device-twins.md).

### <a name="edit-the-device-twin"></a>Оторите устройство близнеца

Вы можете отсвазать устройство близнеца в Visual Studio Code для настройки тегов. Из меню **View** выберите **палитру команд** и запустите команду **IoT Edge: Edit Device Twin.** Выберите устройство IoT Edge и появится близнец устройства.

В этом примере не определены теги. Замените текущий `"tags": {}` пустой раздел собственным определением тегов.

```json
{
    "deviceId": "myEdgeDevice",
    "etag": "AAAAAAAAAAE=",
    "deviceEtag": "NTgwMDg5MDAz",
    "status": "enabled",
    "statusUpdateTime": "0001-01-01T00:00:00Z",
    "connectionState": "Disconnected",
    "lastActivityTime": "0001-01-01T00:00:00Z",
    "cloudToDeviceMessageCount": 0,
    "authenticationType": "sas",
    "x509Thumbprint": {
        "primaryThumbprint": null,
        "secondaryThumbprint": null
    },
    "version": 2,
    "properties": {
        "desired": {
            "$metadata": {
                "$lastUpdated": "2019-12-29T00:58:49.9315265Z"
            },
            "$version": 1
        },
        "reported": {
            "$metadata": {
                "$lastUpdated": "2019-12-29T00:58:49.9315265Z"
            },
            "$version": 1
        }
    },
    "capabilities": {
        "iotEdge": true
    },
    "deviceScope": "ms-azure-iot-edge://myEdgeDevice-637131779299315265",
    "tags": {}
}
```

После сохранения локального файла запустите команду **IoT Edge: Update Device Twin.**

## <a name="create-deployment-at-scale"></a>Создание развертывания в масштабе

После настройки манифеста развертывания и настроенных тегов в близнеце устройства вы готовы к развертыванию.

1. Из меню **«Представление»** выберите **палитру команд** и — **Azure IoT Edge: Создайте развертывание в** команде масштаба.

1. Перейдите к файлу JSON манифеста развертывания, который необходимо использовать, и нажмите **Выберите манифест развертывания Edge**.

1. Укажите значения по мере запроса, начиная с **идентификатора развертывания.**

   ![Указать идентификатор развертывания](./media/how-to-deploy-monitor-vscode/create-deployment-at-scale.png)

   Указать значения для этих параметров:

  | Параметр | Описание |
  | --- | --- |
  | Идентификатор развертывания | Название развертывания, которое будет создано в концентраторе IoT. Присвойте своему развертыванию уникальное имя, содержащее до 128 букв в нижнем регистре. Не используйте пробелы и следующие недопустимые символы: `& ^ [ ] { } \ | " < > /`. |
  | Условие назначения | Введите целевое условие, чтобы определить, какие устройства будут нацелены на это развертывание.Условие основано на тегах двойника устройства или его сообщаемых свойствах и должно соответствовать формату выражения.Например, `tags.environment='test' and properties.reported.devicemodel='4000x'`. |
  | Приоритет |  Положительное целое число. Если два или более развертываний нацелены на одно и то же устройство, будет применяться развертывание с наивысшим числовым значением для Priority. |

  После указания приоритета терминал должен отображать выход, аналогичный следующему изображению:

   ```cmd
   [Edge] Start deployment with deployment id [{specified-value}] and target condition [{specified-value}]
   [Edge] Deployment with deployment id [{specified-value}] succeeded.
   ```

## <a name="monitoring-and-modifying-deployments"></a>Мониторинг и изменение развертывания

Используйте [azure CLI](how-to-deploy-monitor-cli.md#monitor-a-deployment) или [портал Azure](how-to-deploy-monitor.md#monitor-a-deployment) для мониторинга, изменения и удаления развертываний. Оба предоставляют метрики на развертывании.

## <a name="next-steps"></a>Дальнейшие действия

Подробнее о [развертывании модулей на устройстваio-прилежных.](module-deployment-monitoring.md)
