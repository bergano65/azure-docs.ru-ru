---
title: включить файл
description: включить файл
services: iot-hub
author: dominicbetts
ms.service: iot-hub
ms.topic: include
ms.date: 09/07/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 68260bf8aafbbe5afd46ec7dfb763eb88ee2123e
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2020
ms.locfileid: "78892978"
---
В этом разделе описано, как с помощью Azure CLI создать удостоверение устройства для этой статьи. Идентификаторы устройств чувствительны к регистру.

1. Откройте [Azure Cloud Shell](https://shell.azure.com/).

1. В Azure Cloud Shell выполните следующую команду, чтобы установить Microsoft Azure расширение Интернета вещей для Azure CLI:

    ```azurecli-interactive
    az extension add --name azure-iot
    ```

2. Создайте новое удостоверение устройства с именем `myDeviceId` и получите строку подключения устройства с помощью следующих команд:

    ```azurecli-interactive
    az iot hub device-identity create --device-id myDeviceId --hub-name {Your IoT Hub name}
    az iot hub device-identity show-connection-string --device-id myDeviceId --hub-name {Your IoT Hub name} -o table
    ```

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

Запишите строку подключения устройства из результата. Строка подключения этого устройства используется приложением устройства для подключения к Центру Интернета вещей в качестве устройства.

<!-- images and links -->
