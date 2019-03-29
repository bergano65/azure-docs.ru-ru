---
title: Развертывание ASC для модуля IoT Edge | Документация Майкрософт
description: Дополнительные сведения о том, как развернуть ASC для агента безопасности Интернета вещей в IoT Edge.
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: 32a9564d-16fd-4b0d-9618-7d78d614ce76
ms.service: ascforiot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2019
ms.author: mlottner
ms.openlocfilehash: 87094b265a0c30c0349d64e4b956224525c04f74
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2019
ms.locfileid: "58580498"
---
# <a name="deploy-security-module-on-your-iot-edge-device"></a>Развертывание модуля безопасности на вашем устройстве IoT Edge

> [!IMPORTANT]
> ASC для Интернета вещей находится в общедоступной предварительной версии.
> Эта предварительная версия предоставляется без соглашения об уровне обслуживания и не рекомендована для использования рабочей среде. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены. Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

ASC для Интернета вещей **azureiotsecurity** модуль представляет собой решение полнофункциональных средств безопасности для устройства IoT Edge.
Модуль безопасности собирает, собирает и анализирует данные необработанной безопасности из операционной системы и контейнера системы в практические рекомендации по безопасности и оповещения.
Дополнительные сведения см. в разделе [модуль безопасности для IoT Edge](security-edge-architecture.md).

В этом руководстве вы узнаете, как развернуть модуль безопасности на вашем устройстве IoT Edge.

## <a name="deploy-security-module"></a>Развертывание модуля безопасности

Выполните следующие действия для развертывания ASC для модуля безопасности Интернета вещей для IoT Edge.

### <a name="prerequisites"></a>Технические условия

1. Убедитесь, что устройство является [зарегистрировано как устройство IoT Edge](https://docs.microsoft.com/en-us/azure/iot-edge/how-to-register-device-portal).

1. ASC для модуля Edge Интернета вещей требует [AuditD framework](https://linux.die.net/man/8/auditd) устанавливать на устройство Edge.

   Установите платформы, выполнив следующую команду на вашем устройстве Edge:
   
   `apt-get install auditd audispd-plugins`

### <a name="deployment-using-azure-portal"></a>Развертывание с помощью портала Azure

1. Откройте **Marketplace** на портале Azure.

1. Поиск **безопасности Интернета вещей azure** и щелкнуть **безопасности Интернета вещей Azure**.

   ![](media/howto/edge_onboarding_7.png)

1. Нажмите кнопку **Создать**.

1. Выберите ваш **подписки**, **центра Интернета вещей** и **имя устройства IoT Edge**, затем нажмите кнопку **создать**.

1. Нажмите кнопку **Далее** для **проверки развертывания**.

1. Убедитесь, что **edgeHub.settings.createOptions** настраивается следующим образом:

   `"createOptions": "{\"HostConfig\":{\"PortBindings\":{\"8883/tcp\":[{\"HostPort\":\"8883\"}],\"443/tcp\":[{\"HostPort\":\"443\"}],\"5671/tcp\":[{\"HostPort\":\"5671\"}]}}}"`

1. Нажмите кнопку **отправить** для завершения развертывания.


## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о параметрах конфигурации, продолжайте практическим руководством по конфигурации модуля. 
> [!div class="nextstepaction"]
> [Конфигурация модуля практического руководства](./how-to-agent-configuration.md)