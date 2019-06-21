---
title: Развертывание модуля Двойника OPC для Azure с нуля | Документация Майкрософт
description: Как развернуть OPC Двойника с нуля.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: conceptual
ms.service: iot-industrialiot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 41d544fd23d258393cc83ea09371332655223581
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67203927"
---
# <a name="deploy-opc-twin-module-and-dependencies-from-scratch"></a>Развертывание OPC Двойника модуля и зависимости с нуля

OPC Двойника модуля выполняется в IoT Edge и предоставляет несколько пограничных служб в двойник устройства OPC и реестра службы. 

Существует несколько вариантов для развертывания модулей, чтобы ваши [Azure IoT Edge](https://azure.microsoft.com/services/iot-edge/) шлюза, между ними

- [Развертывание из колонки портала Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/how-to-deploy-modules-portal)
- [Развертывание с помощью AZ CLI](https://docs.microsoft.com/azure/iot-edge/how-to-deploy-monitor-cli)

> [!NOTE]
> Дополнительные сведения о подробных сведений о развертывании и инструкции см. в разделе со сведениями о GitHub [репозитория](https://github.com/Azure/azure-iiot-components).

## <a name="deployment-manifest"></a>Манифест развертывания

Все модули развертываются с помощью манифеста развертывания.  Пример манифеста для развертывания обеих [издатель OPC](https://github.com/Azure/iot-edge-opc-publisher) и [Двойника OPC](https://github.com/Azure/azure-iiot-opc-twin-module) показан ниже.

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
                "createOptions": "{\"NetworkingConfig\":{\"EndpointsConfig\":{\"host\":{}}},\"HostConfig\":{\"NetworkMode\":\"host\",\"CapAdd\":[\"NET_ADMIN\"]}}"
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

## <a name="deploying-from-azure-portal"></a>Развертывание на портале Azure

Самый простой способ развернуть модули на устройстве шлюза IoT Edge Azure — через портал Azure.  

### <a name="prerequisites"></a>Технические условия

1. Развертывание на "Двойнике" OPC [зависимости](howto-opc-twin-deploy-dependencies.md) и не получил результирующий `.env` файл. Обратите внимание, развернутому `hub name` из `PCS_IOTHUBREACT_HUB_NAME` переменных в итоговом `.env` файл.

2. Регистрация и запуск [Linux](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-linux) или [Windows](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-windows) шлюз Edge Интернета вещей и запишите его `device id`.

### <a name="deploy-to-an-edge-device"></a>Развертывание на пограничном устройстве

1. Войдите на [портал Azure](https://portal.azure.com/) и перейдите к своему Центру Интернета вещей.

2. Выберите **IoT Edge** в меню слева.

3. Щелкните идентификатор целевого устройства в списке устройств.

4. Щелкните **Set Modules** (Настроить модули).

5. В **развертывания модулей** раздел страницы, выберите **добавить** и **модуля IoT Edge.**

6. В **пользовательского модуля IoT Edge** используйте диалоговое окно `opctwin` как имя модуля, затем укажите контейнер *изображения URI* как

   ```bash
   mcr.microsoft.com/iotedge/opc-twin:latest
   ```

   Как *параметры создания* используйте приведенный ниже код JSON:

   ```json
   {"HostConfig":{"NetworkMode":"host","CapAdd":["NET_ADMIN"]}}
   ```

   При желании заполните необязательные поля. Дополнительные сведения о возможностях при создании контейнера, политике перезапуска и требуемом состоянии см. в разделе [Требуемые свойства EdgeAgent](https://docs.microsoft.com/azure/iot-edge/module-edgeagent-edgehub#edgeagent-desired-properties). Подробные сведения о двойниках модулей см. в разделе [Определение или обновление требуемых свойств](https://docs.microsoft.com/azure/iot-edge/module-composition#define-or-update-desired-properties).

7. Выберите **Сохранить** и повторите шаг **5**.  

8. В диалоговом окне пользовательского модуля IoT Edge используйте `opcpublisher` как имя модуля и контейнером *изображения URI* как 

   ```bash
   mcr.microsoft.com/iotedge/opc-publisher:latest
   ```

   Как *параметры создания* используйте приведенный ниже код JSON:

   ```json
   {"Hostname":"publisher","Cmd":["publisher","--pf=./pn.json","--di=60","--to","--aa","--si=0","--ms=0"],"ExposedPorts":{"62222/tcp":{}},"HostConfig":{"PortBindings":{"62222/tcp":[{"HostPort":"62222"}] }}}
   ```

9. Выберите **Сохранить** и затем **Далее** для перейдите к разделу маршруты.

10. На вкладке «маршруты» вставьте в него следующее 

    ```json
    {
      "routes": {
        "opctwinToIoTHub": "FROM /messages/modules/opctwin/outputs/* INTO $upstream",
        "opcpublisherToIoTHub": "FROM /messages/modules/opcpublisher/outputs/* INTO $upstream"
      }
    }
    ```

    и выберите **Далее**

11. Просмотрите сведения о развертывании и манифест.  Он должен выглядеть выше манифест развертывания.  Нажмите кнопку **Submit** (Отправить).

12. Завершив развертывание модулей на устройстве, вы можете просмотреть их на странице портала **Сведения об устройствах**. Эта страница отображает имя каждого развернутого модуля и полезную информацию о нем, включая состояние развертывания и код завершения.

## <a name="deploying-using-azure-cli"></a>Развертывание с помощью Azure CLI

### <a name="prerequisites"></a>Технические условия

1. Установите последнюю версию [интерфейс командной строки Azure (AZ)](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) из [здесь](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

### <a name="quickstart"></a>Краткое руководство

1. Сохраните манифест развертывания выше в `deployment.json` файл.  

2. Следующая команда применяет конфигурацию к устройству IoT Edge:

   ```bash
   az iot edge set-modules --device-id [device id] --hub-name [hub name] --content ./deployment.json
   ```

   `device id` Параметр чувствителен к регистру. Параметр content указывает на сохраненный ранее файл манифеста развертывания. 
    ![выходные данные набора модулей IoT Edge AZ](https://docs.microsoft.com/azure/iot-edge/media/how-to-deploy-cli/set-modules.png)

3. Завершив развертывание модулей на устройстве, вы можете просмотреть их список с помощью следующей команды:

   ```bash
   az iot hub module-identity list --device-id [device id] --hub-name [hub name]
   ```

   Параметр идентификатора устройства чувствителен к регистру. ![Выходные данные команды az iot hub module-identity list](https://docs.microsoft.com/azure/iot-edge/media/how-to-deploy-cli/list-modules.png)

## <a name="next-steps"></a>Дальнейшие действия

Теперь, когда вы узнали, как развернуть Двойника OPC с нуля, вот мы предлагаем:

> [!div class="nextstepaction"]
> [Развертывание OPC Двойника в существующий проект](howto-opc-twin-deploy-existing.md)