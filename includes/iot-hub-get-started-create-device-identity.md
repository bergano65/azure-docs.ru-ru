---
title: включение файла
description: включение файла
services: iot-hub
author: dominicbetts
ms.service: iot-hub
ms.topic: include
ms.date: 09/07/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: e80033d696de1b83da43fc27e5be9eca3b3f8757
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/07/2018
ms.locfileid: "44168848"
---
## <a name="create-a-device-identity"></a>Создание удостоверения устройства

В этом разделе рассматривается создание удостоверения устройства для этого руководства с помощью Azure CLI. Интерфейс Azure CLI предварительно устанавливается в [Azure Cloud Shell](https://docs.microsoft.com/zure/cloud-shell/overview). Также его можно [установить локально](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). Идентификаторы устройств чувствительны к регистру.

1. Выполните следующую команду в среде командной строки, где используется Azure CLI, чтобы установить расширение Интернета вещей:

    ```cmd/sh
    az extension add --name azure-cli-iot-ext
    ```

1. Если вы используете Azure CLI локально, используйте следующую команду для входа в учетную запись Azure (если вы используете Cloud Shell, вход выполняется автоматически и выполнение этой команды не требуется):

    ```cmd/sh
    az login
    ```

1. Наконец, создайте удостоверение устройства с именем `myDeviceId` и получите строку подключения устройства с помощью следующих команд:

    ```cmd/sh
    az iot hub device-identity create --device-id myDeviceId --hub-name {Your IoT Hub name}
    az iot hub device-identity show-connection-string --device-id myDeviceId --hub-name {Your IoT Hub name} -o table
    ```

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

Запишите строку подключения устройства из результата. Строка подключения этого устройства используется приложением устройства для подключения к Центру Интернета вещей в качестве устройства.

<!-- images and links -->
