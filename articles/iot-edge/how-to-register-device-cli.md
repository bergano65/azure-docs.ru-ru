---
title: Регистрация нового устройства с помощью командной строки — Azure IoT Edge | Документация Майкрософт
description: Регистрация нового устройства IoT Edge и получение строки подключения с помощью расширения Интернета вещей для Azure CLI
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/03/2019
ms.topic: conceptual
ms.reviewer: menchi
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 45b05498702042c931df3765b9e1bd79489dbb6e
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/02/2019
ms.locfileid: "53972147"
---
# <a name="register-a-new-azure-iot-edge-device-with-azure-cli"></a>Регистрация нового устройства Azure IoT Edge с помощью Azure CLI

Чтобы использовать устройства Интернета вещей в Azure IoT Edge, нужно зарегистрировать их в Центре Интернета вещей. После регистрации устройства вы получите строку подключения, которую можно использовать при настройке устройства для рабочих нагрузок Edge.

[Azure CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) — это кроссплатформенная программа командной строки с открытым кодом для управления ресурсами Azure (например, IoT Edge). Она позволяет управлять ресурсами Центра Интернета вещей Azure, экземплярами службы подготовки устройств и связанными концентраторами без дополнительной настройки. Новое расширение Интернета вещей расширяет функции интерфейса командной строки Azure (например, функция управления устройствами) и добавляет возможности IoT Edge.

В этой статье объясняется, как зарегистрировать новое устройство IoT Edge с помощью Azure CLI.

## <a name="prerequisites"></a>Предварительные требования

* [Центр Интернета вещей](../iot-hub/iot-hub-create-using-cli.md) в подписке Azure.
* [Интерфейс командной строки Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) в вашей среде. Вам понадобится как минимум Azure CLI версии 2.0.24 или более поздней. Для проверки используйте `az –-version`. Эта версия поддерживает команды расширения az и представляет собой платформу команд Knack.
* [Расширение Интернета вещей для Azure CLI](https://github.com/Azure/azure-iot-cli-extension).

## <a name="create-a-device"></a>Создание устройства

Чтобы создать идентификатор устройства в Центре Интернета вещей, используйте следующую команду:

   ```cli
   az iot hub device-identity create --device-id [device id] --hub-name [hub name] --edge-enabled
   ```

Эта команда принимает три параметра:

* **device-id**: содержит описательное имя устройства, уникальное в пределах Центра Интернета вещей;

* **hub-name**: содержит имя Центра Интернета вещей;

* **edge-enabled**: указывает, что устройство предназначено для использования с IoT Edge.

   ![Выходные данные команды az iot hub device-identity](./media/how-to-register-device-cli/Create-edge-device.png)

## <a name="view-all-devices"></a>Просмотр данных обо всех устройствах

Следующая команда позволяет просмотреть все устройства в Центре Интернета вещей:

   ```cli
   az iot hub device-identity list --hub-name [hub name]
   ```

Для всех устройств, зарегистрированных в качестве устройства IoT Edge, свойство **capabilities.iotEdge** будет иметь значение **true**.

## <a name="retrieve-the-connection-string"></a>Получение строки подключения

Когда все будет готово к настройке устройства, вам понадобится строка подключения, которая связывает физическое устройство с его идентификатором в Центре Интернета вещей. Используйте следующую команду, чтобы получить строку подключения для одного устройства.

   ```cli
   az iot hub device-identity show-connection-string --device-id [device id] --hub-name [hub name]
   ```

В параметре `device-id` учитывается регистр. Копируйте строку подключения без кавычек.

## <a name="next-steps"></a>Дополнительная информация

Подробнее о [развертывании модулей на устройстве с помощью Azure CLI](how-to-deploy-modules-cli.md)
