---
title: 'IoT DevKit в облаке: подключение платы IoT DevKit AZ3166 к Центру Интернета вещей Azure | Документация Майкрософт'
description: В этом руководстве содержатся сведения о том, как настроить и подключить плату IoT DevKit AZ3166 к Центру Интернета вещей Azure и передавать с нее данные в облачную платформу Azure.
author: wesmc7777
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 03/21/2019
ms.author: wesmc
ms.openlocfilehash: 941455e39a32405097563b043046866aeb5c7964
ms.sourcegitcommit: 87bd7bf35c469f84d6ca6599ac3f5ea5545159c9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/22/2019
ms.locfileid: "58351938"
---
# <a name="connect-iot-devkit-az3166-to-azure-iot-hub"></a>Подключение платы IoT DevKit AZ3166 к Центру Интернета вещей Azure

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

Плату [MXChip IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/) можно использовать для разработки и прототипирования решений Интернета вещей, использующих службы Microsoft Azure. Он содержит совместимую с Arduino плату со множеством периферийных устройств и датчиков, пакет ПО для платы с открытым кодом и расширяющийся [каталог проектов](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/).

## <a name="what-you-do"></a>Что нужно сделать

Подключите DevKit к созданному центру Интернета вещей Azure. Теперь можно собирать данные о температуре и влажности с датчиков и отправлять эти данные в центр Интернета вещей.

