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
ms.openlocfilehash: f470beb79e69b5a4a3febeb6a433c48490b96cf7
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59491362"
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
            "restartPolicy": "never",
            "settings": {
              "image": "mcr.microsoft.com/iotedge/opc-twin:latest",
              "createOptions": "{\"HostConfig\": { \"NetworkMode\": \"host\", \"CapAdd\": [\"NET_ADMIN\"] } }"
            }
          },
          "opcpublisher": {
            "version": "2.0",
            "type": "docker",
            "status": "running",
            "restartPolicy": "never",
            "settings": {
              "image": "mcr.microsoft.com/iotedge/opc-publisher:latest",
              "createOptions": "{\"Hostname\": \"publisher\", \"Cmd\": [ \"publisher\", \"--pf=./pn.json\", \"--di=60\", \"--to\", \"--aa\", \"--si=0\", \"--ms=0\" ], \"ExposedPorts\": { \"62222/tcp\": {} }, \"HostConfig\": { \"PortBindings\": { \"62222/tcp\": [{ \"HostPort\": \"62222\" }] } } }"
            }
          }
        }
      }
    },
    "$edgeHub": {
      "properties.desired": {
        "schemaVersion": "1.0",
        "routes": {
          "opctwinToIoTHub": "FROM /messages/modules/opctwin/outputs/* INTO $upstream",
          "opcpublisherToIoTHub": "FROM /messages/modules/opcpublisher/outputs/* INTO $upstream"
        },
        "storeAndForwardConfiguration": {
          "timeToLiveSecs": 7200
        }
      }
    }
  }
}
```

## <a name="deploying-from-azure-portal"></a>Развертывание на портале Azure

Самый простой способ развернуть модули на устройстве шлюза Edge Интернета вещей Azure — через портал Azure.  

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

### <a name="quickstart"></a>Быстрый запуск

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

## <a name="run-and-debug-locally"></a>Выполнять и отлаживать локально

Устранении неполадок и отладка полезен для запуска локально с помощью модулей Edge [симулятор разработки Edge Интернета вещей](https://github.com/Azure/iotedgehubdev).  Она предоставляет локальную разработку симулятор для создания, разработки, тестирования, запуска и отладки модулей Edge Интернета вещей Azure и решений с помощью того же бит/кода, которые используются в рабочей среде.

### <a name="prerequisites"></a>Технические условия

1. Развертывание на "Двойнике" OPC [зависимости](howto-opc-twin-deploy-dependencies.md).

2. Установка [Docker CE (18.02.0+)](https://www.docker.com/community-edition) на [Windows](https://docs.docker.com/docker-for-windows/install/), [macOS](https://docs.docker.com/docker-for-mac/install/) или [Linux](https://docs.docker.com/install/linux/docker-ce/ubuntu/#install-docker-ce).

3. Установка [Docker Compose (1.20.0+)](https://docs.docker.com/compose/install/#install-compose) (требуется только для **Linux**. Compose уже был включен в установку Docker CE для Windows, Mac OS)

4. Установка [Python (2.7 / 3.5+) и Pip](https://www.python.org/)

5. Установить iotedgehubdev, выполнив следующую команду в окне терминала

   ```bash
   pip install --upgrade iotedgehubdev
   ```

> [!NOTE]
> Установка `iotedgehubdev` для **корневой** в Linux и Mac OS (*не использовать "--пользователя" в этой команде «pip install»*).
> Убедитесь, что нет среды выполнения Azure IoT Edge, запускать на одном компьютере с iotedgehubdev, так как они требуют одинаковые порты.

### <a name="quickstart"></a>Быстрый запуск

1. Следуйте инструкциям, чтобы [создайте устройство на портале Azure](https://docs.microsoft.com/azure/iot-edge/how-to-register-device-portal).  Скопируйте строку подключения для устройства edge.

2. Настройка симулятора, с помощью строки подключения edge.

    ```bash
    iotedgehubdev setup -c <edge-device-connection-string>
    ```

3. Копирования выше манифест в `deployment.json` файл в той же папке.  Запустить развертывание в симуляторе с помощью

    ```bash
    iotedgehubdev start -d deployment.json
    ```

4. Остановите симулятора с помощью

   ```bash
   iotedgehubdev stop
   ```

## <a name="next-steps"></a>Дальнейшие действия

Теперь, когда вы узнали, как развернуть Двойника OPC с нуля, вот мы предлагаем:

> [!div class="nextstepaction"]
> [Развертывание OPC Двойника в существующий проект](howto-opc-twin-deploy-existing.md)