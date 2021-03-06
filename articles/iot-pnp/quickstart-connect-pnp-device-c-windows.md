---
title: Подключение примера кода устройства IoT Plug and Play (предварительная версия) в Windows к Центру Интернета вещей | Документация Майкрософт
description: Создайте и запустите пример кода устройства IoT Plug and Play (предварительная версия) в Windows, который подключается к Центру Интернета вещей. С помощью обозревателя Интернета вещей Azure просматривайте сведения, отправленные устройством в центр.
author: ChrisGMsft
ms.author: chrisgre
ms.date: 12/26/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 1c8b6a5d133d8838c2c7a23f8881092affa424dc
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/28/2019
ms.locfileid: "75531217"
---
# <a name="quickstart-connect-a-sample-iot-plug-and-play-preview-device-application-running-on-windows-to-iot-hub-c-windows"></a>Краткое руководство. Подключение примера приложения устройства IoT Plug and Play (предварительная версия) в Windows к Центру Интернета вещей (C для Windows)

[!INCLUDE [iot-pnp-quickstarts-2-selector.md](../../includes/iot-pnp-quickstarts-2-selector.md)]

В этом кратком руководстве показано, как создать пример приложения устройства IoT Plug and Play, подключить его к Центру Интернета вещей и с помощью обозревателя IoT Azure просмотреть сведения, отправляемые в Центр. Пример приложения написан на языке C и включен в пакет SDK для устройств Центра Интернета вещей Azure для C. Разработчик решения может использовать обозреватель Интернета вещей Azure, чтобы ознакомиться с возможностями устройства IoT Plug and Play, не просматривая код устройства.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>предварительные требования

Для выполнения инструкций, указанных в этом кратком руководстве, вам необходимо установить на локальный компьютер следующее программное обеспечение:

* [Visual Studio (Community, Professional или Enterprise)](https://visualstudio.microsoft.com/downloads/). При установке Visual Studio обязательно добавьте компонент **Диспетчер пакетов NuGet** и рабочую нагрузку **Разработка классических приложений на C++** .
* [Git](https://git-scm.com/download/).
* [CMake](https://cmake.org/download/).

### <a name="install-the-azure-iot-explorer"></a>Установка обозревателя Azure IoT

Скачайте и установите последний выпуск **обозревателя Azure IoT** со страницы [репозитория](https://github.com/Azure/azure-iot-explorer/releases) инструментов, выбрав MSI-файл в разделе "Assets" (Ресурсы) для последнего обновления.

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Выполните следующую команду, чтобы получить _строку подключения к вашему Центру Интернета вещей_ (запишите для использования в будущем):

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

## <a name="prepare-the-development-environment"></a>Подготовка среды разработки

С помощью этого краткого руководства вы подготовите среду разработки, которую можно использовать для клонирования и сборки пакета SDK для устройств Центра Интернета вещей Azure для C.

Откройте командную строку в выбранном каталоге. Выполните следующую команду для клонирования репозитория GitHub [пакетов SDK и библиотек Интернета вещей Azure для С](https://github.com/Azure/azure-iot-sdk-c) в это расположение:

```cmd/sh
git clone https://github.com/Azure/azure-iot-sdk-c --recursive -b public-preview
```

Выполнение этой операции может занять несколько минут.

## <a name="build-the-code"></a>Сборка кода

Используйте пакет SDK для устройств, чтобы выполнить сборку прилагаемого примера кода. Создаваемое приложение имитирует устройство, которое подключается к Центру Интернета вещей. Оно отправляет данные телеметрии и свойства, а также получает команды.

1. Создайте подкаталог `cmake` в корневой папке пакета SDK для устройства и перейдите к этой папке:

    ```cmd\sh
    cd <root folder>\azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

1. Выполните приведенные ниже команды, чтобы собрать пакет SDK для устройства и сгенерированную заглушку кода:

    ```cmd\sh
    cmake ..
    cmake --build . -- /m /p:Configuration=Release
    ```

    > [!NOTE]
    > Если cmake не может найти ваш компилятор C++, при запуске предыдущей команды отобразятся ошибки сборки. В этом случае попробуйте выполнить эту команду в [командной строке Visual Studio](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs).

## <a name="run-the-device-sample"></a>Запуск примера устройства

Запустите пример приложения в пакете SDK для имитации устройства IoT Plug and Play, отправляющего данные телеметрии в ваш центр Интернета вещей. Чтобы запустить пример приложения, используйте следующие команды и передайте строку подключения _устройства_ в качестве параметра.

```cmd\sh
cd digitaltwin_client\samples\digitaltwin_sample_device\Release
copy ..\EnvironmentalSensor.interface.json .
digitaltwin_sample_device.exe "<YourDeviceConnectionString>"
```

Теперь устройство готово к получению команд и обновлений свойств и начало отправлять данные телеметрии в центр. Продолжите работу примера, после того как выполните следующие действия.

## <a name="use-the-azure-iot-explorer-to-validate-the-code"></a>Проверка кода с помощью обозревателя Azure IoT

[!INCLUDE [iot-pnp-iot-explorer-1.md](../../includes/iot-pnp-iot-explorer-1.md)]

4. Чтобы убедиться, что средство может считывать определения моделей интерфейсов с устройства, выберите **Параметры**. В меню "Параметры" **On the connected device** (На подключенном устройстве) может уже отображаться в конфигурациях Plug and Play. Если это не так, выберите **+ Add module definition source** (+ Добавить источник определения модуля), а затем **On the connected device** (На подключенном устройстве), чтобы добавить его.

1. На странице обзора **устройства** найдите созданное ранее удостоверение устройства. Если приложение устройства по-прежнему выполняется в командной строке, убедитесь, что **состояние подключения устройства** в обозревателе Интернета вещей Azure — _Подключено_ (в противном случае нажимайте кнопку **Refresh** (Обновить), пока это состояние не появится). Выберите устройство, чтобы просмотреть дополнительные сведения.

1. Разверните интерфейс, указав идентификатор **urn:НАЗВАНИЕ_ВАШЕЙ_КОМПАНИИ:EnvironmentSensor:1**, чтобы увидеть интерфейс и примитивы IoT Plug and Play: свойства, команды и данные телеметрии.

[!INCLUDE [iot-pnp-iot-explorer-2.md](../../includes/iot-pnp-iot-explorer-2.md)]

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Дальнейшие действия

Из этого краткого руководства вы узнали, как подключить устройство IoT Plug and Play к Центру Интернета вещей. Дополнительные сведения о создании решения, взаимодействующего с устройствами IoT Plug and Play, см. в следующей статье:

> [!div class="nextstepaction"]
> [Практическое руководство. Подключение к устройству IoT Plug and Play (предварительная версия) и взаимодействие с ним](howto-develop-solution.md)
