---
title: Как развернуть модуль OPC Twin для Azure с нуля Документы Майкрософт
description: В этой статье описывается, как развертывать OPC Twin с нуля с помощью лезвия IoT Edge портала Azure, а также с помощью A'CLI.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 6c8ceeaf49d8ebfa15a83118e8b518190f6ff85e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80241056"
---
# <a name="deploy-opc-twin-module-and-dependencies-from-scratch"></a>Развертывание модуля OPC Twin и зависимостей с нуля

Модуль OPC Twin работает на IoT Edge и предоставляет несколько услуг edge для двух и регистрационных служб устройства OPC. 

Существует несколько вариантов развертывания модулей для вашего [шлюза Azure IoT Edge,](https://azure.microsoft.com/services/iot-edge/) среди которых

- [Развертывание с лезвия IoT Edge портала Azure](https://docs.microsoft.com/azure/iot-edge/how-to-deploy-modules-portal)
- [Развертывание с использованием Аз-CLI](https://docs.microsoft.com/azure/iot-edge/how-to-deploy-monitor-cli)

> [!NOTE]
> Для получения дополнительной [информации](https://github.com/Azure/azure-iiot-components)о деталях развертывания и инструкциях см.

## <a name="deployment-manifest"></a>Манифест развертывания

Все модули развертываются с помощью манифеста развертывания.  Приведен пример манифеста для развертывания как [OPC Publisher, так](https://github.com/Azure/iot-edge-opc-publisher) и [OPC Twin](https://github.com/Azure/azure-iiot-opc-twin-module) ниже.

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
                "createOptions": ""
              }
            },
            "edgeHub": {
              "type": "docker",
              "status": "running",
              "restartPolicy": "always",
              "settings": {
                "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
                "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"5671/tcp\":[{\"HostPort\":\"5671\"}], \"8883/tcp\":[{\"HostPort\":\"8883\"}],\"443/tcp\":[{\"HostPort\":\"443\"}]}}}"
              }
            }
          },
          "modules": {
            "opctwin": {
              "version": "1.0",
              "type": "docker",
              "status": "running",
              "restartPolicy": "always",
              "settings": {
                "image": "mcr.microsoft.com/iotedge/opc-twin:latest",
                "createOptions": "{\"NetworkingConfig\": {\"EndpointsConfig\": {\"host\": {}}}, \"HostConfig\": {\"NetworkMode\": \"host\" }}"
              }
            },
            "opcpublisher": {
              "version": "2.0",
              "type": "docker",
              "status": "running",
              "restartPolicy": "always",
              "settings": {
                "image": "mcr.microsoft.com/iotedge/opc-publisher:latest",
                "createOptions": "{\"Hostname\":\"publisher\",\"Cmd\":[\"publisher\",\"--pf=./pn.json\",\"--di=60\",\"--to\",\"--aa\",\"--si=0\",\"--ms=0\"],\"ExposedPorts\":{\"62222/tcp\":{}},\"NetworkingConfig\":{\"EndpointsConfig\":{\"host\":{}}},\"HostConfig\":{\"NetworkMode\":\"host\",\"PortBindings\":{\"62222/tcp\":[{\"HostPort\":\"62222\"}]}}}"
              }
            }
          }
        }
      },
      "$edgeHub": {
        "properties.desired": {
          "schemaVersion": "1.0",
          "routes": {
            "opctwinToIoTHub": "FROM /messages/modules/opctwin/* INTO $upstream",
            "opcpublisherToIoTHub": "FROM /messages/modules/opcpublisher/* INTO $upstream"
          },
          "storeAndForwardConfiguration": {
            "timeToLiveSecs": 7200
          }
        }
      }
    }
  }
}
```

## <a name="deploying-from-azure-portal"></a>Развертывание с портала Azure

Самый простой способ развертывания модулей на устройстве шлюза Azure IoT Edge — это через портал Azure.  

### <a name="prerequisites"></a>Предварительные требования

1. Развернуть [зависимые OPC](howto-opc-twin-deploy-dependencies.md) Twin `.env` и получить полученный файл. Обратите внимание `hub name` на `PCS_IOTHUBREACT_HUB_NAME` развернутую переменную в полученном `.env` файле.

2. Зарегистрируйтесь и запустите шлюз `device id` [Linux](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-linux) или [Windows](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-windows) IoT Edge и обратите внимание на его.

### <a name="deploy-to-an-edge-device"></a>Развертывание на устройстве края

1. Войдите на [портал Azure](https://portal.azure.com/) и перейдите к концентратору IoT.

2. Выберите **IoT Edge** из меню левой руки.

3. Щелкните идентификатор целевого устройства в списке устройств.

4. Выберите **набор модулей.**

5. В разделе **модулей развертывания страницы** выберите **Модуль добавить** и **IoT Edge.**

6. В диалоге **пользовательского модуля IoT Edge** в `opctwin` качестве имени для модуля укажите *изображение* uri контейнера как

   ```bash
   mcr.microsoft.com/iotedge/opc-twin:latest
   ```

   В качестве *вариантов создания контейнеров*используйте следующие JSON:

   ```json
   {"NetworkingConfig": {"EndpointsConfig": {"host": {}}}, "HostConfig": {"NetworkMode": "host" }}
   ```

   При желании заполните необязательные поля. Дополнительные сведения о возможностях при создании контейнера, политике перезапуска и требуемом состоянии см. в разделе [Требуемые свойства EdgeAgent](https://docs.microsoft.com/azure/iot-edge/module-edgeagent-edgehub#edgeagent-desired-properties). Подробные сведения о двойниках модулей см. в разделе [Определение или обновление требуемых свойств](https://docs.microsoft.com/azure/iot-edge/module-composition#define-or-update-desired-properties).

7. Выберите **Сохранить** и повторить шаг **5**.  

8. В диалоге ioT Edge Custom `opcpublisher` Module используйте в качестве имени для модуля и *изображения контейнера URI* как 

   ```bash
   mcr.microsoft.com/iotedge/opc-publisher:latest
   ```

   В качестве *вариантов создания контейнеров*используйте следующие JSON:

   ```json
   {"Hostname":"publisher","Cmd":["publisher","--pf=./pn.json","--di=60","--to","--aa","--si=0","--ms=0"],"ExposedPorts":{"62222/tcp":{}},"HostConfig":{"PortBindings":{"62222/tcp":[{"HostPort":"62222"}] }}}
   ```

9. Выберите **Сохранить,** а затем **далее** продолжить в разделе маршрутов.

10. В вкладке маршрутов вставьте следующие 

    ```json
    {
      "routes": {
        "opctwinToIoTHub": "FROM /messages/modules/opctwin/* INTO $upstream",
        "opcpublisherToIoTHub": "FROM /messages/modules/opcpublisher/* INTO $upstream"
      }
    }
    ```

    и выбрать **Следующий**

11. Просмотрите информацию о развертывании и манифест.  Он должен выглядеть как выше развертывание манифест.  Нажмите кнопку **Отправить**.

12. Завершив развертывание модулей на устройстве, вы можете просмотреть их на странице портала **Сведения об устройствах**. Эта страница отображает имя каждого развернутого модуля и полезную информацию о нем, включая состояние развертывания и код завершения.

## <a name="deploying-using-azure-cli"></a>Развертывание с помощью Azure CLI

### <a name="prerequisites"></a>Предварительные требования

1. Установите последнюю версию [интерфейса командной строки Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) [отсюда.](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)

### <a name="quickstart"></a>Краткое руководство

1. Сохранить вышеуказанный `deployment.json` манифест развертывания в файле.  

2. Следующая команда применяет конфигурацию к устройству IoT Edge:

   ```azurecli
   az iot edge set-modules --device-id [device id] --hub-name [hub name] --content ./deployment.json
   ```

   Параметр `device id` чувствителен к делу. Параметр content указывает на сохраненный ранее файл манифеста развертывания. 
    ![выход наборных модулей az IoT Edge](https://docs.microsoft.com/azure/iot-edge/media/how-to-deploy-cli/set-modules.png)

3. Завершив развертывание модулей на устройстве, вы можете просмотреть их список с помощью следующей команды:

   ```azurecli
   az iot hub module-identity list --device-id [device id] --hub-name [hub name]
   ```

   Параметр идентификатора устройства чувствителен к случаям. ![Выходные данные команды az iot hub module-identity list](https://docs.microsoft.com/azure/iot-edge/media/how-to-deploy-cli/list-modules.png)

## <a name="next-steps"></a>Дальнейшие действия

Теперь, когда вы узнали, как развернуть OPC Twin с нуля, вот предлагаемый следующий шаг:

> [!div class="nextstepaction"]
> [Развертывание OPC Twin в существующий проект](howto-opc-twin-deploy-existing.md)