У вас еще нет платы DevKit? Воспользуйтесь [симулятором платы DevKit](https://azure-samples.github.io/iot-devkit-web-simulator/) или [приобретите ее](https://aka.ms/iot-devkit-purchase).

## <a name="what-you-learn"></a>Что вы узнаете

* Как подключить плату IoT DevKit к точке беспроводного доступа и подготовить среду разработки.
* Как создать Центр Интернета вещей и зарегистрировать устройство для MXChip IoT DevKit.
* Как собирать данные датчиков, запустив пример приложения на MXChip IoT DevKit.
* Как отправить данные датчиков в Центр Интернета вещей.

## <a name="what-you-need"></a>Необходимые элементы

* Плата MXChip IoT DevKit с кабелем micro USB. Вы можете [получить ее прямо сейчас](https://aka.ms/iot-devkit-purchase).
* Компьютер под управлением Windows 10 или macOS 10.10 (или более поздней версии).
* Активная подписка Azure. Вы можете активировать [бесплатную 30-дневную пробную учетную запись Microsoft Azure](https://azureinfo.microsoft.com/us-freetrial.html).
  
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

## <a name="configure-wi-fi"></a>Настройка Wi-Fi

Для работы проектов Центра Интернета вещей требуется подключение к Интернету. Следуйте приведенным ниже инструкциям, чтобы настроить плату DevKit для подключения к сети Wi-Fi.

### <a name="enter-ap-mode"></a>Переключение в режим точки беспроводного доступа

Удерживая нажатой кнопку B, нажмите и отпустите кнопку Reset (Сброс), а затем отпустите кнопку B. Плата DevKit переключится в режим точки беспроводного доступа для настройки Wi-Fi. На снимке экрана ниже отображается идентификатор SSID платы DevKit и IP-адрес портала настройки.

![Кнопка Reset (Сброс), кнопка B и идентификатор SSID](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/wifi-ap.jpg)

![Задание режима точки беспроводного доступа](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/set-ap-mode.gif)

### <a name="connect-to-devkit-ap"></a>Подключение к точке беспроводного доступа DevKit

Теперь используйте другое устройство с модулем Wi-Fi (компьютер или мобильный телефон), чтобы подключиться к плате DevKit по идентификатору SSID (выделен на снимке экрана выше). Поле пароля оставьте пустым.

![Сведения о сети и кнопка "Подключить"](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/connect-ssid.png)

### <a name="configure-wi-fi-for-the-devkit"></a>Настройка Wi-Fi для платы DevKit

Откройте IP-адрес, отображенный на экране DevKit в браузере на компьютере или в мобильном телефоне, выберите сеть Wi-Fi для подключения платы DevKit, а затем введите пароль. Нажмите кнопку **Подключиться**.

![Поле с паролем и кнопка подключения](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/wifi-portal.png)

Через несколько секунд после успешного подключения плата DevKit перезагрузится. На экране появится имя Wi-Fi и IP-адрес.

![Имя Wi-Fi и IP-адрес](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/wifi-ip.jpg)

> [!NOTE]
> Для работы IoT DevKit потребуется сети 2,4 ГГц. Модуль Wi-Fi на IoT DevKit не совместим с сетью 5 ГГц. Дополнительные сведения см. в разделе [Ошибка разрешения Homebrew в macOS](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#wi-fi-configuration).

После настройки Wi-Fi учетные данные для этого подключения сохранятся на устройстве, даже если оно будет отсоединено. Например, если вы настроили плату DevKit для домашней сети Wi-Fi и затем принесли ее на работу, потребуется перенастроить режим точки беспроводного доступа (начиная с шага в разделе "Переключение в режим точки беспроводного доступа"), чтобы подключить плату DevKit к сети Wi-Fi в офисе.

## <a name="start-using-the-devkit"></a>Начало использования платы DevKit

Приложение по умолчанию, запущенное на плате DevKit, проверяет наличие последней версии встроенного ПО и отображает некоторые диагностические данные датчиков.

### <a name="upgrade-to-the-latest-firmware"></a>Обновление до последней версии встроенного ПО

> [!NOTE]
> Начиная с версии 1.1, DevKit поддерживает ST-SAFE в загрузчике. Если используется версия ниже 1.1, необходимо обновить встроенное ПО.

При необходимости обновить встроенное ПО на экране будут показаны текущая и последняя версии. Следуйте указаниям по [обновлению встроенного ПО](https://microsoft.github.io/azure-iot-developer-kit/docs/firmware-upgrading/).

![Отображение текущей и последней версии встроенного ПО](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/firmware.jpg)

> [!NOTE]
> Это действие выполняется один раз. Когда вы начнете разработку с помощью платы DevKit и передадите свое приложение, вместе с ним будет передана последняя версия встроенного ПО.

### <a name="test-various-sensors"></a>Тестирование различных датчиков

Нажмите кнопку B, чтобы проверить датчики. Нажимайте и отпускайте кнопку B, чтобы обойти по очереди все датчики.

![Отображение кнопки B и датчиков](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/sensors.jpg)

## <a name="prepare-the-development-environment"></a>Подготовка среды разработки

### <a name="install-azure-iot-tools"></a>Установка средств Azure IoT

Мы рекомендуем установить расширение [Средства Azure IoT](https://aka.ms/azure-iot-tools) для Visual Studio Code, чтобы выполнять разработку с использованием платы DevKit. Набор средств Azure IoT включает [Azure IoT Device Workbench](https://aka.ms/iot-workbench) для разработки и отладки различных устройств DevKit Интернета вещей и [Azure IoT Hub Toolkit](https://aka.ms/iot-toolkit) для управления Центром Интернета вещей Azure и взаимодействия с ним.

Вы можете посмотреть эти видеоролики [Channel 9](https://channel9.msdn.com/), чтобы получить общее представление о решаемых ими задачах.
* [Introduction to the new IoT Workbench extension for VS Code](https://channel9.msdn.com/Shows/Internet-of-Things-Show/IoT-Workbench-extension-for-VS-Code) (Новое расширение IoT Workbench для VS Code)
* [What's new in the IoT Toolkit extension for VS Code](https://channel9.msdn.com/Shows/Internet-of-Things-Show/Whats-new-in-the-IoT-Toolkit-extension-for-VS-Code) (Новые возможности расширения IoT Toolkit для VS Code)

Выполните инструкции ниже, чтобы подготовить среду разработки для DevKit.

1. Установите [интегрированную среду разработки Arduino](https://www.arduino.cc/en/Main/Software). Она предоставляет необходимую цепочку инструментов для компиляции и передачи кода Arduino.
    * **Windows**: используйте версию установщика Windows. Не следует устанавливать из App Store.
    * **MacOS**: перетащите извлеченное приложение **Arduino.app** в папку `/Applications`.
    * **Ubuntu**: распакуйте его в папку, например `$HOME/Downloads/arduino-1.8.8`.

2. Установите кроссплатформенный редактор исходного кода [Visual Studio Code](https://code.visualstudio.com/) с мощными средствами разработки, такими как отладка и завершение кода IntelliSense.

3. Запустите VS Code, найдите **Arduino** в расширениях Marketplace и установите его. Это расширение предоставляет улучшенные возможности для разработки на платформе Arduino.
    ![Установка Arduino](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-arduino.png)

4. Найдите **Средства Azure IoT** в расширениях Marketplace и установите его.
    ![Установка средств Azure IoT](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-azure-iot-tools.png)

    > [!div class="nextstepaction"]
    > [Установка пакета расширения средств Интернета вещей Azure](vscode:extension/vsciot-vscode.azure-iot-tools)

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
    
        замените заполнитель **{username}**, приведенный ниже, своим именем пользователя.

        ```json
        "arduino.path": "/home/{username}/Downloads/arduino-1.8.8",
        "arduino.additionalUrls": "https://raw.githubusercontent.com/VSChina/azureiotdevkit_tools/master/package_azureboard_index.json"
        ```

6. Щелкните `F1`, чтобы открыть палитру команд, а затем введите и выберите **Arduino: Board Manager** (Диспетчер плат Arduino). Выполните поиск **AZ3166** и установите последнюю версию.
    ![Установка пакета SDK для DevKit](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-az3166-sdk.png)

### <a name="install-st-link-drivers"></a>Установка драйверов ST-Link

[ST-Link/V2](https://www.st.com/en/development-tools/st-link-v2.html) — это интерфейс USB, который IoT DevKit использует для взаимодействия с вашим компьютером разработки. Необходимо установить на Windows начинает мигать, код скомпилированной устройства в devkit. Выполните действия для определенной ОС, чтобы разрешить компьютеру доступ к устройству.

* **Windows**: Скачайте и установите USB-драйвер от [веб-сайт STMicroelectronics](https://www.st.com/en/development-tools/stsw-link009.html) для [прямую ссылку](https://aka.ms/stlink-v2-windows).
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

Теперь подготовка и настройка среды разработки завершены. Давайте создадим образец приложения "Hello, World!" для Интернета вещей, которое отправляет данные телеметрии температуры в Центр Интернета вещей Azure.

## <a name="build-your-first-project"></a>Создание первого проекта

### <a name="open-sample-code-from-sample-gallery"></a>Откройте пример кода из коллекции примеров

1. Убедитесь, что плата IoT DevKit **не подключена** к компьютеру. Сначала запустите VS Code, а затем подключите плату DevKit к компьютеру.

1. Щелкните `F1`, чтобы открыть палитру команд, затем введите и выберите **Azure IoT Device Workbench. Открыть примеры...** Затем выберите **IoT DevKit** в качестве платы.

1. На странице с примерами IoT Workbench найдите **Get Started** (Приступить к работе) и щелкните **Open Sample** (Открыть пример). Затем выберите путь по умолчанию, чтобы загрузить пример кода.
    ![Открытие примера](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/open-sample.png)

### <a name="provision-azure-iot-hub-and-device"></a>Подготовка центра Интернета вещей и устройства

1. В открывшемся окне проекта щелкните `F1`, чтобы открыть палитру команд, введите и выберите **Azure IoT Device Workbench. Подготовка служб Azure...**. Выполните пошаговые инструкции для завершения подготовки Центра Интернета вещей Azure и создания устройства Центра Интернета вещей.
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

1. Щелкните `F1`, чтобы открыть палитру команд, затем введите и выберите **Azure IoT Device Workbench. Настройка параметров устройства...**, а затем последовательно выберите **Config Device Connection String (Настроить строку подключения к устройству) > Select IoT Hub Device Connection String (Выбрать строку подключения к устройству Центра Интернета вещей)**.

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
