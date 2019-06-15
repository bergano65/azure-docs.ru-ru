---
title: 'IoT DevKit в облаке: подключение платы IoT DevKit AZ3166 к Центру Интернета вещей Azure | Документация Майкрософт'
description: В этом руководстве содержатся сведения о том, как настроить и подключить плату IoT DevKit AZ3166 к Центру Интернета вещей Azure и передавать с нее данные в облачную платформу Azure.
author: wesmc7777
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 04/17/2019
ms.author: wesmc
ms.openlocfilehash: 2f86b74299b5d47a87ed0b8e89a992f0f91a84be
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "64924634"
---
# <a name="connect-iot-devkit-az3166-to-azure-iot-hub"></a>Подключение платы IoT DevKit AZ3166 к Центру Интернета вещей Azure

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

Плату [MXChip IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/) можно использовать для разработки и прототипирования решений Интернета вещей, использующих службы Microsoft Azure. Он содержит совместимую с Arduino плату со множеством периферийных устройств и датчиков, пакет платы с открытым исходным кодом и форматированного текста [коллекции примеров](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/).

## <a name="what-you-learn"></a>Что вы узнаете

* Как создать Центр Интернета вещей и зарегистрировать устройство для MXChip IoT DevKit.
* Способы подключения IoT DevKit по Wi-Fi и настроить строку подключения центра Интернета вещей.
* Как отправлять данные телеметрии датчиков DevKit к центру Интернета вещей.
* Как подготовить среду разработки и разработки приложений для IoT DevKit.

