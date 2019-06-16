---
title: Регистрация нового устройства с помощью командной строки — Azure IoT Edge | Документация Майкрософт
description: Регистрация нового устройства IoT Edge и получение строки подключения с помощью расширения Интернета вещей для Azure CLI
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/03/2019
ms.topic: conceptual
ms.reviewer: menchi
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: de00c317483da9bcd93bb2e2505350d787385925
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66495395"
---
# <a name="register-a-new-azure-iot-edge-device-with-azure-cli"></a>Регистрация нового устройства Azure IoT Edge с помощью Azure CLI

Чтобы использовать устройства Интернета вещей в Azure IoT Edge, нужно зарегистрировать их в Центре Интернета вещей. После регистрации устройства вы получите строку подключения, который может использоваться для настройки на устройстве IoT Edge рабочих нагрузок.

[Azure CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) — это кроссплатформенная программа командной строки с открытым кодом для управления ресурсами Azure (например, IoT Edge). Она позволяет управлять ресурсами Центра Интернета вещей Azure, экземплярами службы подготовки устройств и связанными концентраторами без дополнительной настройки. Новое расширение Интернета вещей расширяет функции интерфейса командной строки Azure (например, функция управления устройствами) и добавляет возможности IoT Edge.

В этой статье объясняется, как зарегистрировать новое устройство IoT Edge с помощью Azure CLI.

## <a name="prerequisites"></a>Технические условия

* [Центр Интернета вещей](../iot-hub/iot-hub-create-using-cli.md) в подписке Azure.
* [Интерфейс командной строки Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) в вашей среде. Вам понадобится как минимум Azure CLI версии 2.0.24 или более поздней. Для проверки используйте `az –-version`. Эта версия поддерживает команды расширения az и представляет собой платформу команд Knack.
* [Расширение Интернета вещей для Azure CLI](https://github.com/Azure/azure-iot-cli-extension).

## <a name="create-a-device"></a>Создание устройства

Используйте [создать удостоверение az iot hub устройства](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-device-identity-create) команду, чтобы создать новое удостоверение устройства в центре Интернета вещей. Пример:

   ```cli
   az iot hub device-identity create --device-id [device id] --hub-name [hub name] --edge-enabled
   ```

Эта команда принимает три параметра:

* **device-id**: содержит описательное имя устройства, уникальное в пределах Центра Интернета вещей;

* **hub-name**: содержит имя Центра Интернета вещей;

* **edge-enabled**: указывает, что устройство предназначено для использования с IoT Edge.

   ![Выходные данные команды az iot hub device-identity](./media/how-to-register-device-cli/Create-edge-device.png)

## <a name="view-all-devices"></a>Просмотр данных обо всех устройствах

Используйте [списка удостоверение устройства центра Интернета вещей az](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-device-identity-list) команду, чтобы просмотреть все устройства в центре Интернета вещей. Пример:

   ```cli
   az iot hub device-identity list --hub-name [hub name]
   ```

Для всех устройств, зарегистрированных в качестве устройства IoT Edge, свойство **capabilities.iotEdge** будет иметь значение **true**.

## <a name="retrieve-the-connection-string"></a>Получение строки подключения

Когда все будет готово к настройке устройства, вам понадобится строка подключения, которая связывает физическое устройство с его идентификатором в Центре Интернета вещей. Используйте [центр удостоверения устройства az iot show-connection-string](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-device-identity-show-connection-string) команду, чтобы получить строку подключения для одного устройства:

   ```cli
   az iot hub device-identity show-connection-string --device-id [device id] --hub-name [hub name]
   ```

В параметре `device-id` учитывается регистр. Копируйте строку подключения без кавычек.

## <a name="next-steps"></a>Дальнейшие действия

Узнайте, как [развертывание модулей на устройства с помощью Azure CLI](how-to-deploy-modules-cli.md).
