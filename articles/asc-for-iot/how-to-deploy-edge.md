---
title: Развертывание Центра безопасности Azure для модуля IoT Edge Документы Майкрософт
description: Узнайте о том, как развернуть Центр безопасности Azure для агента безопасности IoT на IoT Edge.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 32a9564d-16fd-4b0d-9618-7d78d614ce76
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/30/2020
ms.author: mlottner
ms.openlocfilehash: b2af392dc4dc848a099b8297bb58e7d4a7104fa6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76964045"
---
# <a name="deploy-a-security-module-on-your-iot-edge-device"></a>Развертывание модуля безопасности на устройстве IoT Edge


**Центр безопасности Azure для** IoT-модуля предоставляет комплексное решение для безопасности устройств IoT Edge.
Модуль безопасности собирает, агрегирует и анализирует необработанные данные безопасности из операционной системы и системы контейнеров в действенные рекомендации и оповещения.
Чтобы узнать больше, смотрите [модуль безопасности для IoT Edge](security-edge-architecture.md).

В этой статье вы узнаете, как развернуть модуль безопасности на устройстве IoT Edge.

## <a name="deploy-security-module"></a>Развертывание модуля безопасности

Используйте следующие шаги для развертывания Центра безопасности Azure для модуля безопасности IoT для IoT Edge.

### <a name="prerequisites"></a>Предварительные требования

