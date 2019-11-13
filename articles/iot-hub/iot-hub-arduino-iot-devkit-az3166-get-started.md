---
title: Подключение центра Интернета вещей DevKit AZ3166 к центру Интернета вещей Azure
description: В этом руководстве содержатся сведения о том, как настроить и подключить плату IoT DevKit AZ3166 к Центру Интернета вещей Azure и передавать с нее данные в облачную платформу Azure.
author: wesmc7777
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 06/25/2019
ms.author: wesmc
ms.openlocfilehash: 3cdeecd5b7698274b899832d7a66a5572a114e4b
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/12/2019
ms.locfileid: "73954516"
---
# <a name="connect-iot-devkit-az3166-to-azure-iot-hub"></a>Подключение платы IoT DevKit AZ3166 к Центру Интернета вещей Azure

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

Плату [MXChip IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/) можно использовать для разработки и прототипирования решений Интернета вещей, использующих службы Microsoft Azure. Она включает совместимую с Arduino плату с богатыми периферийными устройствами и датчиками, пакетом платы с открытым кодом и обширной [коллекцией примеров](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/).

## <a name="what-you-learn"></a>Что вы узнаете

* Как создать Центр Интернета вещей и зарегистрировать устройство для MXChip IoT DevKit.
* Как подключить DevKit IoT к Wi-Fi и настроить строку подключения для центра Интернета вещей.
* Как отправить данные телеметрии датчика DevKit в центр Интернета вещей.
* Как подготовить среду разработки и разработать приложение для DevKit IoT.

