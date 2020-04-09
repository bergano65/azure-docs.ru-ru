---
title: Подключите IoT DevKit A3166 к концентратору Azure IoT
description: В этом руководстве содержатся сведения о том, как настроить и подключить плату IoT DevKit AZ3166 к Центру Интернета вещей Azure и передавать с нее данные в облачную платформу Azure.
author: wesmc7777
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 06/25/2019
ms.author: wesmc
ms.openlocfilehash: 326b79e1aca6fa82b3275249401c755428a8b71d
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878567"
---
# <a name="connect-iot-devkit-az3166-to-azure-iot-hub"></a>Подключение платы IoT DevKit AZ3166 к Центру Интернета вещей Azure

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

Плату [MXChip IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/) можно использовать для разработки и прототипирования решений Интернета вещей, использующих службы Microsoft Azure. Она включает в себя arduino-совместимые доски с богатыми периферийными и датчиками, с открытым исходным кодом пакет, и богатый [образец галереи](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/).

## <a name="what-you-learn"></a>Что вы узнаете

* Как создать Центр Интернета вещей и зарегистрировать устройство для MXChip IoT DevKit.
* Как подключить IoT DevKit к Wi-Fi и настроить строку подключения Концентратора IoT.
* Как отправить данные телеметрии датчика DevKit в ваш концентратор IoT.
* Как подготовить среду разработки и разработать приложение для IoT DevKit.

