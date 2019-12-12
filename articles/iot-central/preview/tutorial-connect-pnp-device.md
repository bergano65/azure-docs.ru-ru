---
title: Учебник. Подключение устройства IoT Plug and Play (предварительная версия) к Azure IoT Central
description: В этом учебнике показано, как использовать модель возможностей устройства для создания кода устройства. Затем запустите код устройства, убедитесь, что устройство подключено к приложению IoT Central, и используйте автоматически сформированные представления.
author: dominicbetts
ms.author: dobett
ms.date: 12/09/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: 2cb07a94b2ae85cc0755e1e7069a76e1ef2a5252
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/10/2019
ms.locfileid: "74977341"
---
# <a name="tutorial-use-a-device-capability-model-to-create-an-iot-plug-and-play-device-and-connect-it-to-your-iot-central-application"></a>Руководство по Использование модели возможностей устройства для создания устройства IoT Plug and Play и его подключения к приложению IoT Central

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

_Модель возможностей устройства_ (DCM) описывает возможности устройства [IoT Plug and Play](../../iot-pnp/overview-iot-plug-and-play.md). IoT Central может использовать DCM для создания шаблона и визуализаций для устройства при его первом подключении.

Из этого руководства вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * Создать устройство IoT Plug and Play с использованием DCM в Visual Studio Code.
> * Запустить код устройства в Windows и убедиться, что оно подключено к приложению IoT Central.
> * Просмотреть смоделированную телеметрию, которую отправляет устройство.

## <a name="prerequisites"></a>Предварительные требования

Выполните инструкции из краткого руководства [Create an Azure IoT Central application (preview features)](./quick-deploy-iot-central.md) (Создание приложения Azure IoT Central (предварительные версии функций)), чтобы создать приложение IoT Central с использованием шаблона **Пользовательское приложение > предварительная версия приложения**.

Для выполнения инструкций, указанных в этом учебнике, вам необходимо установить на локальный компьютер следующее программное обеспечение:

* [Средства сборки для Visual Studio](https://visualstudio.microsoft.com/thank-you-downloading-visual-studio/?sku=BuildTools&rel=16) с рабочими нагрузками **средств сборки C++** и **компонентов диспетчера пакетов NuGet**. Допускается также [Visual Studio 2019, Visual Studio 2017 или Visual Studio 2015 (выпуски Community, Professional или Enterprise)](https://visualstudio.microsoft.com/downloads/) с теми же рабочими нагрузками.
* [Git](https://git-scm.com/download/).
* [CMake](https://cmake.org/download/) — при установке **CMake** выберите параметр **Add CMake to the system PATH** (Добавить CMake в системный путь).
* [Visual Studio Code](https://code.visualstudio.com/).
* [Node.js](https://nodejs.org/)
* Служебная программа `dps-keygen`:

    ```cmd/sh
    npm i -g dps-keygen
    ```

### <a name="install-azure-iot-tools"></a>Установка средств Azure IoT

Чтобы установить пакет расширения Azure IoT Tools в VS Code, выполните следующие действия:

1. В VS Code выберите вкладку **Расширения**.
1. Выполните поиск **Azure IoT Tools**.
1. Щелкните **Установить**.

## <a name="prepare-the-development-environment"></a>Подготовка среды разработки

В этом учебнике используется диспетчер библиотек [Vcpkg](https://github.com/microsoft/vcpkg) для установки пакета SDK для устройств Azure IoT для C в среде разработки.

1. Откройте окно командной строки. Выполните указанную ниже команду, чтобы установить Vcpkg.

    ```cmd
    git clone https://github.com/Microsoft/vcpkg.git
    cd vcpkg

    .\bootstrap-vcpkg.bat
    ```

    Затем, чтобы подключить [интеграцию](https://github.com/microsoft/vcpkg/blob/master/docs/users/integration.md) на уровне пользователя, выполните следующую команду. При первом запуске этой команды требуются привилегии администратора.

    ```cmd
    .\vcpkg.exe integrate install
    ```

1. Установите Vcpkg для пакета SDK для устройств Azure IoT для C.

    ```cmd
    .\vcpkg.exe install azure-iot-sdk-c[public-preview,use_prov_client]
    ```

## <a name="generate-device-key"></a>Создание ключа устройства

Чтобы подключить устройство к приложению IoT Central, вам необходим ключ устройства. Чтобы создать ключ устройства, выполните следующие действия.

1. Войдите в созданное приложение IoT Central с помощью шаблона **Пользовательское приложение > Предварительная версия приложения** в кратком руководстве [Create an Azure IoT Central application (preview features)](./quick-deploy-iot-central.md) (Создание приложения Azure IoT Central (предварительные версии функций)).

1. Перейдите на страницу **​​Администрирование** и выберите **Подключение устройства**.

1. Возьмите на заметку **область идентификатора** и **первичный ключ**, отображаемые при выборе **Просмотреть ключи**. Они понадобятся позже.

    ![подключение устройств.](./media/tutorial-connect-pnp-device/device-connection.png)

1. Откройте командную строку и выполните приведенную ниже команду, чтобы создать ключ устройства.

    ```cmd/sh
    dps-keygen -di:mxchip-001 -mk:{Primary Key from previous step}
    ```

    Запишите созданный _ключ устройства_. Он понадобится на более позднем этапе в этом учебнике.

## <a name="download-your-model"></a>Скачивание модели

В этом учебнике вы используете общедоступную модель DCM для устройства MxChip IoT DevKit. Вам не нужно фактическое устройство DevKit для запуска кода. В этом учебнике вы скомпилируете код для запуска в Windows.

1. Создайте папку с именем `central_app` и откройте ее в VS Code.

1. Используйте сочетание клавиш **CTRL+SHIFT+P**, чтобы открыть палитру команд, введите **IOT Plug and Play** и выберите **Open Model Repository** (Открыть репозиторий моделей). Выберите **Общедоступный репозиторий**. VS Code выведет список моделей DCM в общедоступном репозитории моделей.

1. Выберите модель DCM **MXChip IoT DevKit** с идентификатором `urn:mxchip:mxchip_iot_devkit:1`. Выберите **Скачать**. Теперь у вас есть копия DCM в папке `central_app`.

![Репозиторий моделей и DCM](./media/tutorial-connect-pnp-device/public-repository.png)

> [!NOTE]
> Для работы с IoT Central интерфейсы модели возможностей устройства должны быть указаны в одном файле.

## <a name="generate-the-c-code-stub"></a>Создание заглушки кода C

Теперь у вас есть модель DCM **MXChip IoT DevKit** и связанные с ней интерфейсы, и вы можете сгенерировать код устройства, который реализует модель. Чтобы создать заглушку кода C в VS Code:

1. В открытой папке с файлами DCM нажмите сочетание клавиш **CTRL+SHIFT+P**, чтобы открыть палитру команд, введите **IoT Plug and Play** и выберите **Generate Device Code Stub** (Создать заглушку кода устройства).

    > [!NOTE]
    > При первом использовании служебной программы IoT Plug and Play Code Generator загрузка занимает несколько секунд.

1. Выберите файл DCM **MXChip IoT DevKit**, который вы только что скачали.

1. Введите имя проекта **devkit_device**.

1. Выберите **ANSI C** в качестве языка.

1. В качестве метода подключения выберите **Via DPS (Device Provisioning Service) symmetric key** (С помощью симметричного ключа DPS (Служба подготовки устройств)).

1. Выберите **CMake Project on Windows** (Проект CMake в Windows) в качестве типа проекта. Не выбирайте **MXChip IoT DevKit Project** (Проект MXChip IoT DevKit). Этот вариант подходит для тех случаев, когда у вас есть настоящее устройство DevKit.

1. Выберите **Via Vcpkg** (С помощью Vcpkg) в качестве способа добавления пакета SDK.

1. В VS Code откроется новое окно, содержащее созданные файлы заглушки кода устройства в папке `devkit_device`.

![Созданный код устройства](./media/tutorial-connect-pnp-device/generated-code.png)

## <a name="build-the-code"></a>Сборка кода

Вы используете пакет SDK устройства для сборки созданной заглушки кода устройства. Созданное вами приложение имитирует устройство **MXChip IoT DevKit** и подключается к вашему приложению IoT Central. Оно отправляет данные телеметрии и свойства, а также получает команды.

1. В командной строке создайте подкаталог `cmake` в папке `devkit_device` и перейдите в эту папку.

    ```cmd
    mkdir cmake
    cd cmake
    ```

1. Выполните приведенные ниже команды, чтобы собрать сгенерированную заглушку кода: Замените заполнитель `<directory of your Vcpkg repo>` путем к расположению копии репозитория **Vcpkg9**.

    ```cmd
    cmake .. -G "Visual Studio 16 2019" -A Win32 -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="<directory of your Vcpkg repo>\scripts\buildsystems\vcpkg.cmake"

    cmake --build . -- /p:Configuration=Release
    ```

    При использовании Visual Studio 2017 или Visual Studio 2015 необходимо указать генератор CMake в соответствии с используемыми средствами сборки.

    ```cmd
    # Either
    cmake .. -G "Visual Studio 15 2017" -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="<directory of your Vcpkg repo>\scripts\buildsystems\vcpkg.cmake"
    # or
    cmake .. -G "Visual Studio 14 2015" -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="<directory of your Vcpkg repo>\scripts\buildsystems\vcpkg.cmake"
    ```

1. После успешного завершения сборки запустите приложение в той же командной строке. Замените значения `<scopeid>` и `<devicekey>` значениями, которые вы записали ранее.

    ```cmd
    .\Release\devkit_device.exe mxchip-001 <scopeid> <devicekey>
    ```

1. Приложение устройства начнет отправку данных в Центр Интернета вещей. Иногда при первом запуске предыдущей команды отображается ошибка `Error registering device for DPS`. В таком случае необходимо повторно выполнить команду.

## <a name="view-the-device"></a>Просмотр устройства

После того как код вашего устройства подключится к IoT Central, вы сможете просмотреть свойства и телеметрию, отправляемые устройством:

1. В своем приложении IoT Central перейдите на страницу **​​Устройства** и выберите устройство **mxchip-01**. Это устройство было автоматически добавлено при подключении кода устройства:

    ![Страница "Обзор"](./media/tutorial-connect-pnp-device/overview-page.png)

    Через пару минут на этой странице отобразятся графики телеметрии, отправляемой устройством.

1. Выберите страницу **Сведения**, чтобы просмотреть значения свойств, отправляемых устройством.

1. Выберите страницу **​​Команды** для вызова команд на устройстве. Вы можете увидеть ответ устройства в командной строке, в которой выполняется код устройства.

1. Перейдите на страницу **​​Шаблоны устройств**, чтобы просмотреть шаблон, созданный IoT Central на основе модели DCM, хранящейся в общедоступном репозитории:

    ![Страница шаблонов устройств](./media/tutorial-connect-pnp-device/device-template.png)

## <a name="next-steps"></a>Дополнительная информация

Из этого учебника вы узнали, как подключить устройство IoT Plug and Play, созданное на основе модели DCM, хранящейся в общедоступном репозитории.

Чтобы узнать больше о DCM и о том, как создавать свои собственные модели, перейдите к следующему руководству:

> [!div class="nextstepaction"]
> [Создание группы устройств](./tutorial-use-device-groups.md)