У вас еще нет платы DevKit? Воспользуйтесь [симулятором платы DevKit](https://azure-samples.github.io/iot-devkit-web-simulator/) или [приобретите ее](https://aka.ms/iot-devkit-purchase).

Исходный код для всех руководств DevKit можно найти в [коллекции примеров кода](https://docs.microsoft.com/samples/browse/?term=mxchip).

## <a name="what-you-need"></a>Необходимые элементы

* Плата MXChip IoT DevKit с кабелем Micro-USB. Вы можете [получить ее прямо сейчас](https://aka.ms/iot-devkit-purchase).
* Компьютер под управлением Windows 10, macOS 10.10 + или Ubuntu 18.04 +.
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

## <a name="quickstart-send-telemetry-from-devkit-to-an-iot-hub"></a>Краткое руководство. Отправка данных телеметрии из DevKit в центр Интернета вещей

В кратком руководстве для отправки данных телеметрии в центр Интернета вещей используется предварительно скомпилированное встроенное по DevKit. Перед запуском вы создадите центр Интернета вещей и зарегистрируете устройство в концентраторе.

### <a name="create-an-iot-hub"></a>Создание центра IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

### <a name="register-a-device"></a>Регистрация устройства

Устройство должно быть зарегистрировано в Центре Интернета вещей, прежде чем оно сможет подключиться. В этом кратком руководстве для регистрации имитируемого устройства используется Azure Cloud Shell.

1. Выполните приведенные ниже команды в Azure Cloud Shell, чтобы создать удостоверение устройства.

   **YourIoTHubName.** Замените этот заполнитель именем вашего центра Интернета вещей.

   **Минодедевице**: имя регистрируемого устройства. Используйте **MyNodeDevice**, как показано ниже. Если вы выбрали другое имя для устройства, используйте его при работе с этим руководством и обновите имя устройства в примерах приложений перед их запуском.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyNodeDevice
    ```

   > [!NOTE]
   > Если вы получаете сообщение об ошибке с `device-identity`, установите [расширение Azure IOT для Azure CLI](https://github.com/Azure/azure-iot-cli-extension/blob/dev/README.md) , чтобы получить дополнительные сведения.
  
1. Выполните следующую команду в Azure Cloud Shell, чтобы получить _строку подключения_ зарегистрированного устройства:

   **YourIoTHubName.** Замените этот заполнитель именем вашего центра Интернета вещей.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name YourIoTHubName --device-id MyNodeDevice --output table
    ```

    Запишите строку подключения устройства, которая выглядит так:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyNodeDevice;SharedAccessKey={YourSharedAccessKey}`

    Это значение понадобится позже в рамках этого краткого руководства.

### <a name="send-devkit-telemetry"></a>Отправка данных телеметрии DevKit

DevKit подключается к конечной точке устройства в центре Интернета вещей и отправляет данные телеметрии температуры и влажности.

1. Скачайте последнюю версию DevKit [встроенного по](https://aka.ms/devkit/prod/getstarted/latest) для IOT.

1. Убедитесь, что IoT DevKit подключается к компьютеру через USB. Откройте проводник. это запоминающее устройство USB с именем **AZ3166**.

    ![Откройте проводник Windows.](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/az3166-usb.png)

1. Перетащите встроенное по, загруженное в устройство массового хранения, и автоматически Flash.

    ![Копировать встроенное по](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/copy-firmware.png)

1. На DevKit нажмите кнопку б, нажмите клавишу **b**и отпустите кнопку **Сброс** , а затем отпустите кнопку **b**. DevKit переходит в режим AP. Для подтверждения на экране отображается идентификатор набора служб (SSID) DevKit и IP-адрес портала конфигурации.

    ![Кнопка Reset (Сброс), кнопка B и идентификатор SSID](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/wifi-ap.jpg)

    ![Задание режима точки беспроводного доступа](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/set-ap-mode.gif)

1. Используйте веб-браузер на другом устройстве с поддержкой Wi-Fi (компьютер или мобильный телефон) для подключения к общедоступному идентификатору SSID IoT DevKit, показанному на предыдущем шаге. Если запрашивается пароль, оставьте его пустым.

    ![Подключение SSID](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/connect-ssid.png)

1. Откройте **192.168.0.1** в браузере. Выберите Wi-Fi, к которому нужно подключить IoT DevKit, введите пароль Wi-Fi, а затем вставьте строку подключения устройства, которая была сделана ранее. Нажмите кнопку "Сохранить".

    ![Пользовательский интерфейс настройки](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/configuration-ui.png)

    > [!NOTE]
    > DevKit IoT поддерживает только сеть с частотой 2,4 ГГц. Дополнительные сведения см. в разделе [Ошибка разрешения Homebrew в macOS](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#wi-fi-configuration).

1. Информация о Wi-Fi и строка подключения устройства будут сохранены в IoT DevKit при отображении страницы результатов.

    ![Результат конфигурации](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/configuration-ui-result.png)

    > [!NOTE]
    > После настройки Wi-Fi учетные данные для этого подключения сохранятся на устройстве, даже если оно будет отсоединено.

1. DevKit Интернета вещей перезагружается через несколько секунд. На экране DevKit вы увидите IP-адрес для DevKit, который соответствует данным телеметрии, включая значение температуры и влажности с числом сообщений, отправляемым в центр Интернета вещей Azure.

    ![IP-адрес WiFi](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/wifi-ip.jpg)

    ![Отправка данных](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/sending-data.jpg)

1. Чтобы проверить данные телеметрии, отправляемые в Azure, выполните следующую команду в Azure Cloud Shell:

    ```bash
    az iot hub monitor-events --hub-name YourIoTHubName --output table
    ```

## <a name="prepare-the-development-environment"></a>Подготовка среды разработки

Выполните следующие действия, чтобы подготовить среду разработки для DevKit.

### <a name="install-visual-studio-code-with-azure-iot-tools-extension-package"></a>Установка Visual Studio Code с помощью пакета расширений средств Azure IoT

1. Установите [интегрированную среду разработки Arduino](https://www.arduino.cc/en/Main/Software). Она предоставляет необходимую цепочку инструментов для компиляции и передачи кода Arduino.
    * **Windows**. Используйте версию установщика Windows. Не устанавливайте из App Store.
    * **macOS**. Перетащите и отпустите извлеченное приложение **Arduino.app** в папку `/Applications`.
    * **Ubuntu**. Распакуйте его в папку, например `$HOME/Downloads/arduino-1.8.8`.

2. Установка [Visual Studio Code](https://code.visualstudio.com/). редактор исходного кода для различных платформ с мощными функциями IntelliSense, поддержка завершения кода и отладки, а также расширенные расширения можно установить из Marketplace.

3. Запустите VS Code, найдите **Arduino** в расширениях Marketplace и установите его. Это расширение предоставляет улучшенные возможности для разработки на платформе Arduino.

    ![Установка Arduino](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-arduino.png)

4. Найдите [Средства Azure IoT](https://aka.ms/azure-iot-tools) в расширениях Marketplace и установите его.

    ![Установка средств Azure IoT](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-azure-iot-tools.png)

    Или используйте прямую ссылку:
    > [!div class="nextstepaction"]
    > [Установка пакета расширений для средств Azure IoT](vscode:extension/vsciot-vscode.azure-iot-tools)

    > [!NOTE]
    > Пакет расширений средств Azure IoT содержит средство [Azure IOT Device Workbench](https://aka.ms/iot-workbench) , которое используется для разработки и отладки на различных устройствах IOT DevKit. [Набор средств для центра Интернета вещей Azure](https://aka.ms/iot-toolkit), который также входит в состав пакета расширений средств Azure IOT, используется для управления центрами Интернета вещей Azure и взаимодействия с ними.

5. Настройте VS Code с параметрами Arduino.

    В Visual Studio Code щелкните **файл > настройки > параметры** (на MacOS, **Code > Settings (параметры) > параметров**). Затем щелкните значок " **открыть параметры (JSON)** " в правом верхнем углу страницы " *Параметры* ".

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

6. Нажмите клавишу `F1`, чтобы открыть палитру команд, а затем введите и выберите **Arduino: Board Manager** (Диспетчер плат Arduino). Выполните поиск **AZ3166** и установите последнюю версию.

    ![Установка пакета SDK для DevKit](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-az3166-sdk.png)

### <a name="install-st-link-drivers"></a>Установка драйверов ST-Link

[ST-Link/V2](https://www.st.com/en/development-tools/st-link-v2.html) — это интерфейс USB, который IoT DevKit использует для взаимодействия с вашим компьютером разработки. Его необходимо установить в Windows, чтобы скомпилировать скомпилированный код устройства в DevKit. Выполните действия для определенной ОС, чтобы разрешить компьютеру доступ к устройству.

* **Windows**. Скачайте и установите USB-драйвер с веб-сайта [STMicroelectronics](https://www.st.com/en/development-tools/stsw-link009.html).
* **macOS**. Для macOS не требуется драйвер.
* **Ubuntu**. выполните команды в окне терминала и выйдите из нее, чтобы изменения группы вступили в силу.

    ```bash
    # Copy the default rules. This grants permission to the group 'plugdev'
    sudo cp ~/.arduino15/packages/AZ3166/tools/openocd/0.10.0/linux/contrib/60-openocd.rules /etc/udev/rules.d/
    sudo udevadm control --reload-rules

    # Add yourself to the group 'plugdev'
    # Logout and log back in for the group to take effect
    sudo usermod -a -G plugdev $(whoami)
    ```

Теперь подготовка и настройка среды разработки завершены. Позвольте нам создать только что запущенный пример.

## <a name="build-your-first-project"></a>Создание первого проекта

### <a name="open-sample-code-from-sample-gallery"></a>Открыть пример кода из коллекции примеров

IoT DevKit содержит обширную галерею примеров, которые можно использовать для изучения подключения DevKit к различным службам Azure.

1. Убедитесь, что плата IoT DevKit **не подключена** к компьютеру. Сначала запустите VS Code, а затем подключите плату DevKit к компьютеру.

1. Щелкните `F1`, чтобы открыть палитру команд, введите и выберите **Azure IOT Device Workbench: Open examples...** . Затем выберите **IOT DevKit** в качестве доски.

1. На странице с примерами IoT Workbench найдите **Get Started** (Приступить к работе) и щелкните **Open Sample** (Открыть пример). Затем выберите путь по умолчанию, чтобы загрузить пример кода.

    ![Открыть пример](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/open-sample.png)

### <a name="provision-azure-iot-hub-and-device"></a>Инициализация центра Интернета вещей Azure и устройства

Вместо подготовки центра Интернета вещей Azure и устройства от портал Azure вы можете сделать это в VS Code без необходимости покинуть среду разработки.

1. В окне Новый открытый проект щелкните `F1`, чтобы открыть палитру команд, введите и выберите **Azure IOT Device Workbench: подготавливать службы Azure.** .. Выполните пошаговое руководством, чтобы завершить подготовку центра Интернета вещей Azure и создать устройство центра Интернета вещей.

    ![Команда инициализации](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/provision.png)

    > [!NOTE]
    > Если вы не вошли в Azure. Для входа выполните всплывающее уведомление.

1. Выберите подписку, которую нужно использовать.

    ![Выбор подраздела](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-subscription.png)

1. Затем выберите или создайте новую [группу ресурсов](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#terminology).

    ![Выбор группы ресурсов](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-resource-group.png)

1. В указанной группе ресурсов следуйте указаниям, чтобы выбрать или создать центр Интернета вещей Azure.

    ![Выбор действий центра Интернета вещей](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-hub-provision.png)

    ![Выбор центра Интернета вещей](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-iot-hub.png)

    ![Выбранный центр Интернета вещей](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-hub-selected.png)

1. В окне вывода вы увидите подготовленный центр Интернета вещей Azure.

    ![Центр Интернета вещей подготовлен](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-hub-provisioned.png)

1. Выберите или создайте новое устройство в подготовленном центре Интернета вещей Azure.

    ![Выбор действий для устройств IoT](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-device-provision.png)

    ![Выберите подготовленное устройство IoT](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-iot-device.png)

1. Теперь у вас есть подготовленный центр Интернета вещей Azure и созданное им устройство. Кроме того, строка подключения устройства будет сохранена в VS Code для настройки Интернета вещей DevKit позже.

    ![Инициализация выполнена](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/provision-done.png)

### <a name="configure-and-compile-device-code"></a>Настройка и компиляция кода устройства

1. В нижней правой строке состояния проверьте, выбрана ли плата **MXCHIP AZ3166** и используется ли последовательный порт с **STMicroelectronics**.

    ![Выбор доски и COM](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-com.png)

1. Щелкните `F1`, чтобы открыть палитру команд, введите и выберите **Azure IOT Device Workbench: Настройка параметров устройства...** , а затем выберите **Конфигурация строка подключения устройства > выберите строку подключения устройства центра Интернета вещей**.

1. В DevKit нажмите **кнопку a**, принудительно отпустите и Освободите кнопку **сброса** , а затем отпустите **кнопку A**. DevKit переходит в режим конфигурации и сохраняет строку подключения.

    ![Строка подключения](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/connection-string.png)

1. Щелкните `F1` еще раз, введите и выберите **Azure IOT Device Workbench: отправить код устройства**. Будет запущена компиляция, затем код будет отправлен на устройство DevKit.

    ![Отправка Arduino](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/arduino-upload.png)

Плата DevKit перезагрузится и начнет выполнение кода.

> [!NOTE]
> Если есть ошибки или прерывания, всегда можно восстановить данные, выполнив команду еще раз.

## <a name="test-the-project"></a>Тестирование проекта

### <a name="view-the-telemetry-sent-to-azure-iot-hub"></a>Просмотр данных телеметрии, отправленных в Центр Интернета вещей Azure

Щелкните значок вилки в строке состояния, чтобы открыть Serial Monitor:

![Последовательный монитор](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/serial-monitor.png)

Пример приложения успешно выполняется, если отображаются следующие результаты.

* Serial Monitor отображает сообщение, отправленное в Центр Интернета вещей.
* Светодиодный индикатор на плате MXChip IoT DevKit мигает.

![Выходные данные Serial Monitor](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/result-serial-output.png)

### <a name="view-the-telemetry-received-by-azure-iot-hub"></a>Просмотр данных телеметрии, полученных Центром Интернета вещей Azure

Вы можете использовать [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) для мониторинга сообщений, отправленных из устройства в облако (D2C) в Центре Интернета вещей.

1. Войдите на [портал Azure](https://portal.azure.com/) и найдите созданный Центр Интернета вещей.

    ![портале Azure](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/azure-iot-hub-portal.png)

1. В области **Политики общего доступа** выберите политику **iothubowner** и запишите строку подключения Центра Интернета вещей.

    ![Строка подключения для центра Интернета вещей Azure](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/azure-portal-conn-string.png)

1. В VS Code щелкните `F1`, введите и выберите **центр Интернета вещей Azure: задать строку подключения центра Интернета вещей**. Скопируйте строку подключения в это поле.

    ![Настройка строки подключения для центра Интернета вещей Azure](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/set-iothub-connection-string.png)

1. Раскройте панель **устройства центра Интернета вещей Azure** справа, щелкните имя созданного устройства правой кнопкой мыши и выберите команду **начать мониторинг встроенной конечной точки события**.

    ![Мониторинг сообщения D2C](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/monitor-d2c.png)

1. На панели **выходных данных** вы можете просмотреть сообщения D2C, поступающие в Центр Интернета вещей.

    ![Сообщение D2C](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/d2c-output.png)

## <a name="review-the-code"></a>Просмотр кода

`GetStarted.ino` является основным файлом эскиза Arduino.

![Сообщение D2C](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/code.png)

Чтобы увидеть, как телеметрические данные устройства отправляются в центр Интернета вещей Azure, откройте файл `utility.cpp` в той же папке. Ознакомьтесь со [справочником по API](https://microsoft.github.io/azure-iot-developer-kit/docs/apis/arduino-language-reference/) , чтобы узнать, как использовать датчики и периферийные устройства в IOT DevKit.

`DevKitMQTTClient` используется как оболочка **iothub_client** из [Microsoft Azure SDK и библиотек IOT для C](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client) для взаимодействия с центром Интернета вещей Azure.

## <a name="problems-and-feedback"></a>Проблемы и обратная связь

Если вы столкнулись с проблемами, вы можете найти решение в [часто задаваемых вопросах по IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) или обратиться к нам с помощью [Gitter](https://gitter.im/Microsoft/azure-iot-developer-kit). Вы также можете оставить нам свой отзыв на этой странице.

## <a name="next-steps"></a>Дополнительная информация

Вы успешно подключили плату MXChip IoT DevKit к Центру Интернета вещей и отправили в него собранные данные датчика.

[!INCLUDE [iot-hub-get-started-az3166-next-steps](../../includes/iot-hub-get-started-az3166-next-steps.md)]