У вас еще нет платы DevKit? Воспользуйтесь [симулятором платы DevKit](https://azure-samples.github.io/iot-devkit-web-simulator/) или [приобретите ее](https://aka.ms/iot-devkit-purchase).

## <a name="what-you-need"></a>Необходимые элементы

* Плата MXChip IoT devkit с кабелем Micro USB. Вы можете [получить ее прямо сейчас](https://aka.ms/iot-devkit-purchase).
* Компьютер под управлением Windows 10, macOS 10.10 или Ubuntu 18.04 +.
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

## <a name="quickstart-send-telemetry-from-devkit-to-an-iot-hub"></a>Краткое руководство. Отправка данных телеметрии с DevKit к центру Интернета вещей

Использовать предварительно скомпилированные встроенного по DevKit, чтобы отправлять данные телеметрии в центр Интернета вещей. Перед запуском его, вы создание центра Интернета вещей и регистрация устройства в центре.

### <a name="create-an-iot-hub"></a>Создание Центра Интернета вещей

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

### <a name="register-a-device"></a>Регистрация устройства

Устройство должно быть зарегистрировано в Центре Интернета вещей, прежде чем оно сможет подключиться. В этом кратком руководстве для регистрации имитируемого устройства используется Azure Cloud Shell.

1. Выполните приведенные ниже команды в Azure Cloud Shell, чтобы создать удостоверение устройства.

   **YourIoTHubName**. Замените этот заполнитель именем вашего Центра Интернета вещей.

   **MyNodeDevice**. Имя регистрируемого устройства. Используйте **MyNodeDevice**, как показано ниже. Если вы выбрали другое имя для устройства, используйте его при работе с этим руководством и обновите имя устройства в примерах приложений перед их запуском.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyNodeDevice
    ```
   > [!NOTE]
   > Если отобразится сообщение об ошибке выполнения `device-identity`, установить [расширение Интернета ВЕЩЕЙ Azure для Azure CLI](https://github.com/Azure/azure-iot-cli-extension/blob/dev/README.md) для получения дополнительных сведений.
  
1. Выполните следующую команду в Azure Cloud Shell, чтобы получить _строку подключения_ зарегистрированного устройства:

   **YourIoTHubName**. Замените этот заполнитель именем вашего центра Интернета вещей.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name YourIoTHubName --device-id MyNodeDevice --output table
    ```

    Запишите строку подключения устройства, которая выглядит так:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyNodeDevice;SharedAccessKey={YourSharedAccessKey}`

    Это значение понадобится позже в рамках этого краткого руководства.

### <a name="send-devkit-telemetry"></a>Отправка данных телеметрии DevKit

DevKit подключается к конечной точке конкретного устройства в центре Интернета вещей и отправляет данные телеметрии температуры и влажности.

1. Скачайте последнюю версию [встроенного по GetStarted](https://aka.ms/devkit/prod/getstarted/latest) для IoT DevKit.

1. Убедитесь, что IoT DevKit подключиться к компьютеру через USB. Откройте проводник, имеется запоминающего устройства USB вызывается **AZ3166**.
    ![Откройте Windows Explorer](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/az3166-usb.png)

1. Операции перетаскивания встроенного по, загруженному в запоминающего устройства и он будет автоматически мигать.
    ![Скопируйте встроенного по](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/copy-firmware.png)

1. В DevKit, удерживая нажатой кнопку **B**и отпустите **Сброс** , а затем отпустите кнопку **B**. DevKit переходит в режим точки беспроводного доступа. Чтобы подтвердить, на экране отображается идентификатор (беспроводной сети SSID) DevKit и IP-адрес портала настройки.
    ![Сброс кнопки, B и идентификатор SSID](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/wifi-ap.jpg)

    ![Задание режима точки беспроводного доступа](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/set-ap-mode.gif)

1. Использование веб-браузер на разных Wi-Fi с поддержкой устройства (компьютер или мобильный телефон) для подключения к SSID платы DevKit IoT, отображается на предыдущем шаге. Если мастер просит ввести пароль, оставьте пустым.
    ![Подключение SSID](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/connect-ssid.png)

1. Откройте **192.168.0.1** в браузере. Выберите Wi-Fi, который вы хотите подключить плату IoT DevKit, введите пароль Wi-Fi, а затем вставьте строку подключения устройства, внесенные внимание на некоторые ранее. Нажмите кнопку "Сохранить".
    ![Пользовательский Интерфейс для настройки](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/configuration-ui.png)

    > [!NOTE]
    > IoT DevKit поддерживает только сети с тактовой частотой 2,4 ГГц. Дополнительные сведения см. в разделе [Ошибка разрешения Homebrew в macOS](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#wi-fi-configuration).

1. Строку подключения Wi-Fi сведения и устройства будут храниться в IoT DevKit, обнаружив на страницу результатов.
    ![Результат конфигурации](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/configuration-ui-result.png)

    > [!NOTE]
    > После настройки Wi-Fi учетные данные для этого подключения сохранятся на устройстве, даже если оно будет отсоединено.

1. IoT DevKit перезагрузится через несколько секунд. На экране DevKit появится следующий IP-адрес для DevKit данные телеметрии, включая температуры и отправки значения влажности с количество сообщений в центр Интернета вещей Azure.
    ![WiFi IP](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/wifi-ip.jpg)

    ![Отправка данных](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/sending-data.jpg)

## <a name="prepare-the-development-environment"></a>Подготовка среды разработки

Выполните следующие действия, чтобы подготовить среду разработки для DevKit.

### <a name="install-visual-studio-code-with-azure-iot-tools-extension-package"></a>Установка Visual Studio Code с помощью пакета расширения средств Интернета вещей Azure

1. Установите [интегрированную среду разработки Arduino](https://www.arduino.cc/en/Main/Software). Она предоставляет необходимую цепочку инструментов для компиляции и передачи кода Arduino.
    * **Windows**: используйте версию установщика Windows. Не следует устанавливать из App Store.
    * **MacOS**: перетащите извлеченное приложение **Arduino.app** в папку `/Applications`.
    * **Ubuntu**: распакуйте его в папку, например `$HOME/Downloads/arduino-1.8.8`.

2. Установка [Visual Studio Code](https://code.visualstudio.com/), кроссплатформенный редактор исходного кода с мощной технологией intellisense, завершение кода и отладки, поддержки, а также разнообразных расширений можно установить из marketplace.

3. Запустите VS Code, найдите **Arduino** в расширениях Marketplace и установите его. Это расширение предоставляет улучшенные возможности для разработки на платформе Arduino.
    ![Установка Arduino](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-arduino.png)

4. Найдите [Средства Azure IoT](https://aka.ms/azure-iot-tools) в расширениях Marketplace и установите его.
    ![Установка средств Azure IoT](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-azure-iot-tools.png)

    Или воспользоваться этой прямой ссылкой:
    > [!div class="nextstepaction"]
    > [Установка пакета расширения средств Интернета вещей Azure](vscode:extension/vsciot-vscode.azure-iot-tools)

    > [!NOTE]
    > Пакет расширения средств Интернета вещей Azure содержит [Workbench устройства Интернета вещей Azure](https://aka.ms/iot-workbench) — используется для разработки и отладки на различных устройствах IoT devkit. [Набора средств для центра Интернета вещей Azure](https://aka.ms/iot-toolkit), также входит в состав пакета расширения средств Интернета вещей Azure, используется для управления и взаимодействия с центрами Интернета вещей Azure.

5. Настройте VS Code с параметрами Arduino.

    В Visual Studio Code щелкните **Файл > Предпочтения > Параметры**. Затем щелкните **...** и выберите команду для **открытия файла settings.json**.
    ![Установка средств Azure IoT](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/user-settings-arduino.png)
    
    Добавьте следующие строки для настройки Arduino в зависимости от используемой платформы: 

    * **Windows**:
      
        ```json
        "arduino.path": "C:\\Program Files (x86)\\Arduino",
        "arduino.additionalUrls": "https://raw.githubusercontent.com/VSChina/azureiotdevkit_tools/master/package_azureboard_index.json"
        ```

    * **MacOS**:

        ```json
        "arduino.path": "/Applications",
        "arduino.additionalUrls": "https://raw.githubusercontent.com/VSChina/azureiotdevkit_tools/master/package_azureboard_index.json"
        ```

    * **Ubuntu**:
    
        замените заполнитель **{username}** , приведенный ниже, своим именем пользователя.

        ```json
        "arduino.path": "/home/{username}/Downloads/arduino-1.8.8",
        "arduino.additionalUrls": "https://raw.githubusercontent.com/VSChina/azureiotdevkit_tools/master/package_azureboard_index.json"
        ```

6. Щелкните `F1`, чтобы открыть палитру команд, а затем введите и выберите **Arduino: Board Manager** (Диспетчер плат Arduino). Выполните поиск **AZ3166** и установите последнюю версию.
    ![Установка пакета SDK для DevKit](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-az3166-sdk.png)

### <a name="install-st-link-drivers"></a>Установка драйверов ST-Link

[ST-Link/V2](https://www.st.com/en/development-tools/st-link-v2.html) — это интерфейс USB, который IoT DevKit использует для взаимодействия с вашим компьютером разработки. Необходимо установить на Windows начинает мигать, код скомпилированной устройства в devkit. Выполните действия для определенной ОС, чтобы разрешить компьютеру доступ к устройству.

* **Windows**: Скачайте и установите USB-драйвер от [веб-сайт STMicroelectronics](https://www.st.com/en/development-tools/stsw-link009.html) или [прямую ссылку](https://aka.ms/stlink-v2-windows).
* **MacOS**: для macOS не требуется драйвер.
* **Ubuntu**: Выполните команды в окне терминала и выйдите из системы и входить группу изменения вступили в силу:
    ```bash
    # Copy the default rules. This grants permission to the group 'plugdev'
    sudo cp ~/.arduino15/packages/AZ3166/tools/openocd/0.10.0/linux/contrib/60-openocd.rules /etc/udev/rules.d/
    sudo udevadm control --reload-rules

    # Add yourself to the group 'plugdev'
    # Logout and log back in for the group to take effect
    sudo usermod -a -G plugdev $(whoami)
    ```

Теперь подготовка и настройка среды разработки завершены. Сообщите нам создавайте GetStarted пример, который вы только что выполнили.

## <a name="build-your-first-project"></a>Создание первого проекта

### <a name="open-sample-code-from-sample-gallery"></a>Откройте пример кода из коллекции примеров

IoT DevKit содержит обширной коллекции примеров, которые можно использовать для получения подключить плату DevKit к различным службам Azure.

1. Убедитесь, что плата IoT DevKit **не подключена** к компьютеру. Сначала запустите VS Code, а затем подключите плату DevKit к компьютеру.

1. Щелкните `F1`, чтобы открыть палитру команд, затем введите и выберите **Azure IoT Device Workbench. Открыть примеры...** Затем выберите **IoT DevKit** в качестве платы.

1. На странице с примерами IoT Workbench найдите **Get Started** (Приступить к работе) и щелкните **Open Sample** (Открыть пример). Затем выберите путь по умолчанию, чтобы загрузить пример кода.
    ![Открытие примера](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/open-sample.png)

### <a name="provision-azure-iot-hub-and-device"></a>Подготовка центра Интернета вещей и устройства

Вместо подготовки центра Интернета вещей и устройства на портале Azure это можно сделать в VS Code не выходя из среды разработки.

1. В открывшемся окне проекта щелкните `F1`, чтобы открыть палитру команд, введите и выберите **Azure IoT Device Workbench. Подготовка служб Azure...** . Выполните пошаговые инструкции для завершения подготовки Центра Интернета вещей Azure и создания устройства Центра Интернета вещей.
    ![Подготовка команд](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/provision.png)

    > [!NOTE]
    > Если вы не вошли в Azure. Выполните всплывающее уведомление для входа.

1. Выберите подписку, которую нужно использовать.
    ![Выберите sub](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-subscription.png)

1. Затем выберите или создайте новый [группы ресурсов](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#terminology).
    ![Выберите группу ресурсов](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-resource-group.png)

1. В группе ресурсов, которую вы указали воспользуйтесь руководством выберите или создайте новый центр Интернета вещей Azure.
    ![Выберите действия центра Интернета вещей](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-hub-provision.png)

    ![Выберите центр Интернета вещей](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-iot-hub.png)

    ![Выбранный центр Интернета вещей](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-hub-selected.png)

1. В окне вывода появится центра Интернета вещей подготовлено ![подготовки центра Интернета вещей](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-hub-provisioned.png)

1. Выберите или создайте новое устройство в центре Интернета вещей Azure, вы подготовили.
    ![Выберите действия устройств IoT](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-device-provision.png)

    ![Выберите устройства Интернета вещей, подготовленного](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-iot-device.png)

1. Теперь у вас есть центра Интернета вещей Azure подготовлена и устройства, созданные в нем. Строка подключения устройства будет сохраняться в VS Code для настройки IoT DevKit в более поздней версии.
    ![Подготовка выполнена](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/provision-done.png)

### <a name="configure-and-compile-device-code"></a>Настройка и компиляция кода устройства

1. В нижней правой строке состояния проверьте, выбрана ли плата **MXCHIP AZ3166** и используется ли последовательный порт с **STMicroelectronics**.
    ![Выбор платы и модели COM](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-com.png)

1. Щелкните `F1`, чтобы открыть палитру команд, затем введите и выберите **Azure IoT Device Workbench. Настройка параметров устройства...** , а затем последовательно выберите **Config Device Connection String (Настроить строку подключения к устройству) > Select IoT Hub Device Connection String (Выбрать строку подключения к устройству Центра Интернета вещей)** .

1. В DevKit удерживайте нажатой **кнопку A**, нажмите и отпустите кнопку **Reset** (Сброс), а затем отпустите **кнопку А**. DevKit перейдет в режим настройки и сохранит строку подключения.
    ![Строка подключения](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/connection-string.png)

1. Снова щелкните `F1`, введите и выберите **Azure IoT Device Workbench. Отправка кода устройства**. Будет запущена компиляция, затем код будет отправлен на устройство DevKit.
    ![Отправка Arduino](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/arduino-upload.png)

Плата DevKit перезагрузится и начнет выполнение кода.

> [!NOTE]
> Если есть ошибки или прерывания, всегда можно восстановить данные, выполнив команду еще раз.

## <a name="test-the-project"></a>Тестирование проекта

### <a name="view-the-telemetry-sent-to-azure-iot-hub"></a>Просмотр данных телеметрии, отправленных в Центр Интернета вещей Azure

Щелкните значок вилки в строке состояния, чтобы открыть Serial Monitor: ![Serial Monitor](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/serial-monitor.png)

Пример приложения успешно выполняется, если отображаются следующие результаты.

* Serial Monitor отображает сообщение, отправленное в Центр Интернета вещей.
* Светодиодный индикатор на плате MXChip IoT DevKit мигает.

![Выходные данные Serial Monitor](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/result-serial-output.png)

### <a name="view-the-telemetry-received-by-azure-iot-hub"></a>Просмотр данных телеметрии, полученных Центром Интернета вещей Azure

Вы можете использовать [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) для мониторинга сообщений, отправленных из устройства в облако (D2C) в Центре Интернета вещей.

1. Войдите на [портал Azure](https://portal.azure.com/) и найдите созданный Центр Интернета вещей.
    ![портал Azure](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/azure-iot-hub-portal.png)

1. В области **Политики общего доступа** выберите политику **iothubowner** и запишите строку подключения Центра Интернета вещей.
    ![Строка подключения Центра Интернета вещей](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/azure-portal-conn-string.png)

1. В VS Code щелкните `F1`, введите и выберите **Центр Интернета вещей Azure. Установка строки подключения Центра Интернета вещей**. Скопируйте строку подключения в это поле.
    ![Установка строки подключения Центра Интернета вещей](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/set-iothub-connection-string.png)

1. Разверните панель **AZURE IOT HUB DEVICES** (Устройства центра Интернета вещей Azure) справа, щелкните имя созданного устройства правой кнопкой мыши и выберите **Start Monitoring D2C Message** (Начать мониторинг сообщений D2C).
    ![Мониторинг сообщений D2C](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/monitor-d2c.png)

1. На панели **выходных данных** вы можете просмотреть сообщения D2C, поступающие в Центр Интернета вещей.
    ![Сообщения, отправляемые с устройства в облако](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/d2c-output.png)

## <a name="review-the-code"></a>Просмотр кода

`GetStarted.ino` Является главным эскиза Arduino.

![Сообщения D2C](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/code.png)

Чтобы увидеть, способ отправки данных телеметрии устройства в центр Интернета вещей Azure, откройте `utility.cpp` файл в той же папке. Представление [Справочник по API](https://microsoft.github.io/azure-iot-developer-kit/docs/apis/arduino-language-reference/) чтобы узнать, как использовать датчики и периферийные устройства IoT DevKit.

`DevKitMQTTClient` Используется представляет собой оболочку **iothub_client** из [пакетов SDK Интернета вещей Microsoft Azure и библиотек для C](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client) для взаимодействия с центром Интернета вещей Azure.

## <a name="problems-and-feedback"></a>Проблемы и обратная связь

Если вы столкнулись с проблемами, вы можете найти решение в [часто задаваемых вопросах по IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) или обратиться к нам с помощью [Gitter](https://gitter.im/Microsoft/azure-iot-developer-kit). Вы также можете оставить нам свой отзыв на этой странице.

## <a name="next-steps"></a>Дальнейшие действия

Вы успешно подключили плату MXChip IoT DevKit к Центру Интернета вещей и отправили в него собранные данные датчика.

[!INCLUDE [iot-hub-get-started-az3166-next-steps](../../includes/iot-hub-get-started-az3166-next-steps.md)]
