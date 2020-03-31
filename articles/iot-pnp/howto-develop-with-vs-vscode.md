---
title: Используйте Visual Studio и визуальный код студии для создания IoT Plug и воспроизведения устройств предварительного просмотра (ru) Документы Майкрософт
description: Используйте Visual Studio и Visual Studio Code для ускорения авторизации моделей IoT Plug и Play устройств и реализации кода устройства.
author: liydu
ms.author: liydu
ms.date: 12/26/2019
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 91e7b1c0be9a38c3d79440f07d944d182980dc10
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80159240"
---
# <a name="use-visual-studio-and-visual-studio-code-to-build-iot-plug-and-play-devices"></a>Используйте Visual Studio и visual Studio Code для создания устройств IoT Plug и Play

Azure IoT Tools for Visual Studio Code обеспечивает интегрированную среду для авторских моделей возможностей устройств (DCM) и интерфейсов, публикует для моделирования репозитории и генерирует код скелета C для реализации приложения устройства.

В этой статье показано, как выполнить следующие действия:

- Создание кода устройства и проекта приложения.
- Используйте сгенерированный код в проекте устройства.
- Итерировать путем регенерации скелетного кода.

Подробнее об использовании VS-кода для разработки устройств IoT см. [https://github.com/microsoft/vscode-iot-workbench](https://github.com/microsoft/vscode-iot-workbench)

## <a name="prerequisites"></a>Предварительные требования

Установка [визуального кода студии](https://code.visualstudio.com/).

Используйте следующие шаги для установки пакета расширения в VS Code.

1. В VS Code выберите вкладку **Расширения.**
1. Поиск и установка **инструментов Azure IoT** с рынка.

## <a name="generate-device-code-and-project"></a>Создание кода и проекта устройства

В VS Code используйте **Ctrl-Shift-P,** чтобы открыть командную палитру, ввести **IoT Plug and Play**и выбрать **Generate Device Code Stub** для настройки кода скелета и типа проекта. Следующий список подробно описывает каждый шаг:

- **DCM файл, который будет использоваться для генерации кода.** Чтобы создать код устройства скелета, откройте папку, которая содержит DCM и интерфейсные файлы, которые она реализует. Если генератор кода не может найти интерфейс, который требуется DCM, он загружает его из репозитория модели по мере необходимости.

- **Язык кода устройства**. В настоящее время генератор кода поддерживает только ANSI C.

- **Название проекта**. Название проекта используется в качестве имени папки для генерируемого кода и других файлов проекта. Папка по умолчанию размещается рядом с файлом DCM. Чтобы избежать необходимости вручную копировать генерируемую папку кода при обновлении DCM и регенерации кода устройства, держите файл DCM в той же папке, что и папка проекта.

- **Метод подключения к Azure IoT**. Генерируемые файлы также содержат код для настройки устройства для подключения к концентратору Azure IoT. Вы можете подключиться непосредственно к [концентратору IoT Azure](https://docs.microsoft.com/azure/iot-hub) или воспользоваться [службой обеспечения устройств.](https://docs.microsoft.com/azure/iot-dps)

    - **Через строку подключения устройства IoT Hub:** укажите строку соединения устройства для приложения устройства для непосредственного подключения к Концентратору IoT.
    - **Через симметричный ключ DPS:** укажите **область ID Scope,** **симметричный ключ** и **идентификатор устройства** для приложения устройства, которые необходимы для подключения к IoT Hub или IoT Central с помощью DPS.

- **Тип проекта**. Генератор кода также генерирует проект CMake или Arduino. В настоящее время поддерживаемые типы проектов:

    - **Проект CMake на Windows:** для проекта устройства, который использует [CMake](https://cmake.org/) в качестве системы сборки на Windows. Эта опция `CMakeLists.txt` генерирует сятвия с конфигурациями SDK устройства в той же папке, что и c-код.
    - **Проект CMake на Linux**: для проекта устройства, который использует [CMake](https://cmake.org/) в качестве системы сборки на Linux. Эта опция `CMakeLists.txt` генерирует сятвия с конфигурациями SDK устройства в той же папке, что и c-код.
    - **Проект MXChip IoT DevKit**: для проекта устройства, работающее на устройстве [MXChip IoT DevKit.](https://aka.ms/iot-devkit) Эта опция создает проект Arduino, который можно [использовать в VS Code](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started) или в Arduino IDE для создания и запуска на устройстве IoT DevKit.

- **Устройство типа SDK**. Если вы выберете CMake в качестве типа проекта, это шаг для настройки того, `CMakeLists.txt`как генерируемый код будет включать устройство SDK Azure IoT C в:

    - **Через исходный код**: сгенерированный код опирается на [исходный код устройства SDK](https://github.com/Azure/azure-iot-sdk-c) для включения и сборки вместе с ним. Это рекомендуется, когда вы настроить исходный код устройства SDK.
    - **Через Vcpkg**: сгенерированный код опирается на [устройство SDK Vcpkg,](https://github.com/microsoft/vcpkg/tree/master/ports/azure-iot-sdk-c) чтобы включить и построить вместе с ним. Это рекомендуемый способ для устройств под управлением Windows, Linux или macOS.

    > [!NOTE]
    > Поддержка MacOS для устройства Azure IoT C SDK Vcpkg продолжается.

Генератор кода пытается использовать DCM и файлы интерфейса, расположенные в локальной папке. Если файлы интерфейса не в локальной папке, генератор кода ищет их в общедоступном репозитории модели или репозитории модели компании. [Общие файлы интерфейса](./concepts-common-interfaces.md) хранятся в общедоступном репозитории модели.

После завершения генерации кода расширение открывает новое окно VS Code с кодом. Если вы открываете генерируемый файл, такой как **main.c,** вы можете обнаружить, что IntelliSense сообщает, что он не может открыть исходные файлы C SDK. Для обеспечения правильной навигации IntelliSense и кода используйте следующие действия, чтобы включить источник C SDK:

1. В VS Code используйте **ctrl-Shift-P,** чтобы открыть командную палитру, введите и выберите **C/C: Конфигурации edit (JSON)** для открытия файла **c_cpp_properties.json.**

1. Добавьте путь устройства SDK `includePath` в разделе:

    ```json
    "includePath": [
        "${workspaceFolder}/**",
        "{path_of_device_c_sdk}/**"
    ]
    ```

1. Сохраните файл.

## <a name="use-the-generated-code"></a>Использование генерируемого кода

Ниже приведены инструкции, описываемые как использовать генерируемый код в собственном проекте устройства на различных платформах машин разработки. Инструкции предполагают, что вы используете строку подключения устройства IoT Hub с генерируемым кодом:

### <a name="linux"></a>Linux

Чтобы построить код устройства вместе с устройством C SDK Vcpkg с помощью CMake в среде Linux, такой как Ubuntu или Debian:

1. Откройте приложение терминала.

1. Установите **GCC,** `cmake` **Git**, и `apt-get` все зависимости с помощью команды:

    ```bash
    sudo apt-get update
    sudo apt-get install -y git cmake build-essential curl libcurl4-openssl-dev libssl-dev uuid-dev
    ```

    Убедитесь, что версия `cmake` выше **2.8.12**, а версия **GCC** выше **4.4.7**.

    ```bash
    cmake --version
    gcc --version
    ```

1. Установка Vcpkg:

    ```bash
    git clone https://github.com/Microsoft/vcpkg.git
    cd vcpkg

    ./bootstrap-vcpkg.sh
    ```

    Затем, чтобы подключить [пользовательской интеграции,](https://github.com/microsoft/vcpkg/blob/master/docs/users/integration.md)запустить:

    ```bash
    ./vcpkg integrate install
    ```

1. Установите Vcpkg для пакета SDK для устройств Azure IoT для C.

    ```bash
    ./vcpkg install azure-iot-sdk-c[public-preview,use_prov_client]
    ```

1. Создайте `cmake` субдиректорию в папке, содержащей заглушку сгенерированного кода, и перейдите к этой папке:

    ```bash
    mkdir cmake
    cd cmake
    ```

1. Запустите следующие команды для использования CMake для создания устройства SDK и сгенерированного кода:

    ```bash
    cmake .. -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="{directory of your Vcpkg repo}/scripts/buildsystems/vcpkg.cmake"

    cmake --build .
    ```

1. После успешной сборки запустите приложение, определяющее строку соединения устройства IoT Hub в качестве параметра.

    ```bash
    ./{generated_code_project_name} "[IoT Hub device connection string]"
    ```

### <a name="windows"></a>Windows

Чтобы построить код устройства вместе с устройством C SDK на Windows с помощью CMake и [IoT Plug and Play quickstart](./quickstart-create-pnp-device-windows.md)компиляторов Visual Studio C/C', на командной линии, см. Следующие шаги показывают, как построить код устройства вместе с устройством C SDK Vcpkg как проект CMake в Visual Studio.

1. Выполните последующие действия в [быстром запуске,](https://docs.microsoft.com/azure/iot-pnp/quickstart-create-pnp-device-windows#prepare-the-development-environment) чтобы установить устройство SDK Azure IoT для C через Vcpkg.

1. Установите [Visual Studio 2019 (Сообщество, Профессионал или Предприятие)](https://visualstudio.microsoft.com/downloads/) - убедитесь, что вы включаете компонент **менеджера пакетов NuGet** и **разработку настольных компьютеров с** рабочей нагрузкой.

1. Открыть Visual Studio, выберите файл > Открыть `CMakeLists.txt` > **CMake ...,** чтобы открыть в папке содержит сгенерированный код.

1. В **общей** панели инструментов найдите выпадение **конфигураций.** Выберите **конфигурацию управления,** чтобы добавить настройки CMake для вашего проекта.

    ![Управление конфигурацией](media/howto-develop-with-vs-vscode/vs-manage-config.png)

1. В **настройках CMake**добавьте новую конфигурацию и выберите **x86-Debug** в качестве мишени.

1. В **Аргументах командования CMake**добавьте следующую строку:

    ```txt
    -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON
    ```

1. Сохраните файл.

1. Переключитесь на **x86-Debug** в **выпадении конфигураций.** Для создания кэша для cMake требуется несколько секунд. Просмотр окна вывода, чтобы увидеть прогресс.

    ![Выход CMake](media/howto-develop-with-vs-vscode/vs-cmake-output.png)

1. В **Solution Explorer**нажмите правой кнопкой мыши на `CMakeLists.txt` корневой папке и выберите **Сборку** из контекстного меню для создания сгенерированного кода с помощью SDK устройства.

1. После успешной сборки в запросе команды запустите приложение, определяющее строку соединения устройства IoT hub в качестве параметра.

    ```cmd
    .\out\build\x86-Debug\{generated_code_project_name}.exe "[IoT Hub device connection string]"
    ```

> [!TIP]
> Чтобы узнать больше об использовании CMake в Visual Studio, смотрите [проект Build CMake](https://docs.microsoft.com/cpp/build/cmake-projects-in-visual-studio?view=vs-2019#building-cmake-projects) .

### <a name="macos"></a>macOS

Следующие шаги показывают, как построить код устройства вместе с исходным кодом устройства C SDK на macOS с помощью CMake:

1. Открытое приложение терминала.

1. Используйте [Homebrew](https://homebrew.sh) для установки всех зависимостей:

    ```bash
    brew update
    brew install git cmake pkgconfig openssl ossp-uuid
    ```

1. Убедитесь, что [CMake,](https://cmake.org/) по крайней мере, версия **2.8.12:**

    ```bash
    cmake --version
    ```

1. [Патч CURL](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#upgrade-curl-on-mac-os) к последней доступной версии.

1. В папке, содержащей сгенерированный код, клоните репозиторий [Azure IoT C SDK:](https://github.com/Azure/azure-iot-sdk-c)

    ```bash
    git clone https://github.com/Azure/azure-iot-sdk-c --recursive -b public-preview
    ```

    Выполнение этой операции может занять несколько минут.

1. Создайте папку, называемую `cmake` папкой, содержащую сгенерированный код, и перейдите к этой папке.

    ```bash
    mkdir cmake
    cd cmake
    ```

1. Запустите следующие команды для использования CMake для создания устройства SDK и сгенерированного кода:

    ```bash
    cmake -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DOPENSSL_ROOT_DIR:PATH=/usr/local/opt/openssl ..
    cmake --build .
    ```

1. После успешной сборки запустите приложение, определяющее строку соединения устройства IoT Hub в качестве параметра.

    ```bash
    cd {generated_code_folder_name}/cmake/
    ./{generated_code_project_name} "[IoT Hub device connection string]"
    ```

## <a name="iterate-by-regenerating-the-skeleton-code"></a>Итерировать путем регенерации кода скелета

Генератор кода может регенерировать код, если вы обновите DCM или интерфейс файлов. Предполагая, что вы уже сгенерировали код устройства из файла DCM, чтобы регенерировать код:

1. В открытой папке с файлами DCM нажмите сочетание клавиш **CTRL+SHIFT+P**, чтобы открыть палитру команд, введите **IoT Plug and Play** и выберите **Generate Device Code Stub** (Создать заглушку кода устройства).

1. Выберите обновленный файл DCM.

1. Выберите **Re-generate код для «имя проекта».**

1. Генератор кода использует предыдущую настройку и регенерирует код. Тем не менее, он не перезаписывают файлы, которые могут содержать пользовательский код, такие как `main.c` и `{project_name}_impl.c`.

> [!NOTE]
> Если обновить идентификатор URN в файле интерфейса, он будет рассматриваться как новый интерфейс. При повторном создании кода генератор кода генерирует код для интерфейса, но не перезаписывает исходный в файле. `{project_name}_impl.c`

## <a name="problems-and-feedback"></a>Проблемы и обратная связь

Azure IoT Tools — проект с открытым исходным кодом на GitHub. Для любых проблем и запросов функций можно [создать проблему на GitHub.](https://github.com/microsoft/vscode-azure-iot-tools/issues/new)

## <a name="next-steps"></a>Дальнейшие действия

В этой статье вы узнали, как использовать Visual Studio и Visual Studio Code для создания кода скелета C для реализации приложения устройства. Следующий шаг заключается в том, чтобы узнать, как установить и использовать инструмент [Explorer Azure IoT.](./howto-install-iot-explorer.md)
