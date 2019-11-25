---
title: Register a new Azure IoT Edge device | Microsoft Docs
description: Регистрация нового устройства IoT Edge и получение строки подключения с помощью расширения Интернета вещей для Azure CLI
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 10/21/2019
ms.topic: conceptual
ms.reviewer: menchi
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 793ddcb9f218248c396e10f23201dfe905545ceb
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/24/2019
ms.locfileid: "74456850"
---
# <a name="register-an-azure-iot-edge-device"></a>Register an Azure IoT Edge device

Before you can use your IoT devices with Azure IoT Edge, you must register them with your IoT hub. Once a device is registered, you can retrieve a connection string to set up your device for IoT Edge workloads.

You have the choice of registering by using one of the following tools:

* [Azure portal](https://portal.azure.com) provides a graphical user interface to create, view, and manage Azure resources.
* [Visual Studio Code](https://code.visualstudio.com/) is a source-code editor. Azure IoT extensions make it easy to manage IoT resources from the same tool where you're developing IoT solutions.
* [Azure CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) is a command-line tool for managing Azure resources. Its reusable commands are helpful for automating tasks.

## <a name="register-in-the-azure-portal"></a>Register in the Azure portal

You can perform all registration tasks in the Azure portal.

### <a name="prerequisites-for-the-azure-portal"></a>Prerequisites for the Azure portal

A free or standard [IoT hub](../iot-hub/iot-hub-create-through-portal.md) in your Azure subscription.

### <a name="create-an-iot-edge-device-in-the-azure-portal"></a>Create an IoT Edge device in the Azure portal

In your IoT Hub in the Azure portal, IoT Edge devices are created and managed separately from IOT devices that are not edge enabled.

1. Войдите на [портал Azure](https://portal.azure.com) и перейдите к своему Центру Интернета вещей.
2. In the left pane, select **IoT Edge** from the menu.
3. Выберите **Добавить устройство IoT Edge**.
4. Укажите описательный идентификатор устройства. Use the default settings to auto-generate authentication keys and connect the new device to your hub.
5. Щелкните **Сохранить**.

### <a name="view-iot-edge-devices-in-the-azure-portal"></a>View IoT Edge devices in the Azure portal

Все устройства IoT Edge, подключенные к Центру Интернета вещей, перечислены на странице **IoT Edge**.

![Просмотр списка всех устройств IoT Edge в центре Интернета вещей](./media/how-to-register-device/portal-view-devices.png)

### <a name="retrieve-the-connection-string-in-the-azure-portal"></a>Retrieve the connection string in the Azure portal

Когда все будет готово к настройке устройства, вам понадобится строка подключения, которая связывает физическое устройство с его идентификатором в Центре Интернета вещей.

1. From the **IoT Edge** page in the portal, click on the device ID from the list of IoT Edge devices.
2. Скопируйте значение в поле **Строка подключения (первичный ключ)** или **Строка подключения (вторичный ключ)** .

## <a name="register-with-visual-studio-code"></a>Register with Visual Studio Code

VS Code позволяет выполнять большинство операций разными способами. This article uses the Explorer, but you can also use the Command Palette to run the steps.

### <a name="prerequisites-for-visual-studio-code"></a>Prerequisites for Visual Studio Code

* [Центр Интернета вещей](../iot-hub/iot-hub-create-through-portal.md) в подписке Azure.
* [Код Visual Studio](https://code.visualstudio.com/)
* [Средства Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) для Visual Studio Code.

### <a name="sign-in-to-access-your-iot-hub"></a>Войдите в систему для получения доступа к Центру Интернета вещей

You can use the Azure IoT extensions for Visual Studio Code to perform operations with your IoT Hub. For these operations to work, you need to sign in to your Azure account and select your IoT Hub.

1. Откройте в Visual Studio Code представление **Explorer**.
1. At the bottom of the Explorer, expand the **Azure IoT Hub** section.

   ![Развернутый раздел "Azure IoT Hub Devices" (Устройства Центра Интернета вещей Azure)](./media/how-to-register-device/azure-iot-hub-devices.png)

1. Click on the **...** in the **Azure IoT Hub** section header. Если значок многоточия не отображается, щелкните заголовок или наведите на него курсор.
1. Щелкните **Выбрать Центр Интернета вещей**.
1. If you aren't signed in to your Azure account, follow the prompts to do so.
1. Выберите подписку Azure.
1. Выберите нужный Центр Интернета вещей.

### <a name="create-an-iot-edge-device-with-visual-studio-code"></a>Create an IoT Edge device with Visual Studio Code

1. В обозревателе VS Code разверните раздел **Устройства Центра Интернета вещей Azure**.
1. Щелкните значок **...** в заголовке раздела **Устройства Центра Интернета вещей Azure**. Если значок многоточия не отображается, щелкните заголовок или наведите на него курсор.
1. Выберите **Создать устройство IoT Edge**.
1. В открывшемся текстовом поле введите идентификатор для нового устройства.

На экране выходных данных отобразится результат выполнения команды. Здесь содержатся сведения об устройстве, в том числе введенный вами идентификатор **deviceId** и строка подключения **connectionString**, которая понадобится для подключения физического устройства к Центру Интернета вещей.

На экране выходных данных отобразится результат выполнения команды. Здесь содержатся сведения об устройстве, в том числе введенный вами идентификатор **deviceId** и строка подключения **connectionString**, которая понадобится для подключения физического устройства к Центру Интернета вещей.

### <a name="view-iot-edge-devices-with-visual-studio-code"></a>View IoT Edge devices with Visual Studio Code

All the devices that connect to your IoT hub are listed in the **Azure IoT Hub** section of the Visual Studio Code Explorer. IoT Edge devices are distinguishable from non-Edge devices with a different icon, and the fact that the **$edgeAgent** and **$edgeHub** modules are deployed to each IoT Edge device.

![Просмотр списка всех устройств IoT Edge в центре Интернета вещей](./media/how-to-register-device/view-devices.png)

### <a name="retrieve-the-connection-string-with-visual-studio-code"></a>Retrieve the connection string with Visual Studio Code

Когда все будет готово к настройке устройства, вам понадобится строка подключения, которая связывает физическое устройство с его идентификатором в Центре Интернета вещей.

1. Right-click on the ID of your device in the **Azure IoT Hub** section.
1. Выберите действие **Копировать строку подключения для устройства**.

   Строка подключения будет помещена в буфер обмена.

Также вы можете выбрать пункт **Получить информацию об устройства** в контекстном меню, чтобы просмотреть в окне выходных данных сведения об этом устройстве, в том числе строку подключения.

## <a name="register-with-the-azure-cli"></a>Register with the Azure CLI

The [Azure CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) is an open-source cross platform command-line tool for managing Azure resources such as IoT Edge. Она позволяет управлять ресурсами Центра Интернета вещей Azure, экземплярами службы подготовки устройств и связанными концентраторами без дополнительной настройки. Новое расширение Интернета вещей расширяет функции интерфейса командной строки Azure (например, функция управления устройствами) и добавляет возможности IoT Edge.

### <a name="prerequisites-for-the-azure-cli"></a>Prerequisites for the Azure CLI

* [Центр Интернета вещей](../iot-hub/iot-hub-create-using-cli.md) в подписке Azure.
* [Интерфейс командной строки Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) в вашей среде. Вам понадобится как минимум Azure CLI версии 2.0.24 или более поздней. Для проверки используйте `az --version`. Эта версия поддерживает команды расширения az и представляет собой платформу команд Knack.
* [Расширение Интернета вещей для Azure CLI](https://github.com/Azure/azure-iot-cli-extension).

### <a name="create-an-iot-edge-device-with-the-azure-cli"></a>Create an IoT Edge device with the Azure CLI

Use the [az iot hub device-identity create](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-device-identity-create) command to create a new device identity in your IoT hub. Пример.

   ```cli
   az iot hub device-identity create --device-id [device id] --hub-name [hub name] --edge-enabled
   ```

Эта команда принимает три параметра:

* **device-id** содержит описательное имя устройства, уникальное в пределах Центра Интернета вещей;
* **hub-name** содержит имя Центра Интернета вещей;
* **edge-enabled** указывает, что устройство предназначено для использования с IoT Edge.

   ![Выходные данные команды az iot hub device-identity](./media/how-to-register-device/Create-edge-device.png)

### <a name="view-iot-edge-devices-with-the-azure-cli"></a>View IoT Edge devices with the Azure CLI

Use the [az iot hub device-identity list](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-device-identity-list) command to view all devices in your IoT hub. Пример.

   ```cli
   az iot hub device-identity list --hub-name [hub name]
   ```

Для всех устройств, зарегистрированных в качестве устройства IoT Edge, свойство **capabilities.iotEdge** будет иметь значение **true**.

### <a name="retrieve-the-connection-string-with-the-azure-cli"></a>Retrieve the connection string with the Azure CLI

Когда все будет готово к настройке устройства, вам понадобится строка подключения, которая связывает физическое устройство с его идентификатором в Центре Интернета вещей. Use the [az iot hub device-identity show-connection-string](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-device-identity-show-connection-string) command to return the connection string for a single device:

   ```cli
   az iot hub device-identity show-connection-string --device-id [device id] --hub-name [hub name]
   ```

В параметре `device-id` учитывается регистр. Копируйте строку подключения без кавычек.

## <a name="next-steps"></a>Дальнейшие действия

Now that you have a device identity registered in your IoT hub, you're ready to install the IoT Edge runtime on your devices. Install the runtime according to the device's operating system:

* [Install Azure IoT Edge on Windows](how-to-install-iot-edge-windows.md)
* [Install the Azure IoT Edge runtime on Linux](how-to-install-iot-edge-linux.md)
