---
title: Развертывание модулей из Visual Studio Code - Azure IoT Edge
description: Используйте Visual Studio Code с инструментами Azure IoT, чтобы подтолкнуть модуль IoT Edge от концентратора IoT на устройство IoT Edge, настроенный манифестом развертывания.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/8/2019
ms.topic: conceptual
ms.reviewer: ''
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: e1b2e2a80670cf0409f8f8477563b9a209cc8706
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77209211"
---
# <a name="deploy-azure-iot-edge-modules-from-visual-studio-code"></a>Развертывание модулей Azure IoT Edge из Visual Studio Code

Создав модули IoT Edge с в соответствии с определенной бизнес-логикой, вы наверняка захотите развернуть их на устройствах, которые будут работать в граничной среде. Если у вас есть несколько модулей, которые работают вместе для сбора и обработки данных, вы можете развернуть их одновременно и объявить правила маршрутизации, которые их соединяют.

В этой статье показано, как создать манифест развертывания JSON и применить этот файл для отправки этого развертывания на устройство IoT Edge. Для получения информации о создании развертывания, ориентированного на несколько устройств на основе общих тегов, [см. Модули Deploy IoT Edge в масштабе с помощью Visual Studio Code.](how-to-deploy-monitor-vscode.md)

## <a name="prerequisites"></a>Предварительные требования

* [Концентратор IoT](../iot-hub/iot-hub-create-through-portal.md) в подписке Azure.
* [Устройство IoT Edge](how-to-register-device.md#register-with-visual-studio-code) с установленной средой выполнения IoT Edge.
* [Визуальный код студии](https://code.visualstudio.com/).
* [Инструменты Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools#overview) для визуального кода студии.

## <a name="configure-a-deployment-manifest"></a>Настройка манифеста развертывания

Манифест развертывания — это документ JSON, в котором определены развертываемые модули, способ передачи данных между этими модулями и требуемые свойства для двойников модулей. Дополнительные сведения о работе манифестов развертывания и об их создании см. в руководстве по [использованию, настройке и повторном использовании модулей Azure IoT Edge](module-composition.md).

Чтобы развернуть модули с помощью Visual Studio Code, сохраните манифест развертывания на локальный компьютер в формате JSON. В следующем разделе вам потребуется путь к файлу для запуска команды, применяющей конкретную конфигурацию к устройству.

Вот пример простого манифеста развертывания с одним модулем.

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
                 "createOptions": "{}"
               }
             },
             "edgeHub": {
               "type": "docker",
               "status": "running",
               "restartPolicy": "always",
               "settings": {
                 "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
                 "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"443/tcp\":[{\"HostPort\":\"443\"}],\"5671/tcp\":[{\"HostPort\":\"5671\"}],\"8883/tcp\":[{\"HostPort\":\"8883\"}]}}}"
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
               "route": "FROM /messages/* INTO $upstream"
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
   ```

## <a name="sign-in-to-access-your-iot-hub"></a>Вход в систему для получения доступа к Центру Интернета вещей

Для выполнения действий с Центром Интернета вещей можно использовать расширения Интернета вещей Azure для Visual Studio Code. Для этого войдите в учетную запись Azure и выберите Центр Интернета вещей, с которым вы хотите работать.

1. Откройте в Visual Studio Code представление **Explorer**.

1. В нижней части Explorer расширьте раздел **Azure IoT Hub.**

   ![Расширить раздел Azure IoT концентратор](./media/how-to-deploy-modules-vscode/azure-iot-hub-devices.png)

1. Нажмите на **раздел** **Azure IoT Hub.** Если значок многоточия не отображается, наведите курсор на заголовок.

1. Щелкните **Выбрать Центр Интернета вещей**.

1. Если вы еще не вошли в учетную запись Azure, сделайте это сейчас, следуя инструкциям.

1. Выберите подписку Azure.

1. Выберите нужный Центр Интернета вещей.

## <a name="deploy-to-your-device"></a>Развертывание на устройстве

Для развертывания модулей на устройстве следует применить манифест развертывания, в который были заранее внесены сведения о модулях.

1. В представлении Visual Studio Code explorer расширьте раздел **Azure IoT Hub,** а затем расширьте узел **устройств.**

1. Щелкните правой кнопкой мыши на устройстве IoT Edge, которое необходимо настроить с помощью манифеста развертывания.

    > [!TIP]
    > Чтобы подтвердить, что выбранное устройство является устройством IoT Edge, выберите его, после чего развернется список модулей, где можно проверить наличие **$edgeHub** и **$edgeAgent**. Каждое устройство IoT Edge содержит эти два модуля.

1. Выберите **Create Deployment for Single Device** (Создание развертывания для одного устройства).

1. Перейдите к файлу JSON манифеста развертывания, который необходимо использовать, и нажмите **Выберите манифест развертывания Edge**.

   ![Выбор манифеста развертывания Edge](./media/how-to-deploy-modules-vscode/select-deployment-manifest.png)

Результаты развертывания будут напечатаны в выходных данных VS Code. Если целевое устройство запущено и подключено к Интернету, успешные развертывания применяются в течение нескольких минут.

## <a name="view-modules-on-your-device"></a>Просмотр модулей, установленных на устройстве

После развертывания модулей на устройство можно просмотреть все их в разделе **Azure IoT Hub.** Выберите стрелку рядом с устройством IoT Edge, чтобы развернуть его. Отобразятся все модули, которые выполняются в настоящий момент.

Если вы недавно развернули новые модули на устройстве, наведите указатель мыши на заголовок раздела **Azure IoT Hub Devices** (Устройства Центра Интернета вещей Azure) и выберите значок обновления, чтобы обновить представление.

Щелкните правой кнопкой мыши на название модуля, чтобы его просмотреть и отредактировать.

## <a name="next-steps"></a>Дальнейшие действия

Узнайте, как [развертывать и контролировать модули IoT Edge в масштабе с помощью Visual Studio Code](how-to-deploy-monitor.md)
