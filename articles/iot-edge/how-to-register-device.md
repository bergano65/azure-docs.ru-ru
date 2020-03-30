---
title: Зарегистрируйте новое устройство Azure IoT Edge Документы Майкрософт
description: Регистрация нового устройства IoT Edge и получение строки подключения с помощью расширения Интернета вещей для Azure CLI
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/08/2020
ms.topic: conceptual
ms.reviewer: menchi
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 6fb776b4c1ff537401a23eb272526b3043fdb1e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80235727"
---
# <a name="register-an-azure-iot-edge-device"></a>Регистрация устройства Azure IoT Edge

Прежде чем использовать устройства IoT с Azure IoT Edge, необходимо зарегистрировать их в своем концентраторе IoT. После регистрации устройства можно получить строку соединения для настройки устройства для рабочих нагрузок IoT Edge.

У вас есть выбор регистрации с помощью одного из следующих инструментов:

* [Зарегистрируйте устройство на портале Azure,](#register-in-the-azure-portal) если вы предпочитаете графический пользовательский интерфейс для создания, просмотра и управления ресурсами Azure.
* [Зарегистрируйте устройство с кодом Visual Studio,](#register-with-visual-studio-code) если вы предпочитаете управлять ресурсами Azure IoT в том же месте, где вы разрабатываете решения IoT.
* [Зарегистрируйте устройство с помощью Azure CLI,](#register-with-the-azure-cli) если вы предпочитаете инструменты командной строки для управления ресурсами Azure или собираетесь автоматизировать задачи.

## <a name="register-in-the-azure-portal"></a>Зарегистрируйтесь на портале Azure

Вы можете выполнять все регистрационные задачи на портале Azure.

### <a name="prerequisites-for-the-azure-portal"></a>Предпосылки для портала Azure

Бесплатный или стандартный [концентратор IoT](../iot-hub/iot-hub-create-through-portal.md) в подписке Azure.

### <a name="create-an-iot-edge-device-in-the-azure-portal"></a>Создание устройства IoT Edge на портале Azure

В концентраторе IoT на портале Azure устройства IoT Edge создаются и управляются отдельно от устройств IOT, которые не имеют преимущества.

1. Войдите на [портал Azure](https://portal.azure.com) и перейдите к концентратору IoT.
2. В левом стеле выберите **IoT Edge** из меню.
3. Выберите **Добавить устройство IoT Edge**.
4. Укажите описательный идентификатор устройства. Используйте настройки по умолчанию для автоматического создания ключей проверки подлинности и подключения нового устройства к концентратору.
5. Нажмите кнопку **Сохранить**.

### <a name="view-iot-edge-devices-in-the-azure-portal"></a>Просмотр устройств IoT Edge на портале Azure

Все устройства IoT Edge, подключенные к Центру Интернета вещей, перечислены на странице **IoT Edge**.

![Просмотр списка всех устройств IoT Edge в центре Интернета вещей](./media/how-to-register-device/portal-view-devices.png)

### <a name="retrieve-the-connection-string-in-the-azure-portal"></a>Извлечения строки соединения на портале Azure

Когда все будет готово к настройке устройства, вам понадобится строка подключения, которая связывает физическое устройство с его идентификатором в Центре Интернета вещей.

1. Со страницы **IoT Edge** на портале нажмите на идентификатор устройства из списка устройств IoT Edge.
2. Копирование значения либо **основной строки подключения** или **вторичной строки подключения**.

## <a name="register-with-visual-studio-code"></a>Зарегистрируйтесь с кодом Visual Studio

VS Code позволяет выполнять большинство операций разными способами. В этой статье используется Explorer, но вы также можете использовать палитру команд для выполнения шагов.

### <a name="prerequisites-for-visual-studio-code"></a>Предпосылки для визуального кода студии

* [Центр Интернета вещей](../iot-hub/iot-hub-create-through-portal.md) в подписке Azure.
* [Visual Studio Code](https://code.visualstudio.com/)
* [Инструменты Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) для визуального кода студии

### <a name="sign-in-to-access-your-iot-hub"></a>Вход в систему для получения доступа к Центру Интернета вещей

Расширения Azure IoT для Visual Studio Code можно использовать для выполнения операций с концентратором IoT. Чтобы эти операции работали, необходимо войти в учетную запись Azure и выбрать концентратор IoT.

1. Откройте в Visual Studio Code представление **Explorer**.
1. В нижней части Explorer расширьте раздел **Azure IoT Hub.**

   ![Развернутый раздел "Azure IoT Hub Devices" (Устройства Центра Интернета вещей Azure)](./media/how-to-register-device/azure-iot-hub-devices.png)

1. Нажмите на **раздел** **Azure IoT Hub.** Если значок многоточия не отображается, щелкните заголовок или наведите на него курсор.
1. Щелкните **Выбрать Центр Интернета вещей**.
1. Если вы не зарегистрировались в учетной записи Azure, следуйте подсказкам.
1. Выберите подписку Azure.
1. Выберите нужный Центр Интернета вещей.

### <a name="create-an-iot-edge-device-with-visual-studio-code"></a>Создание устройства IoT Edge с помощью кода Visual Studio

1. В обозревателе VS Code разверните раздел **Устройства Центра Интернета вещей Azure**.
1. Щелкните значок **...** в заголовке раздела **Устройства Центра Интернета вещей Azure**. Если значок многоточия не отображается, щелкните заголовок или наведите на него курсор.
1. Выберите **Создать устройство IoT Edge**.
1. В открывшемся текстовом поле введите идентификатор для нового устройства.

На экране выходных данных отобразится результат выполнения команды. Здесь содержатся сведения об устройстве, в том числе введенный вами идентификатор **deviceId** и строка подключения **connectionString**, которая понадобится для подключения физического устройства к Центру Интернета вещей.

На экране выходных данных отобразится результат выполнения команды. Здесь содержатся сведения об устройстве, в том числе введенный вами идентификатор **deviceId** и строка подключения **connectionString**, которая понадобится для подключения физического устройства к Центру Интернета вещей.

### <a name="view-iot-edge-devices-with-visual-studio-code"></a>Просмотр устройств IoT Edge с помощью кода Visual Studio

Все устройства, подключенные к концентратору IoT, перечислены в разделе **Azure IoT Hub** Visual Studio Code Explorer. Устройства IoT Edge отличаются от устройств, не являющиеся edge с другим значком, а также тем фактом, что **$edgeAgent** и **$edgeHub** модули развернуты на каждом устройстве IoT Edge.

![Просмотр списка всех устройств IoT Edge в центре Интернета вещей](./media/how-to-register-device/view-devices.png)

### <a name="retrieve-the-connection-string-with-visual-studio-code"></a>Извлеките строку соединения с кодом Visual Studio

Когда все будет готово к настройке устройства, вам понадобится строка подключения, которая связывает физическое устройство с его идентификатором в Центре Интернета вещей.

1. Нажмите правой кнопкой мыши на идентификаторе устройства в разделе **Azure IoT Hub.**
1. Выберите действие **Копировать строку подключения для устройства**.

   Строка подключения будет помещена в буфер обмена.

Также вы можете выбрать пункт **Получить информацию об устройства** в контекстном меню, чтобы просмотреть в окне выходных данных сведения об этом устройстве, в том числе строку подключения.

## <a name="register-with-the-azure-cli"></a>Зарегистрируйтесь в Azure CLI

[Azure CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) — это кросс-платформенный командный инструмент с открытым исходным кодом для управления ресурсами Azure, такими как IoT Edge. Она позволяет управлять ресурсами Центра Интернета вещей Azure, экземплярами службы подготовки устройств и связанными концентраторами без дополнительной настройки. Расширение Интернета вещей расширяет функции Azure CLI (например, функция управления устройствами) и добавляет возможности IoT Edge.

### <a name="prerequisites-for-the-azure-cli"></a>Предпосылки для Azure CLI

* [Концентратор IoT](../iot-hub/iot-hub-create-using-cli.md) в подписке Azure.
* [Интерфейс командной строки Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) в вашей среде. Как минимум, ваша версия Azure CLI должна быть 2.0.70 или выше. Для проверки используйте `az --version`. Эта версия поддерживает команды расширения az и представляет собой платформу команд Knack.
* [Расширение IoT для Azure CLI.](https://github.com/Azure/azure-iot-cli-extension)

### <a name="create-an-iot-edge-device-with-the-azure-cli"></a>Создание устройства IoT Edge с помощью Azure CLI

Используйте команду [создания устройства-идентификации концентратора аз-йота](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-device-identity-create) для создания нового удостоверения устройства в концентраторе IoT. Пример:

   ```azurecli
   az iot hub device-identity create --device-id [device id] --hub-name [hub name] --edge-enabled
   ```

Эта команда принимает три параметра:

* **device-id** содержит описательное имя устройства, уникальное в пределах Центра Интернета вещей;
* **hub-name** содержит имя Центра Интернета вещей;
* **edge-enabled** указывает, что устройство предназначено для использования с IoT Edge.

   ![Выходные данные команды az iot hub device-identity](./media/how-to-register-device/Create-edge-device.png)

### <a name="view-iot-edge-devices-with-the-azure-cli"></a>Просмотр устройств IoT Edge с помощью Azure CLI

Используйте команду [списка концентраторных устройств-идентификационных устройств аз-йота,](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-device-identity-list) чтобы просматривать все устройства в вашем концентраторе IoT. Пример:

   ```azurecli
   az iot hub device-identity list --hub-name [hub name]
   ```

Для всех устройств, зарегистрированных в качестве устройства IoT Edge, свойство **capabilities.iotEdge** будет иметь значение **true**.

### <a name="retrieve-the-connection-string-with-the-azure-cli"></a>Извлеките строку соединения с Azure CLI

Когда все будет готово к настройке устройства, вам понадобится строка подключения, которая связывает физическое устройство с его идентификатором в Центре Интернета вещей. Используйте команду [az iot hub device-identity show-connection-string,](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-device-identity-show-connection-string) чтобы вернуть строку соединения для одного устройства:

   ```azurecli
   az iot hub device-identity show-connection-string --device-id [device id] --hub-name [hub name]
   ```

В параметре `device-id` учитывается регистр. Копируйте строку подключения без кавычек.

## <a name="next-steps"></a>Дальнейшие действия

Теперь, когда в концентраторе IoT зарегистрировано удостоверение устройства, вы готовы установить время выполнения IoT Edge на устройствах. Установите время выполнения в соответствии с операционной системой устройства:

* [Установка Azure IoT Edge на Windows](how-to-install-iot-edge-windows.md)
* [Установка времени выполнения Azure IoT Edge на Linux](how-to-install-iot-edge-linux.md)
