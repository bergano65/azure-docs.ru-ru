---
title: Использование Workbench для устройств центра Интернета вещей Azure для создания предварительной версии устройств IoT Plug and Play | Документация Майкрософт
description: Используйте расширение Azure IoT Device Workbench в Visual Studio Code, чтобы ускорить создание моделей устройств IoT Plug and Play и реализацию кода устройства.
author: liydu
ms.author: liydu
ms.date: 07/25/2019
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: eaf1e313e3f88e151576ff00aec762a5fc2d8c66
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/21/2019
ms.locfileid: "69880492"
---
# <a name="use-azure-iot-device-workbench-extension-in-visual-studio-code"></a>Использование расширения Azure IoT Device Workbench в Visual Studio Code

Расширение Visual Studio Code Azure IoT Device Workbench предоставляет интегрированную среду для создания моделей возможностей устройств (DCM) и интерфейсов, публикации в репозиториях модели и создания каркаса кода на языке C для реализации приложения устройства.

В этой статье описаны следующие процессы.

- Создание кода устройства и проекта приложения.
- Используйте созданный код в проекте устройства.
- Выполните итерацию путем повторного создания каркаса кода.

Дополнительные сведения об использовании расширения Device Workbench для разработки устройств IoT см. в разделе [https://github.com/microsoft/vscode-iot-workbench](https://github.com/microsoft/vscode-iot-workbench).

## <a name="prerequisites"></a>Предварительные требования

Установите [Visual Studio Code](https://code.visualstudio.com/).

Чтобы установить расширение в VS Code, выполните следующие действия.

1. В VS Code выберите вкладку **расширения** .
1. Найдите и установите **Azure IOT Device Workbench** из Marketplace.

## <a name="generate-device-code-and-project"></a>Создание кода устройства и проекта

В VS Code используйте **сочетание клавиш CTRL + SHIFT + P** , чтобы открыть палитру команд, введите **IOT Plug and Play**и выберите **создать заглушку кода устройства** , чтобы настроить скелет кода и тип проекта. В следующем списке подробно описывается каждый шаг:

- **Файл DCM, используемый для создания кода**. Чтобы создать каркас кода устройства, откройте папку, содержащую файлы DCM и интерфейсов, которые он реализует. Если генератору кода не удается найти интерфейс, необходимый для DCM, он скачивает его из репозитория модели по мере необходимости.

- **Язык кода устройства**. В настоящее время генератор кода поддерживает только ANSI C.

- **Имя проекта**. Имя проекта используется в качестве имени папки для созданного кода и других файлов проекта. Папка по умолчанию размещается рядом с файлом DCM. Чтобы не копировать созданную папку кода вручную при обновлении DCM и повторном создании кода устройства, сохраните файл DCM в той же папке, где находится папка проекта.

- **Тип проекта**. Генератор кода также создает файл проекта, чтобы можно было интегрировать код в свой проект или в проект пакета SDK для устройств. В настоящее время поддерживаются следующие типы проектов:

    - **Проект CMAK**: для проекта устройства, в котором используется [CMAK](https://cmake.org/) как система сборки. Этот параметр создает `CMakeLists.txt` файл в той же папке, что и код на языке C.
    - **Проект MXChip IOT DevKit**: для проекта устройства, который выполняется на устройстве [MXChip IOT DevKit](https://aka.ms/iot-devkit) . Этот параметр создает проект Arduino, который можно [использовать в VS Code](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started) или в интегрированной среде разработки Arduino для сборки и запуска на устройстве IOT DevKit.

- **Способ подключения к Azure IOT**. Созданные файлы также содержат код, позволяющий настроить устройство для подключения к центру Интернета вещей Azure. Вы можете подключиться непосредственно к центру [Интернета вещей Azure](https://docs.microsoft.com/azure/iot-hub) или использовать [службу подготовки устройств](https://docs.microsoft.com/azure/iot-dps).

    - С **помощью строки подключения устройства центра Интернета вещей**. Укажите строку подключения устройства для подключения к центру Интернета вещей напрямую.
    - С **помощью симметричного ключа DPS**: укажите **идентификатор области**, **идентификатор регистрации**и **ключ SAS** для приложения устройства, необходимого для подключения к центру Интернета вещей или IOT Central с помощью DPS.

Генератор кода пытается использовать DCM и файлы интерфейса, расположенные в локальной папке. Если файлы интерфейса отсутствуют в локальной папке, генератор кода ищет их в общедоступном репозитории модели или в репозитории модели компании. [Общие файлы интерфейса](./concepts-common-interfaces.md) хранятся в репозитории общедоступной модели.

После завершения создания кода расширение открывает новое VS Code окно с кодом. При открытии созданного файла, например **Main. c**, может оказаться, что IntelliSense сообщает, что не удается открыть исходные файлы SDK c. Чтобы включить правильную технологию IntelliSense и навигацию по коду, выполните следующие действия, чтобы включить источник SDK C:

1. В VS Code используйте **сочетание клавиш CTRL + SHIFT + P** , чтобы открыть палитру команд, введите **и выберитеC++C/: Изменить конфигурации (JSON)** , чтобы открыть файл **c_cpp_properties. JSON** .

1. Добавьте путь к пакету SDK для устройства в `includePath` разделе:

    ```json
    "includePath": [
        "${workspaceFolder}/**",
        "{path_of_device_c_sdk}/**"
    ]
    ```

1. Сохраните файл.

## <a name="use-the-generated-code"></a>Использование созданного кода

Приведенные ниже инструкции описывают использование созданного кода в собственном проекте устройства на различных платформах машинного программирования. В инструкциях предполагается, что вы используете строку подключения устройства центра Интернета вещей с созданным кодом:

### <a name="linux"></a>Linux

Для сборки кода устройства вместе с пакетом SDK для устройства C с помощью CMak в среде Linux, такой как Ubuntu или Debian:

1. Откройте приложение терминала.

1. Установите компоненты **GCC**, **Git**, `cmake`и все зависимости с помощью `apt-get` команды:

    ```sh
    sudo apt-get update
    sudo apt-get install -y git cmake build-essential curl libcurl4-openssl-dev libssl-dev uuid-dev
    ```

    Убедитесь, что версия `cmake` более **2.8.12** , а версия **GCC** выше **4.4.7**.

    ```sh
    cmake --version
    gcc --version
    ```

1. Клонирование репозитория [SDK для Azure IOT C](https://github.com/Azure/azure-iot-sdk-c) :

    ```sh
    git clone https://github.com/Azure/azure-iot-sdk-c --recursive -b public-preview
    ```

    Выполнение этой операции может занять несколько минут.

1. Скопируйте папку, содержащую созданный код, в корневую папку пакета SDK для устройства.

1. В VS Code откройте `CMakeLists.txt` файл в корневой папке пакета SDK для устройства.

1. Добавьте следующую строку в конец `CMakeLists.txt` файла, чтобы включить папку заглушки кода устройства при компиляции пакета SDK:

    ```txt
    add_subdirectory({generated_code_folder_name})
    ```

1. Создайте папку с именем `cmake` в корневой папке пакета SDK для устройства и перейдите к этой папке.

    ```sh
    mkdir cmake
    cd cmake
    ```

1. Выполните следующие команды, чтобы создать пакет SDK для устройства и созданную заглушку кода с помощью CMak.

    ```cmd\sh
    cmake -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON ..
    cmake --build .

1. After the build succeeds, run the application specifying the IoT Hub device connection string as parameter.

    ```cmd\sh
    cd azure-iot-sdk-c/cmake/{generated_code_folder_name}/
    ./{generated_code_project_name} "[IoT Hub device connection string]"
    ```

### <a name="windows"></a>Windows

Чтобы создать код устройства вместе с пакетом SDK для устройства C в Windows с помощью CMak и Visual Studio C/C++ Compiler в командной строке, ознакомьтесь с кратким руководством по [Plug and Play IOT](./quickstart-create-pnp-device.md). Ниже показано, как создать код устройства вместе с пакетом SDK для устройства C как с помощью проекта CMak в Visual Studio.

1. Установите [Visual Studio 2019 (Community, Professional или Enterprise)](https://visualstudio.microsoft.com/downloads/) — убедитесь, что включен компонент **диспетчера пакетов NuGet** и **Разработка классических приложений с C++**  рабочей нагрузкой.

1. Откройте Visual Studio и на странице Начало **работы** выберите **клонировать или извлечь код**:

1. В **расположении репозитория**выполните Клонирование репозитория [SDK для Azure IOT C](https://github.com/Azure/azure-iot-sdk-c) :

    ```txt
    https://github.com/Azure/azure-iot-sdk-c
    ```

    Необходимо, чтобы выполнение этой операции занимало несколько минут, можно было увидеть ход выполнения на панели **Team Explorer** .

1. Откройте репозиторий **Azure-IOT-SDK-c** в **Team Explorer**, выберите **ветви**, найдите общедоступную ветвь и просмотрите ее.

    ![Общедоступная предварительная версия](media/howto-use-iot-device-workbench/vs-public-preview.png)

1. Скопируйте папку, содержащую созданный код, в корневую папку пакета SDK для устройства.

1. `azure-iot-sdk-c` Откройте папку в VS.

1. `CMakeLists.txt` Откройте файл в корневой папке пакета SDK для устройства.

1. Добавьте следующую строку в конец `CMakeLists.txt` файла, чтобы включить папку заглушки кода устройства при компиляции пакета SDK:

    ```txt
    add_subdirectory({generated_code_folder_name})
    ```

1. На панели инструментов **Общие** найдите раскрывающийся список **конфигураций** . Выберите **Управление конфигурацией** , чтобы добавить параметр CMAK для проекта.

    ![Управление конфигурацией](media/howto-use-iot-device-workbench/vs-manage-config.png)

1. В **параметрах CMAK**добавьте новую конфигурацию и выберите **x64-Release** в качестве целевого объекта.

1. В **аргументах команды CMAK**добавьте следующую строку:

    ```txt
    -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON
    ```

1. Сохраните файл.

1. В **Обозреватель решений**щелкните правой кнопкой мыши `CMakeLists.txt` в корневой папке и выберите в контекстном меню пункт **построить** , чтобы создать пакет SDK для устройства и созданную заглушку кода.

1. После завершения сборки в командной строке запустите приложение, указав строку подключения устройства центра Интернета вещей в качестве параметра.

    ```cmd
    cd %USERPROFILE%\CMakeBuilds\{workspaceHash}\build\x64-Release\{generated_code_folder_name}\
    {generated_code_project_name}.exe "[IoT Hub device connection string]"
    ```

> [!TIP]
> Дополнительные сведения об использовании CMak в Visual Studio см. в разделе [Build CMAK Project](https://docs.microsoft.com/cpp/build/cmake-projects-in-visual-studio?view=vs-2019#building-cmake-projects) .

### <a name="macos"></a>macOS

Ниже показано, как создать код устройства вместе с пакетом SDK для устройства C в macOS с помощью CMak.

1. Откройте приложение терминала.

1. Используйте [Homebrew](https://homebrew.sh) для установки всех зависимостей:

    ```bash
    brew update
    brew install git cmake pkgconfig openssl ossp-uuid
    ```

1. Убедитесь, что используется версия [CMAK](https://cmake.org/) не ниже **2.8.12**версии.

    ```bash
    cmake --version
    ```

1. [Прилистывание исправлений](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#upgrade-curl-on-mac-os) к последней доступной версии.

1. Клонирование репозитория [SDK для Azure IOT C](https://github.com/Azure/azure-iot-sdk-c) :

    ```bash
    git clone https://github.com/Azure/azure-iot-sdk-c --recursive -b public-preview
    ```

    Выполнение этой операции может занять несколько минут.

1. Скопируйте папку, содержащую созданный код, в корневую папку пакета SDK для устройства.

1. В VS Code откройте `CMakeLists.txt` файл в корневой папке пакета SDK для устройства.

1. Добавьте следующую строку в конец `CMakeLists.txt` файла, чтобы включить папку заглушки кода устройства при компиляции пакета SDK:

    ```txt
    add_subdirectory({generated_code_folder_name})
    ```

1. Создайте папку с именем `cmake` в корневой папке пакета SDK для устройства и перейдите к этой папке.

    ```bash
    mkdir cmake
    cd cmake
    ```

1. Выполните следующие команды, чтобы создать пакет SDK для устройства и созданную заглушку кода с помощью CMak.

    ```bash
    cmake -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DOPENSSL_ROOT_DIR:PATH=/usr/local/opt/openssl ..
    cmake --build .

1. After the build succeeds, run the application specifying the IoT Hub device connection string as parameter.

    ```bash
    cd azure-iot-sdk-c/cmake/{generated_code_folder_name}/
    ./{generated_code_project_name} "[IoT Hub device connection string]"
    ```

## <a name="iterate-by-regenerating-the-skeleton-code"></a>Перебор путем повторного создания каркаса кода

Генератор кода может повторно создать код при обновлении файлов DCM или интерфейса. Предположим, что вы уже создали код устройства из файла DCM, чтобы повторно создать код:

1. После открытия папки с файлами DCM нажмите **клавиши CTRL + SHIFT + P** , чтобы открыть палитру команд, введите **IOT Plug and Play**и выберите **создать заглушку кода устройства**.

1. Выберите обновленный файл DCM.

1. Выберите **повторно создать код для {Project Name}** .

1. В генераторе кода используется предыдущий параметр, который вы настроили и повторно создаете код. Однако он не перезаписывает файлы, которые могут содержать пользовательский код, например `main.c` и. `{project_name}_impl.c`

> [!NOTE]
> Если вы обновляете идентификатор URN в файле интерфейса, он рассматривается как новый интерфейс. При повторном создании кода генератор кода создает код для интерфейса, но не перезаписывает исходный `{project_name}_impl.c` файл в файле.

## <a name="problems-and-feedback"></a>Проблемы и отзывы

Расширение Azure IoT Device Workbench — это проект с открытым кодом на GitHub. При возникновении проблем и запросов функций можно [создать вопрос на GitHub](https://github.com/microsoft/vscode-iot-workbench/issues).

## <a name="next-steps"></a>Следующие шаги

В этом пошаговом руководстве вы узнали, как с помощью Azure IoT Device Workbench создавать файлы DCM и интерфейсов. Вы также узнали, как создать скелет кода на языке C для реализации приложения устройства. Предлагаемый следующий шаг — Узнайте, как [установить и использовать средство Azure IOT Explorer](./howto-install-iot-explorer.md) .
