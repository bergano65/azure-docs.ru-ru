---
title: Руководство по потокам устройств Центра Интернета вещей в C для SSH/RDP (предварительная версия) | Документация Майкрософт
description: В этом кратком руководстве будет выполняться запуск примера приложения C, которое выступает в качестве прокси-сервера, для реализации сценариев SSH/RDP через потоки устройств Центра Интернета вещей.
author: rezasherafat
manager: briz
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: quickstart
ms.custom: mvc
ms.date: 01/15/2019
ms.author: rezas
ms.openlocfilehash: d0fc8d68b3412c2c43a88e3a9484dab3a150b811
ms.sourcegitcommit: b4755b3262c5b7d546e598c0a034a7c0d1e261ec
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/24/2019
ms.locfileid: "54886277"
---
# <a name="quickstart-sshrdp-over-iot-hub-device-streams-using-c-proxy-application-preview"></a>Краткое руководство. SSH/RDP через потоки устройств Центра Интернета вещей с помощью приложения прокси C (предварительная версия)

[!INCLUDE [iot-hub-quickstarts-4-selector](../../includes/iot-hub-quickstarts-4-selector.md)]

[Потоки устройств Центра Интернета вещей](./iot-hub-device-streams-overview.md) позволяют службам и приложениям устройств безопасным и подходящим методом обмениваться данными с брандмауэром. Общие сведения о настройке см. на [этой странице](./iot-hub-device-streams-overview.md#local-proxy-sample-for-ssh-or-rdp).

В этом документе описывается настройка для туннелирования трафика SSH (используя порт 22) через потоки устройств. Настройка для трафика RDP выполняется аналогично и требует простого изменения конфигурации. Так как потоки устройств не зависят от приложений и протоколов, текущее краткое руководство можно изменить (путем изменения коммуникационных портов) для размещения других типов трафика приложений.

## <a name="how-it-works"></a>Принципы работы.
На рисунке, приведенном ниже, показано, как программы локального прокси-сервера устройств и служб обеспечивают сквозное подключение между клиентом SSH и процессами управляющей программы SSH. На этапе предварительной версии пакет SDK для C поддерживает только потоки устройств на стороне устройства. В результате это краткое руководство охватывает только инструкции по запуску приложения локального прокси-сервера устройства. Вам следует запустить сопутствующее приложение локального прокси-сервера службы, которое доступно в кратких руководствах [по C#](./quickstart-device-streams-proxy-csharp.md) и [по Node.js](./quickstart-device-streams-proxy-nodejs.md).

![Замещающий текст](./media/quickstart-device-streams-proxy-csharp/device-stream-proxy-diagram.svg "Настройка локального прокси-сервера")


1. Локальный прокси-сервер службы подключается к Центру Интернета вещей и инициирует поток устройств в целевое устройство.

2. Локальный прокси-сервер устройства завершает подтверждение инициации потоковой передачи и устанавливает сквозной потоковый туннель через конечную точку потоковой передачи Центра Интернета вещей на стороне службы.

3. Локальный прокси-сервер устройства подключается к управляющей программе SSH (SSHD), прослушивающей порт 22 на устройстве (это можно настроить, как описано [ниже](#run-the device-local-proxy-application)).

4. Локальный прокси-сервер службы ожидает новые SSH-подключения от пользователя, прослушивая назначенный порт, который в этом случае является портом 2222 (это также можно настроить, как описано [ниже](#run-the-device-local-proxy-application)). Когда пользователь подключается через клиент SSH, туннель позволяет передавать трафик приложения SSH между клиентом SSH и программами сервера.

> [!NOTE]
> Трафик SSH, передаваемый по потоку устройств, будет туннелироваться через конечную точку потоковой передачи Центра Интернета вещей, а не напрямую между службой и устройством. Это обеспечивает [такие преимущества](./iot-hub-device-streams-overview.md#benefits). Кроме того, на рисунке показана управляющая программа SSH, запущенная на том же устройстве (или компьютере), что и локальный прокси-сервер устройства. В этом кратком руководстве предоставление IP-адреса управляющей программы SSH позволяет локальному прокси-серверу устройства и управляющей программе работать также на разных компьютерах.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

* Установите [Visual Studio 2017](https://www.visualstudio.com/vs/) с включенной рабочей нагрузкой [Разработка классических приложений на C++](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/).
* Установите последнюю версию [Git](https://git-scm.com/download/).

## <a name="prepare-the-development-environment"></a>Подготовка среды разработки

В этом кратком руководстве вы будете использовать [пакет SDK для устройств Azure IoT для C](iot-hub-device-sdk-c-intro.md). Вы подготовите среду разработки, которая используется для клонирования и сборки [пакета SDK Azure IoT для C](https://github.com/Azure/azure-iot-sdk-c) с сайта GitHub. Пакет SDK на сайте GitHub содержит пример кода, используемый в этом кратком руководстве. 


1. Скачайте версию 3.11.4 [системы сборки CMake](https://cmake.org/download/). Проверьте загруженный бинарный дистрибутив, используя соответствующее значение криптографического хэша. В следующем примере для проверки криптографического хэша для дистрибутива MSI x64 версии 3.11.4 использовалась среда Windows PowerShell:

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

    **Перед** установкой `CMake` очень важно установить на компьютер необходимые компоненты Visual Studio (Visual Studio с рабочей нагрузкой "Разработка классических приложений на C++"). После установки компонентов и проверки загрузки установите систему сборки CMake.

2. Откройте командную строку или оболочку Git Bash. Выполните следующую команду для клонирования репозитория GitHub [пакета SDK для устройства C Интернета вещей Azure](https://github.com/Azure/azure-iot-sdk-c):
    
    ```
    git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive
    ```
    Размер этого репозитория в настоящее время составляет примерно 220 МБ. Выполнение этой операции может занять несколько минут.


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
    cmake ..
    make -j
```

В Windows выполните следующие команды в Командной строке разработчика, чтобы появился запрос Visual Studio 2015 или 2017:

```
    # In Windows
    # For VS2015
    $ cmake .. -G "Visual Studio 15 2015"
    
    # Or for VS2017
    $ cmake .. -G "Visual Studio 15 2017

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

2. Выполните следующую команду в Azure Cloud Shell, чтобы получить _строку подключения_ зарегистрированного устройства.

   **YourIoTHubName**. Замените этот заполнитель именем вашего Центра Интернета вещей.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name YourIoTHubName --device-id MyDevice --output table
    ```

    Запишите строку подключения устройства, которая выглядит, как на следующем примере.

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyDevice;SharedAccessKey={YourSharedAccessKey}`

    Это значение понадобится позже в рамках этого краткого руководства.


## <a name="ssh-to-a-device-via-device-streams"></a>SSH-подключение к устройству через потоки устройств

### <a name="run-the-device-local-proxy-application"></a>Запуск приложения локального прокси-сервера устройства

- Отредактируйте исходный файл `iothub_client/samples/iothub_client_c2d_streaming_proxy_sample/iothub_client_c2d_streaming_proxy_sample.c` и укажите строку подключения устройства, IP-адрес или имя узла целевого устройства, а также порт RDP 22:
```C
  /* Paste in the your iothub connection string  */
  static const char* connectionString = "[Connection string of IoT Hub]";
  static const char* localHost = "[IP/Host of your target machine]"; // Address of the local server to connect to.
  static const size_t localPort = 22; // Port of the local server to connect to.
```

- Скомпилируйте пример следующим образом:

```
    # In Linux
    # Go to the sample's folder cmake/iothub_client/samples/iothub_client_c2d_streaming_proxy_sample
    $ make -j


    # In Windows
    # Go to cmake at root of repository
    cmake --build . -- /m /p:Configuration=Release
```

- Запустите скомпилированную программу на устройстве:
```
    # In Linux
    # Go to sample's folder cmake/iothub_client/samples/iothub_client_c2d_streaming_proxy_sample
    $ ./iothub_client_c2d_streaming_proxy_sample

    # In Windows
    # Go to sample's release folder cmake\iothub_client\samples\iothub_client_c2d_streaming_proxy_sample\Release
    iothub_client_c2d_streaming_proxy_sample.exe
```

### <a name="run-the-service-local-proxy-application"></a>Запуск приложения локального прокси-сервера службы

Как описано [выше](#how-it-works), для создания сквозной потоковой передачи для туннелирования трафика SSH требуется локальный прокси-сервер с каждой стороны (т. е. службы и устройства). Тем не менее во время предварительной версии пакет C SDK поддерживает только потоки устройств на стороне устройства. Для локального прокси-сервера службы используйте прилагаемые руководства [по C#](./quickstart-device-streams-proxy-csharp.md) или [по Node.js](./quickstart-device-streams-proxy-nodejs.md).


### <a name="establish-an-ssh-session"></a>Установка сеанса SSH

Предположим, что локальные прокси-серверы устройства и службы запущены, теперь используйте клиентскую программу SSH и подключитесь к локальному прокси-серверу службы через порт 2222 (вместо подключения напрямую через управляющую программу SSH). 

```
ssh <username>@localhost -p 2222
```

На этом этапе откроется командная строка входа SSH для ввода учетных данных.


Вывод на консоль на локальном прокси-сервере устройства, который подключается к управляющей программе SSH по адресу `IP_address:22`: ![Замещающий текст](./media/quickstart-device-streams-proxy-c/device-console-output.PNG "Вывод локального прокси-сервера устройства")

Вывод на консоль клиентской программы SSH (клиент SSH связывается с управляющей программой SSH, подключаясь к порту 22, где прослушивает локальный прокси-сервер службы): ![Замещающий текст](./media/quickstart-device-streams-proxy-csharp/ssh-console-output.png "Вывод клиента SSH")

## <a name="clean-up-resources"></a>Очистка ресурсов

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Дополнительная информация

В рамках этого краткого руководства вы настроили Центр Интернета вещей, зарегистрировали устройство, развернули программу локальных прокси-серверов устройств и служб, чтобы установить потоковую передачу устройств через Центр Интернета вещей, а также использовали прокси-серверы для туннелирования трафика SSH.

Используйте приведенные ниже ссылки для получения дополнительных сведений о потоках устройства:

> [!div class="nextstepaction"]
> [IoT Hub Device Streams (preview)](./iot-hub-device-streams-overview.md) (Потоки устройств (предварительная версия))
