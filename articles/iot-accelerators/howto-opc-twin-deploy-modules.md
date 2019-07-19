---
title: Как развернуть модуль двойника OPC для Azure с нуля | Документация Майкрософт
description: Как развернуть OPC двойника с нуля.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: df1dd45d58baf82710b5e362afaf055aad140b98
ms.sourcegitcommit: af58483a9c574a10edc546f2737939a93af87b73
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/17/2019
ms.locfileid: "68302642"
---
# <a name="deploy-opc-twin-module-and-dependencies-from-scratch"></a>Развертывание модуля и зависимостей OPC двойника с нуля

Модуль OPC двойника выполняется на IoT Edge и предоставляет несколько служб пограничных услуг для двойникаов устройств OPC и служб реестра. 

Существует несколько вариантов развертывания модулей в шлюзе [Azure IOT Edge](https://azure.microsoft.com/services/iot-edge/) .

- [Развертывание из колонки IoT Edge портал Azure](https://docs.microsoft.com/azure/iot-edge/how-to-deploy-modules-portal)
- [Развертывание с помощью AZ CLI](https://docs.microsoft.com/azure/iot-edge/how-to-deploy-monitor-cli)

> [!NOTE]
> Дополнительные сведения о развертывании и инструкциях см. в [репозитории](https://github.com/Azure/azure-iiot-components)GitHub.

## <a name="deployment-manifest"></a>Манифест развертывания

Все модули развертываются с помощью манифеста развертывания.  Ниже приведен пример манифеста для развертывания как [издателя OPC](https://github.com/Azure/iot-edge-opc-publisher) , так и [OPC двойника](https://github.com/Azure/azure-iiot-opc-twin-module) .

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

## <a name="deploying-from-azure-portal"></a>Развертывание из портал Azure

Самый простой способ развертывания модулей на Azure IoT Edge устройстве шлюза — это портал Azure.  

### <a name="prerequisites"></a>Предварительные требования

1. Развернуть [зависимости](howto-opc-twin-deploy-dependencies.md) OPC двойника и получить получившийся `.env` файл. Обратите внимание `hub name` на развернутую `PCS_IOTHUBREACT_HUB_NAME` переменную `.env` в результирующем файле.

2. Зарегистрируйте и запустите шлюз [Linux](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-linux) или [Windows](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-windows) IOT EDGE и запишите `device id`его.

### <a name="deploy-to-an-edge-device"></a>Развертывание на пограничном устройстве

1. Войдите на [портал Azure](https://portal.azure.com/) и перейдите к своему Центру Интернета вещей.

2. Выберите **IOT Edge** в меню слева.

3. Щелкните идентификатор целевого устройства в списке устройств.

4. Щелкните **Set Modules** (Настроить модули).

5. В разделе **модули развертывания** страницы выберите **Добавить** и **IOT Edge модуль.**

6. В диалоговом окне **IOT Edge пользовательский модуль** используйте `opctwin` в качестве имени для модуля, а затем укажите *универсальный код ресурса (URI) образа* контейнера.

   ```bash
   mcr.microsoft.com/iotedge/opc-twin:latest
   ```

   В качестве *параметров создания контейнера*используйте следующий код JSON:

   ```json
   {"NetworkingConfig": {"EndpointsConfig": {"host": {}}}, "HostConfig": {"NetworkMode": "host" }}
   ```

   При желании заполните необязательные поля. Дополнительные сведения о возможностях при создании контейнера, политике перезапуска и требуемом состоянии см. в разделе [Требуемые свойства EdgeAgent](https://docs.microsoft.com/azure/iot-edge/module-edgeagent-edgehub#edgeagent-desired-properties). Подробные сведения о двойниках модулей см. в разделе [Определение или обновление требуемых свойств](https://docs.microsoft.com/azure/iot-edge/module-composition#define-or-update-desired-properties).

7. Выберите **сохранить** и повторите шаг **5**.  

8. В диалоговом окне IOT Edge настраиваемый модуль `opcpublisher` используйте в качестве имени модуля, а *URI образа* контейнера — как 

   ```bash
   mcr.microsoft.com/iotedge/opc-publisher:latest
   ```

   В качестве *параметров создания контейнера*используйте следующий код JSON:

   ```json
   {"Hostname":"publisher","Cmd":["publisher","--pf=./pn.json","--di=60","--to","--aa","--si=0","--ms=0"],"ExposedPorts":{"62222/tcp":{}},"HostConfig":{"PortBindings":{"62222/tcp":[{"HostPort":"62222"}] }}}
   ```

9. Щелкните **сохранить** , а затем **Далее** , чтобы перейти к разделу маршруты.

10. На вкладке Routes (маршруты) Вставьте следующий текст 

    ```json
    {
      "routes": {
        "opctwinToIoTHub": "FROM /messages/modules/opctwin/* INTO $upstream",
        "opcpublisherToIoTHub": "FROM /messages/modules/opcpublisher/* INTO $upstream"
      }
    }
    ```

    и нажмите кнопку **Далее** .

11. Проверьте сведения о развертывании и манифест.  Он должен выглядеть, как в приведенном выше манифесте развертывания.  Нажмите кнопку **Submit** (Отправить).

12. Завершив развертывание модулей на устройстве, вы можете просмотреть их на странице портала **Сведения об устройствах**. Эта страница отображает имя каждого развернутого модуля и полезную информацию о нем, включая состояние развертывания и код завершения.

## <a name="deploying-using-azure-cli"></a>Развертывание с помощью Azure CLI

### <a name="prerequisites"></a>предварительные требования

1. Установите последнюю версию [интерфейса командной строки Azure (AZ)](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) [отсюда](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

### <a name="quickstart"></a>Краткое руководство

1. Сохраните приведенный выше манифест развертывания в `deployment.json` файл.  

2. Следующая команда применяет конфигурацию к устройству IoT Edge:

   ```bash
   az iot edge set-modules --device-id [device id] --hub-name [hub name] --content ./deployment.json
   ```

   `device id` Параметр учитывает регистр. Параметр content указывает на сохраненный ранее файл манифеста развертывания. 
    ![AZ IoT Edge Set-modules Output](https://docs.microsoft.com/azure/iot-edge/media/how-to-deploy-cli/set-modules.png)

3. Завершив развертывание модулей на устройстве, вы можете просмотреть их список с помощью следующей команды:

   ```bash
   az iot hub module-identity list --device-id [device id] --hub-name [hub name]
   ```

   В параметре идентификатора устройства учитывается регистр. ![Выходные данные команды az iot hub module-identity list](https://docs.microsoft.com/azure/iot-edge/media/how-to-deploy-cli/list-modules.png)

## <a name="next-steps"></a>Следующие шаги

Теперь, когда вы узнали, как развернуть OPC двойника с нуля, предлагаем следующий шаг:

> [!div class="nextstepaction"]
> [Развертывание OPC двойника в существующем проекте](howto-opc-twin-deploy-existing.md)