У вас еще нет платы DevKit? Воспользуйтесь [симулятором платы DevKit](https://azure-samples.github.io/iot-devkit-web-simulator/) или [приобретите ее](https://aka.ms/iot-devkit-purchase).

Вы можете найти исходный код для всех уроков DevKit из [галереи образцов кода.](https://docs.microsoft.com/samples/browse/?term=mxchip)

## <a name="what-you-need"></a>Необходимые элементы

* Доска MXChip IoT DevKit с кабелем Micro-USB. Вы можете [получить ее прямо сейчас](https://aka.ms/iot-devkit-purchase).
* Компьютер под управлением Windows 10, macOS 10.10 или Ubuntu 18.04.
* Активная подписка Azure. Вы можете активировать [бесплатную 30-дневную пробную учетную запись Microsoft Azure](https://azureinfo.microsoft.com/us-freetrial.html).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]
  
## <a name="prepare-your-hardware"></a>Подготовка оборудования

Подключите к компьютеру следующее оборудование.

* Плата DevKit.
* Кабель micro USB.

![Необходимое оборудование](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/hardware.jpg)

Чтобы подключить плату DevKit к компьютеру, сделайте следующее:

1. Подключите разъем USB кабеля к компьютеру.

2. Подключите разъем micro USB кабеля к плате DevKit.

3. Зеленый светодиодный индикатор питания будет означать, что подключение установлено.

   ![Подключение оборудования](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/connect.jpg)

## <a name="quickstart-send-telemetry-from-devkit-to-an-iot-hub"></a>Быстрый запуск: Отправить телеметрию из DevKit в концентратор IoT

Быстрый запуск использует предварительно составленную прошивку DevKit для отправки телеметрии в концентратор IoT. Перед запуском необходимо создать концентратор IoT и зарегистрировать устройство в концентраторе.

### <a name="create-an-iot-hub"></a>Создание Центра Интернета вещей

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

### <a name="register-a-device"></a>Регистрация устройства

Устройство должно быть зарегистрировано в Центре Интернета вещей, прежде чем оно сможет подключиться. В этом кратком руководстве для регистрации имитируемого устройства используется Azure Cloud Shell.

1. Выполните приведенные ниже команды в Azure Cloud Shell, чтобы создать удостоверение устройства.

   **YourIoTHubName.** Замените этот заполнитель именем вашего центра Интернета вещей.

   **MyNodeDevice**: Название зарегистрированного устройства. Используйте **MyNodeDevice** в показано. Если вы выбрали другое имя для устройства, используйте его при работе с этим руководством и обновите имя устройства в примерах приложений перед их запуском.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyNodeDevice
    ```

   > [!NOTE]
   > При запуске `device-identity`ошибки установите [расширение Azure IoT для Azure CLI.](https://github.com/Azure/azure-iot-cli-extension/blob/dev/README.md)
   > Выполните следующую команду, чтобы добавить расширение Интернета вещей Microsoft Azure для Azure CLI в экземпляр Cloud Shell. Расширение IoT добавляет команды, характерные для IoT Hub, IoT Edge и службы обеспечения устройств IoT (DPS) в Azure CLI.
   > 
   > ```azurecli-interactive
   > az extension add --name azure-iot
   >  ```
   >
  
1. Запустите следующие команды в Azure Cloud Shell, чтобы получить _строку подключения устройства_ для только что зарегистрированного устройства:

   **YourIoTHubName.** Замените этот заполнитель именем вашего центра Интернета вещей.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name YourIoTHubName --device-id MyNodeDevice --output table
    ```

    Запишите строку подключения устройства, которая выглядит так:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyNodeDevice;SharedAccessKey={YourSharedAccessKey}`

    Это значение понадобится позже в рамках этого краткого руководства.

### <a name="send-devkit-telemetry"></a>Отправить Телеметрию DevKit

DevKit подключается к конкретной точке устройства на вашем концентраторе IoT и отправляет телеметрию температуры и влажности.

1. Загрузите последнюю версию [прошивки GetStarted](https://aka.ms/devkit/prod/getstarted/latest) для IoT DevKit.

1. Убедитесь, что IoT DevKit подключается к компьютеру через USB. Open File Explorer имеет устройство массового хранения USB под названием **A3166**.

    ![Откройте проводник.](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/az3166-usb.png)

1. Перетащите и падение прошивки просто загружены в массовое устройство хранения, и он будет мигать автоматически.

    ![Копирование прошивки](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/copy-firmware.png)

1. На DevKit, удерживайте кнопку **B,** нажмите и отпустите кнопку **сброса,** а затем отпустите кнопку **B.** Ваш DevKit входит в режим AP. Для подтверждения экрана отображается идентификатор набора услуг (SSID) DevKit и IP-адрес портала конфигурации.

    ![Кнопка Reset (Сброс), кнопка B и идентификатор SSID](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/wifi-ap.jpg)

    ![Задание режима точки беспроводного доступа](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/set-ap-mode.gif)

1. Используйте веб-браузер на другом устройстве с поддержкой Wi-Fi (компьютер или мобильный телефон) для подключения к IoT DevKit SSID, отображаемому на предыдущем этапе. Если он запрашивает пароль, оставьте его пустым.

    ![Подключите SSID](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/connect-ssid.png)

1. Открыт **192.168.0.1** в браузере. Выберите Wi-Fi, к который вы хотите подключить СяО DevKit, введите пароль Wi-Fi, а затем вставьте строку подключения устройства, о которой вы ранее отмечали. Нажмите кнопку "Сохранить".

    ![Конфигурация UI](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/configuration-ui.png)

    > [!NOTE]
    > IoT DevKit поддерживает только сеть 2,4 ГГц. Дополнительные сведения см. в разделе [Ошибка разрешения Homebrew в macOS](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#wi-fi-configuration).

1. Строка подключения к Информации и устройства Устройства будет храниться в IoT DevKit, когда вы видите страницу результата.

    ![Результат конфигурации](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/configuration-ui-result.png)

    > [!NOTE]
    > После настройки Wi-Fi учетные данные для этого подключения сохранятся на устройстве, даже если оно будет отсоединено.

1. IoT DevKit перезагружается за несколько секунд. На экране DevKit вы видите IP-адрес DevKit, следует из данных телеметрии, включая значение температуры и влажности с количеством сообщений, отправляемым в Azure IoT Hub.

    ![WiFi IP](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/wifi-ip.jpg)

    ![Отправка данных](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/sending-data.jpg)

1. Чтобы проверить данные телеметрии, отправленные в Azure, запустите следующую команду в Azure Cloud Shell:

    ```azurecli
    az iot hub monitor-events --hub-name YourIoTHubName --output table
    ```

## <a name="prepare-the-development-environment"></a>Подготовка среды разработки

Выполните следующие действия, чтобы подготовить среду разработки для DevKit:

### <a name="install-visual-studio-code-with-azure-iot-tools-extension-package"></a>Установка визуального кода студии с пакетом расширения Azure IoT Tools

1. Установите [интегрированную среду разработки Arduino](https://www.arduino.cc/en/Main/Software). Она предоставляет необходимую цепочку инструментов для компиляции и передачи кода Arduino.
    * **Windows**. Используйте версию установщика Windows. Не устанавливайте из App Store.
    * **macOS**. Перетащите и отпустите извлеченное приложение **Arduino.app** в папку `/Applications`.
    * **Ubuntu**. Распакуйте его в папку, например `$HOME/Downloads/arduino-1.8.8`.

2. Установка [Visual Studio Code](https://code.visualstudio.com/), перекрестный редактор исходного кода платформы с мощным интеллектом, завершением кода и поддержкой отладки, а также богатыми расширениями, которые могут быть установлены с рынка.

3. Запустите VS Code, найдите **Arduino** в расширениях Marketplace и установите его. Это расширение предоставляет улучшенные возможности для разработки на платформе Arduino.

    ![Установка Arduino](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-arduino.png)

4. Найдите [Средства Azure IoT](https://aka.ms/azure-iot-tools) в расширениях Marketplace и установите его.

    ![Установка средств Azure IoT](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-azure-iot-tools.png)

    Или используйте эту прямую ссылку:
    > [!div class="nextstepaction"]
    > [Установка пакета расширения Инструментов Azure IoT](vscode:extension/vsciot-vscode.azure-iot-tools)

    > [!NOTE]
    > Пакет расширения Azure IoT Tools содержит [Workbench приборов Azure IoT,](https://aka.ms/iot-workbench) который используется для разработки и отладки на различных устройствах IoT devkit. [Расширение концентратора Azure IoT,](https://aka.ms/iot-toolkit)также включенное в пакет расширений Azure IoT Tools, используется для управления и взаимодействия с концентраторами Azure IoT.

5. Настройте VS Code с параметрами Arduino.

    В Visual Studio Code щелкните **Файл > Настройки > настройки** (на macOS, **Code > Настройки > настройки).** Затем нажмите значок **«Открытые настройки» (JSON)** в правом верхнем углу *страницы «Настройки».*

    ![Установка средств Azure IoT](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/user-settings-arduino.png)

    Добавьте следующие строки для настройки Arduino в зависимости от используемой платформы: 

    * **Windows**:

        ```json
        "arduino.path": "C:\\Program Files (x86)\\Arduino",
        "arduino.additionalUrls": "https://raw.githubusercontent.com/VSChina/azureiotdevkit_tools/master/package_azureboard_index.json"
        ```

    * **macOS**:

        ```json
        "arduino.path": "/Applications",
        "arduino.additionalUrls": "https://raw.githubusercontent.com/VSChina/azureiotdevkit_tools/master/package_azureboard_index.json"
        ```

    * **Увунту**:

        замените заполнитель **{username}**, приведенный ниже, своим именем пользователя.

        ```json
        "arduino.path": "/home/{username}/Downloads/arduino-1.8.8",
        "arduino.additionalUrls": "https://raw.githubusercontent.com/VSChina/azureiotdevkit_tools/master/package_azureboard_index.json"
        ```

6. Нажмите клавишу `F1`, чтобы открыть палитру команд, а затем введите и выберите **Arduino: Board Manager** (Диспетчер плат Arduino). Выполните поиск **AZ3166** и установите последнюю версию.

    ![Установка пакета SDK для DevKit](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-az3166-sdk.png)

### <a name="install-st-link-drivers"></a>Установка драйверов ST-Link

[ST-Link/V2](https://www.st.com/en/development-tools/st-link-v2.html) — это интерфейс USB, который IoT DevKit использует для взаимодействия с вашим компьютером разработки. Вам нужно установить его на Windows, чтобы флэш-код компилированного устройства на DevKit. Выполните действия для определенной ОС, чтобы разрешить компьютеру доступ к устройству.

* **Windows**. Скачайте и установите USB-драйвер с веб-сайта [STMicroelectronics](https://www.st.com/en/development-tools/stsw-link009.html).
* **macOS**. Для macOS не требуется драйвер.
* **Ubuntu**: Запуск команд в терминале и войти и войти в группу изменения, чтобы ввести силу:

    ```bash
    # Copy the default rules. This grants permission to the group 'plugdev'
    sudo cp ~/.arduino15/packages/AZ3166/tools/openocd/0.10.0/linux/contrib/60-openocd.rules /etc/udev/rules.d/
    sudo udevadm control --reload-rules

    # Add yourself to the group 'plugdev'
    # Logout and log back in for the group to take effect
    sudo usermod -a -G plugdev $(whoami)
    ```

Теперь подготовка и настройка среды разработки завершены. Давайте построим образец GetStarted, который вы только что запустили.

## <a name="build-your-first-project"></a>Создание первого проекта

### <a name="open-sample-code-from-sample-gallery"></a>Откройте пример кода из выборочной галереи

IoT DevKit содержит богатую галерею образцов, которые можно использовать для подключения DevKit к различным службам Azure.

1. Убедитесь, что плата IoT DevKit **не подключена** к компьютеру. Сначала запустите VS Code, а затем подключите плату DevKit к компьютеру.

1. Нажмите, `F1` чтобы открыть палитру команд, введите и выберите **Azure IoT устройство Workbench: Открытые примеры...**. Затем выберите **IoT DevKit** в качестве доски.

1. На странице с примерами IoT Workbench найдите **Get Started** (Приступить к работе) и щелкните **Open Sample** (Открыть пример). Затем выберите путь по умолчанию, чтобы загрузить пример кода.

    ![Открытие примера](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/open-sample.png)

### <a name="provision-azure-iot-hub-and-device"></a>Предоставление Azure IoT концентратор и устройство

Вместо того, чтобы предоставить концентратор Azure IoT и устройство с портала Azure, вы можете сделать это в коде VS, не выходя из среды разработки.

1. В новом открытом окне `F1` проекта нажмите, чтобы открыть палитру команд, введите и выберите **Устройство Azure IoT Workbench: Предоставление услуг Azure...**. Следуйте шаг за шагом руководство, чтобы закончить подготовку концентратора Azure IoT и создание устройства IoT концентратор.

    ![Команда обеспечения](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/provision.png)

    > [!NOTE]
    > Если вы еще не зарегистрировались в Azure. Следите за всплывающее уведомление для входе.

1. Выберите подписку, которую нужно использовать.

    ![Выберите подлодку](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-subscription.png)

1. Затем выберите или создайте новую [группу ресурсов.](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#terminology)

    ![Выбор группы ресурсов](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-resource-group.png)

1. В указанной группе ресурсов следуйте руководству по выбору или созданию нового концентратора Azure IoT.

    ![Выберите шаги Концентратора IoT](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-hub-provision.png)

    ![Выберите концентратор IoT](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-iot-hub.png)

    ![Выбранный концентратор IoT](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-hub-selected.png)

1. В окне вывода вы увидите концентратор Azure IoT.

    ![Концентратор IoT подготовлен](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-hub-provisioned.png)

1. Выберите или создайте новое устройство в подготовленном концентраторе Azure IoT.

    ![Выберите шаги IoT-устройства](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-device-provision.png)

    ![Выберите устройство IoT, подготовленное](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-iot-device.png)

1. Теперь у вас есть Azure IoT концентратор подготовлены и устройство, созданное в нем. Также строка соединения устройства будет сохранена в VS Code для настройки IoT DevKit позже.

    ![Положение сделано](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/provision-done.png)

### <a name="configure-and-compile-device-code"></a>Настройка и компиляция кода устройства

1. В нижней правой строке состояния проверьте, выбрана ли плата **MXCHIP AZ3166** и используется ли последовательный порт с **STMicroelectronics**.

    ![Выбор платы и модели COM](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-com.png)

1. Нажмите, `F1` чтобы открыть палитру команд, введите и выберите **Azure IoT Устройство Workbench: Настройка настройки устройства ...**, затем выберите **Config Device Connection String > Выберите IoT Hub Device Connection String.**

1. На DevKit удерживайте **кнопку A,** нажимайте и отпустите кнопку **сбросить,** а затем отпустите **кнопку A.** Ваш DevKit вводит режим конфигурации и сохраняет строку соединения.

    ![Строка подключения](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/connection-string.png)

1. Нажмите `F1` еще раз, введите и выберите **Azure IoT устройство Workbench: Загрузить код устройства**. Будет запущена компиляция, затем код будет отправлен на устройство DevKit.

    ![Отправка Arduino](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/arduino-upload.png)

Плата DevKit перезагрузится и начнет выполнение кода.

> [!NOTE]
> Если есть ошибки или прерывания, всегда можно восстановить данные, выполнив команду еще раз.

## <a name="test-the-project"></a>Тестирование проекта

### <a name="view-the-telemetry-sent-to-azure-iot-hub"></a>Просмотр данных телеметрии, отправленных в Центр Интернета вещей Azure

Щелкните значок вилки в строке состояния, чтобы открыть Serial Monitor:

![Serial Monitor](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/serial-monitor.png)

Пример приложения успешно выполняется, если отображаются следующие результаты.

* Serial Monitor отображает сообщение, отправленное в Центр Интернета вещей.
* Светодиодный индикатор на плате MXChip IoT DevKit мигает.

![Выходные данные Serial Monitor](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/result-serial-output.png)

### <a name="view-the-telemetry-received-by-azure-iot-hub"></a>Просмотр данных телеметрии, полученных Центром Интернета вещей Azure

Вы можете использовать [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) для мониторинга сообщений, отправленных из устройства в облако (D2C) в Центре Интернета вещей.

1. Войдите на [портал Azure](https://portal.azure.com/) и найдите созданный Центр Интернета вещей.

    ![Портал Azure](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/azure-iot-hub-portal.png)

1. В области **Политики общего доступа** выберите политику **iothubowner** и запишите строку подключения Центра Интернета вещей.

    ![Строка подключения Центра Интернета вещей](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/azure-portal-conn-string.png)

1. В VS Code `F1`нажмите, введите и выберите **Azure IoT Hub: Установите строку подключения Концентратора IoT.** Скопируйте строку подключения в это поле.

    ![Установка строки подключения Центра Интернета вещей](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/set-iothub-connection-string.png)

1. Расширьте панель **АЗЁР IOT HUB DEVICES** справа, нажмите на созданное вами устройство и выберите **встроенную конечную точку мониторинга start Monitoring.**

    ![Мониторинг сообщений D2C](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/monitor-d2c.png)

1. На панели **выходных данных** вы можете просмотреть сообщения D2C, поступающие в Центр Интернета вещей.

    ![Сообщения, отправляемые с устройства в облако](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/d2c-output.png)

## <a name="review-the-code"></a>Просмотр кода

Является `GetStarted.ino` основным файлом эскиза Arduino.

![Сообщения, отправляемые с устройства в облако](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/code.png)

Чтобы увидеть, как телеметрия устройства отправляется в `utility.cpp` концентратор Azure IoT, откройте файл в той же папке. Посмотреть [ссылку API,](https://microsoft.github.io/azure-iot-developer-kit/docs/apis/arduino-language-reference/) чтобы узнать, как использовать датчики и периферийные устройства на IoT DevKit.

Использованное `DevKitMQTTClient` средство является оберткой **iothub_client** из [SDK Microsoft Azure IoT и библиотек для C для](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client) взаимодействия с Azure IoT Hub.

## <a name="problems-and-feedback"></a>Проблемы и обратная связь

Если вы столкнулись с проблемами, вы можете найти решение в [часто задаваемых вопросах по IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) или обратиться к нам с помощью [Gitter](https://gitter.im/Microsoft/azure-iot-developer-kit). Вы также можете оставить нам свой отзыв на этой странице.

## <a name="next-steps"></a>Дальнейшие действия

Вы успешно подключили плату MXChip IoT DevKit к Центру Интернета вещей и отправили в него собранные данные датчика.

[!INCLUDE [iot-hub-get-started-az3166-next-steps](../../includes/iot-hub-get-started-az3166-next-steps.md)]
