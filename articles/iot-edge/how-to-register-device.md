---
title: Регистрация нового устройства Azure IoT Edge | Документация Майкрософт
description: Регистрация нового устройства IoT Edge и получение строки подключения с помощью расширения Интернета вещей для Azure CLI
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 10/21/2019
ms.topic: conceptual
ms.reviewer: menchi
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 65fb55a16e358624dceb42a268b035f826576f63
ms.sourcegitcommit: c4700ac4ddbb0ecc2f10a6119a4631b13c6f946a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/27/2019
ms.locfileid: "72966259"
---
# <a name="register-an-azure-iot-edge-device"></a>Регистрация устройства Azure IoT Edge

Прежде чем вы сможете использовать устройства IoT с Azure IoT Edge, необходимо зарегистрировать их в центре Интернета вещей. После регистрации устройства можно получить строку подключения, чтобы настроить устройство для IoT Edge рабочих нагрузок.

Вы можете зарегистрировать с помощью одного из следующих средств:

* [Портал Azure](https://portal.azure.com) предоставляет графический пользовательский интерфейс для создания, просмотра ресурсов Azure и управления ими.
* [Visual Studio Code](https://code.visualstudio.com/) является редактором исходного кода. Расширения Интернета вещей Azure упрощают управление ресурсами Интернета вещей из того же средства, в котором разрабатываются решения Интернета вещей.
* [Azure CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) — это программа командной строки для управления ресурсами Azure. Его многократно используемые команды полезны для автоматизации задач.

## <a name="register-in-the-azure-portal"></a>Регистрация в портал Azure

Все задачи регистрации можно выполнять в портал Azure.

### <a name="prerequisites-for-the-azure-portal"></a>Необходимые условия для портал Azure

Бесплатный или стандартный [центр Интернета вещей](../iot-hub/iot-hub-create-through-portal.md) в подписке Azure.

### <a name="create-an-iot-edge-device-in-the-azure-portal"></a>Создание устройства IoT Edge в портал Azure

В центре Интернета вещей в портал Azure IoT Edge устройства создаются и управляются отдельно от устройств IOT, которые не включены в пограничные устройства.

1. Войдите на [портал Azure](https://portal.azure.com) и перейдите к своему Центру Интернета вещей.
2. В левой области выберите **IOT Edge** в меню.
3. Выберите **Добавить устройство IoT Edge**.
4. Укажите описательный идентификатор устройства. Используйте параметры по умолчанию для автоматического создания ключей проверки подлинности и подключения нового устройства к концентратору.
5. Щелкните **Сохранить**.

### <a name="view-iot-edge-devices-in-the-azure-portal"></a>Просмотр IoT Edge устройств в портал Azure

Все устройства IoT Edge, подключенные к Центру Интернета вещей, перечислены на странице **IoT Edge**.

![Просмотр списка всех устройств IoT Edge в центре Интернета вещей](./media/how-to-register-device/portal-view-devices.png)

### <a name="retrieve-the-connection-string-in-the-azure-portal"></a>Получение строки подключения в портал Azure

Когда все будет готово к настройке устройства, вам понадобится строка подключения, которая связывает физическое устройство с его идентификатором в Центре Интернета вещей.

1. На странице **IOT Edge** на портале щелкните идентификатор устройства в списке устройств IOT Edge.
2. Скопируйте значение в поле **Строка подключения (первичный ключ)** или **Строка подключения (вторичный ключ)** .

## <a name="register-with-visual-studio-code"></a>Регистрация в Visual Studio Code

VS Code позволяет выполнять большинство операций разными способами. В этой статье используется обозреватель, но можно также использовать палитру команд для выполнения этих действий.

### <a name="prerequisites-for-visual-studio-code"></a>Необходимые условия для Visual Studio Code

* [Центр Интернета вещей](../iot-hub/iot-hub-create-through-portal.md) в подписке Azure.
* [Visual Studio Code](https://code.visualstudio.com/)
* [Средства Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) для Visual Studio Code.

### <a name="sign-in-to-access-your-iot-hub"></a>Войдите в систему для получения доступа к Центру Интернета вещей

Вы можете использовать расширения Azure IoT для Visual Studio Code для выполнения операций с центром Интернета вещей. Чтобы эти операции работали, необходимо войти в учетную запись Azure и выбрать центр Интернета вещей.

1. Откройте в Visual Studio Code представление **Explorer**.
1. В нижней части обозревателя разверните раздел **центр Интернета вещей Azure** .

   ![Развернутый раздел "Azure IoT Hub Devices" (Устройства Центра Интернета вещей Azure)](./media/how-to-register-device/azure-iot-hub-devices.png)

1. Щелкните.. **.** в заголовке раздела центра **Интернета вещей Azure** . Если значок многоточия не отображается, щелкните заголовок или наведите на него курсор.
1. Щелкните **Выбрать Центр Интернета вещей**.
1. Если вы не вошли в учетную запись Azure, следуйте инструкциям на экране.
1. Выберите подписку Azure.
1. Выберите нужный Центр Интернета вещей.

### <a name="create-an-iot-edge-device-with-visual-studio-code"></a>Создание устройства IoT Edge с помощью Visual Studio Code

1. В обозревателе VS Code разверните раздел **Устройства Центра Интернета вещей Azure**.
1. Щелкните значок **...** в заголовке раздела **Устройства Центра Интернета вещей Azure**. Если значок многоточия не отображается, щелкните заголовок или наведите на него курсор.
1. Выберите **Создать устройство IoT Edge**.
1. В открывшемся текстовом поле введите идентификатор для нового устройства.

На экране выходных данных отобразится результат выполнения команды. Здесь содержатся сведения об устройстве, в том числе введенный вами идентификатор **deviceId** и строка подключения **connectionString**, которая понадобится для подключения физического устройства к Центру Интернета вещей.

На экране выходных данных отобразится результат выполнения команды. Здесь содержатся сведения об устройстве, в том числе введенный вами идентификатор **deviceId** и строка подключения **connectionString**, которая понадобится для подключения физического устройства к Центру Интернета вещей.

### <a name="view-iot-edge-devices-with-visual-studio-code"></a>Просмотр IoT Edge устройств с помощью Visual Studio Code

Все устройства, подключающиеся к центру Интернета вещей, перечислены в разделе **центра Интернета вещей Azure** обозревателя Visual Studio Code. IoT Edge устройства отличаются от устройств, не являющихся пограничными, с помощью другого значка и того факта, что **$edgeAgent** и **$edgeHub** модули развертываются на каждом устройстве IOT Edge.

![Просмотр списка всех устройств IoT Edge в центре Интернета вещей](./media/how-to-register-device/view-devices.png)

### <a name="retrieve-the-connection-string-with-visual-studio-code"></a>Получение строки подключения с помощью Visual Studio Code

Когда все будет готово к настройке устройства, вам понадобится строка подключения, которая связывает физическое устройство с его идентификатором в Центре Интернета вещей.

1. Щелкните идентификатор устройства правой кнопкой мыши в разделе **центра Интернета вещей Azure** .
1. Выберите действие **Копировать строку подключения для устройства**.

   Строка подключения будет помещена в буфер обмена.

Также вы можете выбрать пункт **Получить информацию об устройства** в контекстном меню, чтобы просмотреть в окне выходных данных сведения об этом устройстве, в том числе строку подключения.

## <a name="register-with-the-azure-cli"></a>Регистрация в Azure CLI

[Azure CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) — это программа командной строки с открытым исходным кодом для управления ресурсами Azure, например IOT Edge. Она позволяет управлять ресурсами Центра Интернета вещей Azure, экземплярами службы подготовки устройств и связанными концентраторами без дополнительной настройки. Новое расширение Интернета вещей расширяет функции интерфейса командной строки Azure (например, функция управления устройствами) и добавляет возможности IoT Edge.

### <a name="prerequisites-for-the-azure-cli"></a>Необходимые условия для Azure CLI

* [Центр Интернета вещей](../iot-hub/iot-hub-create-using-cli.md) в подписке Azure.
* [Интерфейс командной строки Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) в вашей среде. Вам понадобится как минимум Azure CLI версии 2.0.24 или более поздней. Для проверки используйте `az --version`. Эта версия поддерживает команды расширения az и представляет собой платформу команд Knack.
* [Расширение Интернета вещей для Azure CLI](https://github.com/Azure/azure-iot-cli-extension).

### <a name="create-an-iot-edge-device-with-the-azure-cli"></a>Создание IoT Edge устройства с Azure CLI

Чтобы создать удостоверение устройства в центре Интернета вещей, используйте команду [AZ IOT Hub Device-Identity Create](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-device-identity-create) . Пример.

   ```cli
   az iot hub device-identity create --device-id [device id] --hub-name [hub name] --edge-enabled
   ```

Эта команда принимает три параметра:

* **device-id** содержит описательное имя устройства, уникальное в пределах Центра Интернета вещей;
* **hub-name** содержит имя Центра Интернета вещей;
* **edge-enabled** указывает, что устройство предназначено для использования с IoT Edge.

   ![Выходные данные команды az iot hub device-identity](./media/how-to-register-device/Create-edge-device.png)

### <a name="view-iot-edge-devices-with-the-azure-cli"></a>Просмотр IoT Edge устройств с Azure CLI

Чтобы просмотреть все устройства в центре Интернета вещей, используйте команду [AZ IOT Hub Device-Identity List](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-device-identity-list) . Пример.

   ```cli
   az iot hub device-identity list --hub-name [hub name]
   ```

Для всех устройств, зарегистрированных в качестве устройства IoT Edge, свойство **capabilities.iotEdge** будет иметь значение **true**.

### <a name="retrieve-the-connection-string-with-the-azure-cli"></a>Получение строки подключения с помощью Azure CLI

Когда все будет готово к настройке устройства, вам понадобится строка подключения, которая связывает физическое устройство с его идентификатором в Центре Интернета вещей. Чтобы вернуть строку подключения для одного устройства, используйте команду [AZ IOT Hub Device-Identity показывать-Connection-String](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-device-identity-show-connection-string) .

   ```cli
   az iot hub device-identity show-connection-string --device-id [device id] --hub-name [hub name]
   ```

В параметре `device-id` учитывается регистр. Копируйте строку подключения без кавычек.

## <a name="next-steps"></a>Дальнейшие действия

Теперь, когда удостоверение устройства зарегистрировано в центре Интернета вещей, вы можете установить среду выполнения IoT Edge на устройствах. Установите среду выполнения в соответствии с операционной системой устройства:

* [Установка Azure IoT Edge в Windows](how-to-install-iot-edge-windows.md)
* [Установка среды выполнения Azure IoT Edge в Linux](how-to-install-iot-edge-linux.md)
