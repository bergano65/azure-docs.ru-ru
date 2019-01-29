---
title: Руководство по потоковой передаче Центра Интернета вещей в C (предварительная версия) | Документация Майкрософт
description: В этом кратком руководстве будет выполняться приложение C на стороне службы, которое обменивается данными с устройством Интернета вещей через поток устройств.
author: rezasherafat
manager: briz
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: quickstart
ms.custom: mvc
ms.date: 01/15/2019
ms.author: rezas
ms.openlocfilehash: 149d3e3515c8bbc10798ad26e58dcefbe830f9bc
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/23/2019
ms.locfileid: "54830738"
---
# <a name="quickstart-communicate-to-a-device-application-in-c-via-iot-hub-device-streams-preview"></a>Краткое руководство. Взаимодействие с приложением устройства в C с помощью потоков устройств Центра Интернета вещей (предварительная версия)

[!INCLUDE [iot-hub-quickstarts-3-selector](../../includes/iot-hub-quickstarts-3-selector.md)]

[Потоки устройств Центра Интернета вещей](./iot-hub-device-streams-overview.md) позволяют службам и приложениям устройств безопасным и подходящим методом обмениваться данными с брандмауэром. На этапе предварительной версии пакет SDK для C поддерживает только потоки устройств на стороне устройства. В результате это краткое руководство охватывает только инструкции по запуску приложения на стороне устройства. Вам следует запустить сопутствующее приложение на стороне службы, которое доступно в кратких руководствах [по C#](./quickstart-device-streams-echo-csharp.md) и [по Node.js](./quickstart-device-streams-echo-nodejs.md).

Приложение C на стороне устройства, используемое в этом кратком руководстве, имеет следующие функции:

* установка потока устройств к устройству Интернета вещей;

* получение данных, отправленных со стороны сервиса, и отправка их обратно.

Код продемонстрирует процесс инициализации потока устройств, а также его использование для отправки и получения данных.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

* Установите [Visual Studio 2017](https://www.visualstudio.com/vs/) с включенной рабочей нагрузкой [Разработка классических приложений на C++](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/).
* Установите последнюю версию [Git](https://git-scm.com/download/).

## <a name="prepare-the-development-environment"></a>Подготовка среды разработки

В этом кратком руководстве вы будете использовать [пакет SDK для устройств Azure IoT для C](iot-hub-device-sdk-c-intro.md). Вы подготовите среду разработки, которая используется для клонирования и сборки [пакета SDK Azure IoT для C](https://github.com/Azure/azure-iot-sdk-c) с сайта GitHub. Пакет SDK на сайте GitHub содержит пример кода, используемый в этом кратком руководстве. 


1. Скачайте версию 3.11.4 [системы сборки CMake](https://cmake.org/download/). Проверьте загруженный бинарный дистрибутив, используя соответствующее значение криптографического хэша. В следующем примере для проверки криптографического хэша для дистрибутива MSI x64 версии 3.11.4 использовалась среда Windows PowerShell:

    ```PowerShell
    PS C:\Downloads> $hash = get-filehash .\cmake-3.11.4-win64-x64.msi
    PS C:\Downloads> $hash.Hash -eq "56e3605b8e49cd446f3487da88fcc38cb9c3e9e99a20f5d4bd63e54b7a35f869"
    True
    ```
    
    Приведенные ниже значения хэша для версии 3.11.4 были указаны на сайте CMake на момент написания этой статьи.

    ```
    6dab016a6b82082b8bcd0f4d1e53418d6372015dd983d29367b9153f1a376435  cmake-3.11.4-Linux-x86_64.tar.gz
    72b3b82b6d2c2f3a375c0d2799c01819df8669dc55694c8b8daaf6232e873725  cmake-3.11.4-win32-x86.msi
    56e3605b8e49cd446f3487da88fcc38cb9c3e9e99a20f5d4bd63e54b7a35f869  cmake-3.11.4-win64-x64.msi
    ```

2. Откройте командную строку или оболочку Git Bash. Выполните следующую команду для клонирования репозитория GitHub [пакета SDK для устройства C Интернета вещей Azure](https://github.com/Azure/azure-iot-sdk-c):
    
    ```
    git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive
    ```

    Размер этого репозитория в настоящее время составляет примерно 220 МБ.

3. Создайте подкаталог `cmake` в корневом каталоге репозитория Git и перейдите в эту папку. 

    ```
    cd azure-iot-sdk-c
    git checkout public-preview
    mkdir cmake
    cd cmake
    ```

4. Выполните приведенную ниже команду, чтобы создать версию пакета SDK для используемой клиентской платформы разработки. В Windows эта команда также создает решение Visual Studio для имитированного устройства в каталоге `cmake`. 

```
    # In Linux
    cmake -Denable_streaming=ON ..
    make -j
```

В Windows выполните следующие команды в Командной строке разработчика, чтобы появился запрос Visual Studio 2015 или 2017:

```
    # In Windows
    # For VS2015
    cmake -Denable_streaming=ON .. -G "Visual Studio 15 2015"
    
    # Or for VS2017
    cmake -Denable_streaming=ON .. -G "Visual Studio 15 2017

    # Then build the project
    cmake --build . -- /m /p:Configuration=Release
```

## <a name="create-an-iot-hub"></a>Создание Центра Интернета вещей

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub-device-streams.md)]

## <a name="register-a-device"></a>Регистрация устройства

Устройство должно быть зарегистрировано в Центре Интернета вещей, прежде чем оно сможет подключиться. В этом разделе описано, как регистрировать имитированное устройство с помощью [расширения Интернета вещей](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot?view=azure-cli-latest) для Azure Cloud Shell.

1. Выполните приведенные ниже команды в Azure Cloud Shell, чтобы добавить расширение CLI Центра Интернета вещей и создать удостоверение устройства. 

   **YourIoTHubName**. Замените этот заполнитель именем вашего Центра Интернета вещей.

   **MyDevice**. Это имя, присвоенное зарегистрированному устройству. Используйте имя MyDevice, как показано в примере. Если вы выбрали другое имя для устройства, используйте его при работе с этим руководством и обновите имя устройства в примерах приложений перед их запуском.

    ```azurecli-interactive
    az extension add --name azure-cli-iot-ext
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyDevice
    ```

2. Выполните следующую команду в Azure Cloud Shell, чтобы получить _строку подключения_ зарегистрированного устройства:

   **YourIoTHubName**. Замените этот заполнитель именем вашего Центра Интернета вещей.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name YourIoTHubName --device-id MyDevice --output table
    ```

    Запишите строку подключения устройства, которая выглядит, как на следующем примере.

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyDevice;SharedAccessKey={YourSharedAccessKey}`

    Это значение понадобится позже в рамках этого краткого руководства.


## <a name="communicate-between-device-and-service-via-device-streams"></a>Обмен данными между устройством и службой через потоки устройств

### <a name="run-the-device-side-application"></a>Запуск приложения на стороне устройства

Для запуска приложения на стороне устройства необходимо выполнить следующие шаги.
- Настройте среду разработки, используя инструкции в этой [статье о потоках устройств](https://github.com/Azure/azure-iot-sdk-c-tcpstreaming/blob/master/iothub_client/readme.md#compiling-the-device-sdk-for-c).

- Укажите учетные данные устройства, отредактировав исходный файл `iothub_client/samples/iothub_client_c2d_streaming_sample/iothub_client_c2d_streaming_sample.c` и предоставив строку подключения к устройству.
```C
  /* Paste in the your iothub connection string  */
  static const char* connectionString = "[device connection string]";
```

- Скомпилируйте код, как показано:

```
  # In Linux
  # Go to the sample's folder cmake/iothub_client/samples/iothub_client_c2d_streaming_sample
  make -j


  # In Windows
  # Go to the cmake folder at the root of repo
  cmake --build . -- /m /p:Configuration=Release
```

- Запустите скомпилированную программу:

```
  # In Linux
  # Go to sample's folder
  cmake/iothub_client/samples/iothub_client_c2d_streaming_sample
  ./iothub_client_c2d_streaming_sample


  # In Windows
  # Go to sample's release folder
  cmake\iothub_client\samples\iothub_client_c2d_streaming_sample\Release
  iothub_client_c2d_streaming_sample.exe
```

### <a name="run-the-service-side-application"></a>Запуск приложения на стороне службы

Как упоминалось ранее, пакет SDK для C Центра Интернета вещей поддерживает только потоки устройств на стороне устройства. Для приложения на стороне службы используйте сопутствующие программы на стороне службы, доступные в кратких руководствах [по C#](./quickstart-device-streams-echo-csharp.md) и [по Node.js](./quickstart-device-streams-echo-nodejs.md).


## <a name="clean-up-resources"></a>Очистка ресурсов

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Дополнительная информация

При работе с этим кратким руководством вы настроили Центр Интернета вещей, зарегистрировали устройство, отправили имитированные данные телеметрии в центр с помощью приложения C, а также считали данные телеметрии из центра, используя Azure Cloud Shell.

Используйте приведенные ниже ссылки для получения дополнительных сведений о потоках устройства:

> [!div class="nextstepaction"]
> [IoT Hub Device Streams (preview)](./iot-hub-device-streams-overview.md) (Потоки устройств (предварительная версия))