1. В концентраторе IoT убедитесь, что устройство [зарегистрировано как устройство IoT Edge.](https://docs.microsoft.com/azure/iot-edge/how-to-register-device-portal)

1. Центр безопасности Azure для модуля IoT Edge требует установки [платформы AuditD](https://linux.die.net/man/8/auditd) на устройстве IoT Edge.

    - Установите фреймворк, запустив следующую команду на устройстве IoT Edge:
   
    `sudo apt-get install auditd audispd-plugins`

    - Verify AuditD активен при запуске следующей команды: 
   
    `sudo systemctl status auditd`<br>
    - Ожидаемый ответ:`active (running)` 
        

### <a name="deployment-using-azure-portal"></a>Развертывание с помощью портала Azure

1. С портала Azure, откройте **Marketplace**.

1. Выберите **Internet of Things,** затем ищите **Центр безопасности Azure для IoT** и выберите его.

   ![Выберите Центр безопасности Azure для IoT](media/howto/edge-onboarding-8.png)

1. Нажмите **Создать** для настройки развертывания. 

1. Выберите **подписку** Azure на концентратор IoT, а затем выберите **концентратор IoT.**<br>Выберите Развертывание на устройстве для **таргетинга** на одно устройство или выберите **Развертывание в масштабе,** чтобы настроить таргетинг на несколько устройств, и нажмите **«Создать».** Для получения дополнительной информации о развертывании в масштабе [см.](https://docs.microsoft.com/azure/iot-edge/how-to-deploy-monitor) 

    >[!Note] 
    >Если вы выбрали **Развертывание в масштабе,** добавьте имя устройства и детали, прежде чем продолжить вкладку **«Добавленные модули»** в следующих инструкциях.     

Заполните каждый шаг для завершения развертывания IoT Edge для Центра безопасности Azure для IoT. 

#### <a name="step-1-modules"></a>Шаг 1: Модули

1. Выберите модуль **AzureSecurityCenterforIoT.**
1. На вкладке **Настройки модуля** измените **название** на **azureiotsecurity.**
1. На вкладке **Переменные Enviroment,** добавить переменную, если это необходимо (например, уровень отладки).
1. На вкладке **Параметры создания контейнера** добавьте следующую конфигурацию:

    ``` json
    {
        "NetworkingConfig": {
            "EndpointsConfig": {
                "host": {}
            }
        },
        "HostConfig": {
            "Privileged": true,
            "NetworkMode": "host",
            "PidMode": "host",
            "Binds": [
                "/:/host"
            ]
        }
    }    
    ```
    
1. На вкладке **Параметры Модуля-Близнецы** добавьте следующую конфигурацию:
      
    ``` json
      "ms_iotn:urn_azureiot_Security_SecurityAgentConfiguration":{}
    ```

1. Выберите **обновление**.

#### <a name="step-2-runtime-settings"></a>Шаг 2: Настройки времени выполнения

1. Выберите **параметры выполнения.**
1. Под **Edge Hub**измените **изображение** на **mcr.microsoft.com/azureiotedge-hub:1.0.8.3.**
1. Параметры проверки **создания** настроены на следующую конфигурацию: 
         
    ``` json
    { 
       "HostConfig":{ 
          "PortBindings":{ 
             "8883/tcp":[ 
                { 
                   "HostPort":"8883"
                }
             ],
             "443/tcp":[ 
                { 
                   "HostPort":"443"
                }
             ],
             "5671/tcp":[ 
                { 
                   "HostPort":"5671"
                }
             ]
          }
       }
    }
    ```
    
1. Нажмите кнопку **Сохранить**.
   
1. Нажмите кнопку **Далее**.

#### <a name="step-3-specify-routes"></a>Шаг 3: Определить маршруты 

1. На вкладке **«Указанные маршруты»** убедитесь, что у вас есть маршрут (явный или неявный), который будет перенаправлять сообщения из модуля **azureiotsecurity** **$upstream** в соответствии со следующими примерами. Только тогда, когда маршрут на месте, выберите **Следующий**.

   Пример маршрутов:

    ~~~Default implicit route
    "route": "FROM /messages/* INTO $upstream" 
    ~~~

    ~~~Explicit route
    "ASCForIoTRoute": "FROM /messages/modules/azureiotsecurity/* INTO $upstream"
    ~~~

1. Нажмите кнопку **Далее**.

#### <a name="step-4-review-deployment"></a>Шаг 4: Обзор развертывания

- На вкладке **«Развертывание обзора»** просмотрите информацию о развертывании, а затем выберите **«Создать»** для завершения развертывания.

## <a name="diagnostic-steps"></a>Диагностические шаги

Если вы столкнулись с проблемой, журналы контейнеров — это лучший способ узнать о состоянии устройства модуля модуля безопасности IoT Edge. Для сбора сведений используйте команды и инструменты, приведенные в этом разделе.

### <a name="verify-the-required-containers-are-installed-and-functioning-as-expected"></a>Проверка необходимых контейнеров установлена и функционирует в соответствии с ожиданиями

1. Выполнить следующую команду на устройстве IoT Edge:
    
    `sudo docker ps`
   
1. Убедитесь, что следующие контейнеры работают:
   
   | name | IMAGE |
   | --- | --- |
   | лазуриотбезопасности | mcr.microsoft.com/ascforiot/azureiotsecurity:1.0.2 |
   | edgeHub | mcr.microsoft.com/azureiotedge-hub:1.0.8.3 |
   | edgeAgent | mcr.microsoft.com/azureiotedge-agent:1.0.1 |
   
   Если минимальные требуемые контейнеры отсутствуют, проверьте, совпадает ли манифест развертывания IoT Edge с рекомендуемыми настройками. Для получения дополнительной информации [см.](#deployment-using-azure-portal)

### <a name="inspect-the-module-logs-for-errors"></a>Проверка журналов модулей на наличие ошибок
   
1. Выполнить следующую команду на устройстве IoT Edge:

   `sudo docker logs azureiotsecurity`
   
1. Для получения более подробной информации о журналах добавьте следующую `logLevel=Debug`переменную среды в развертывание модуля **azureiotsecurity:** .

## <a name="next-steps"></a>Дальнейшие действия

Чтобы узнать больше о вариантах конфигурации, перейти к руководству по конфигурации модуля. 
> [!div class="nextstepaction"]
> [Конфигурация модуля как руководство](./how-to-agent-configuration.md)
