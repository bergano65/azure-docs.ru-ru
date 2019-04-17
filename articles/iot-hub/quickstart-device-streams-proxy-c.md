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
ms.date: 03/14/2019
ms.author: rezas
ms.openlocfilehash: 78aa8653385a126cf40e851332d50eac4c293390
ms.sourcegitcommit: 045406e0aa1beb7537c12c0ea1fbf736062708e8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/04/2019
ms.locfileid: "59005992"
---
# <a name="quickstart-sshrdp-over-iot-hub-device-streams-using-c-proxy-application-preview"></a>Краткое руководство. SSH/RDP через потоки устройств Центра Интернета вещей с помощью приложения прокси C (предварительная версия)

[!INCLUDE [iot-hub-quickstarts-4-selector](../../includes/iot-hub-quickstarts-4-selector.md)]

Центр Интернета вещей Microsoft Azure поддерживает потоки устройств, которые сейчас доступны в режиме [предварительной версии](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

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

* Предварительная версия потоков устройств сейчас поддерживается только в Центрах Интернета вещей, созданных в следующих регионах.

  * **Центральный регион США**
  * **Центральная часть США (EUAP)**

* Установите [Visual Studio 2017](https://www.visualstudio.com/vs/) с включенной рабочей нагрузкой [Разработка классических приложений на C++](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/).
* Установите последнюю версию [Git](https://git-scm.com/download/).
* Выполните следующую команду, чтобы добавить расширение Интернета вещей Microsoft Azure для Azure CLI в экземпляр Cloud Shell. Расширение Интернета вещей добавляет в Azure CLI специальные команды Центра Интернета вещей, IoT Edge и службы подготовки устройств Интернета вещей (DPS).

   ```azurecli-interactive
   az extension add --name azure-cli-iot-ext
   ```

## <a name="prepare-the-development-environment"></a>Подготовка среды разработки

В этом кратком руководстве вы будете использовать [пакет SDK для устройств Azure IoT для C](iot-hub-device-sdk-c-intro.md). Вы подготовите среду разработки, которая используется для клонирования и сборки [пакета SDK Azure IoT для C](https://github.com/Azure/azure-iot-sdk-c) с сайта GitHub. Пакет SDK на сайте GitHub содержит пример кода, используемый в этом кратком руководстве. 

1. Скачайте [систему сборки CMake](https://cmake.org/download/). Проверьте загруженный бинарный дистрибутив с помощью криптографического хэша, соответствующего скачанной версии. Криптографические значения хэша также доступны по предоставленной ссылке для скачивания CMake.

    В следующем примере для проверки криптографического хэша для дистрибутива MSI x64 версии 3.13.4 использовалась среда Windows PowerShell.

    ```powershell
    PS C:\Downloads> $hash = get-filehash .\cmake-3.13.4-win64-x64.msi
    PS C:\Downloads> $hash.Hash -eq "64AC7DD5411B48C2717E15738B83EA0D4347CD51B940487DFF7F99A870656C09"
    True
    ```

    Приведенные ниже значения хэша для версии 3.13.4 были указаны на сайте CMake на момент написания этой статьи.

    ```
    563a39e0a7c7368f81bfa1c3aff8b590a0617cdfe51177ddc808f66cc0866c76  cmake-3.13.4-Linux-x86_64.tar.gz
    7c37235ece6ce85aab2ce169106e0e729504ad64707d56e4dbfc982cb4263847  cmake-3.13.4-win32-x86.msi
    64ac7dd5411b48c2717e15738b83ea0d4347cd51b940487dff7f99a870656c09  cmake-3.13.4-win64-x64.msi
    ```

    **Перед** установкой `CMake` очень важно установить на компьютер необходимые компоненты Visual Studio (Visual Studio с рабочей нагрузкой "Разработка классических приложений на C++"). После установки компонентов и проверки загрузки установите систему сборки CMake.

2. Откройте командную строку или оболочку Git Bash. Выполните следующую команду для клонирования репозитория GitHub [пакета SDK для устройства C Интернета вещей Azure](https://github.com/Azure/azure-iot-sdk-c):
    
    ```
    git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive -b public-preview
    ```
    Размер этого репозитория в настоящее время составляет примерно 220 МБ. Выполнение этой операции может занять несколько минут.

3. Создайте подкаталог `cmake` в корневом каталоге репозитория Git и перейдите в эту папку. 

    ```
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

4. Выполните приведенную ниже команду из каталога `cmake`, чтобы создать версию пакета SDK для используемой клиентской платформы разработки.

   * В Linux:

      ```bash
      cmake ..
      make -j
      ```

   * В Windows выполните приведенные ниже команды в Командной строке разработчика Visual Studio 2015 или 2017. Эта команда также создает решение Visual Studio для имитированного устройства в каталоге `cmake`.

      ```cmd
      rem For VS2015
      cmake .. -G "Visual Studio 14 2015"

      rem Or for VS2017
      cmake .. -G "Visual Studio 15 2017"

      rem Then build the project
      cmake --build . -- /m /p:Configuration=Release
      ```

## <a name="create-an-iot-hub"></a>Создание Центра Интернета вещей

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub-device-streams.md)]

## <a name="register-a-device"></a>Регистрация устройства

Устройство должно быть зарегистрировано в Центре Интернета вещей, прежде чем оно сможет подключиться. В этом разделе описано, как регистрировать имитированное устройство с помощью [расширения Интернета вещей](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot?view=azure-cli-latest) для Azure Cloud Shell.

1. Выполните приведенные ниже команды в Azure Cloud Shell, чтобы создать удостоверение устройства.

   **YourIoTHubName**. Замените этот заполнитель именем вашего Центра Интернета вещей.

   **MyDevice**. Это имя, присвоенное зарегистрированному устройству. Используйте имя MyDevice, как показано в примере. Если вы выбрали другое имя для устройства, используйте его при работе с этим руководством и обновите имя устройства в примерах приложений перед их запуском.

    ```azurecli-interactive
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

## <a name="ssh-to-a-device-via-device-streams"></a>SSH-подключение к устройству через потоки устройств

### <a name="run-the-device-local-proxy-application"></a>Запуск приложения локального прокси-сервера устройства

1. Отредактируйте исходный файл `iothub_client/samples/iothub_client_c2d_streaming_proxy_sample/iothub_client_c2d_streaming_proxy_sample.c` и укажите строку подключения устройства, IP-адрес или имя узла целевого устройства, а также порт SSH 22.

   ```C
   /* Paste in the your iothub connection string  */
   static const char* connectionString = "[Connection string of IoT Hub]";
   static const char* localHost = "[IP/Host of your target machine]"; // Address of the local server to connect to.
   static const size_t localPort = 22; // Port of the local server to connect to.
   ```

2. Компиляция образца

   ```bash
    # In Linux
    # Go to the sample's folder cmake/iothub_client/samples/iothub_client_c2d_streaming_proxy_sample
    make -j
   ```

   ```cmd
    rem In Windows
    rem Go to cmake at root of repository
    cmake --build . -- /m /p:Configuration=Release
   ```

3. Запустите скомпилированную программу на устройстве:

   ```bash
    # In Linux
    # Go to the sample's folder cmake/iothub_client/samples/iothub_client_c2d_streaming_proxy_sample
    ./iothub_client_c2d_streaming_proxy_sample
   ```

   ```cmd
    rem In Windows
    rem Go to the sample's release folder cmake\iothub_client\samples\iothub_client_c2d_streaming_proxy_sample\Release
    iothub_client_c2d_streaming_proxy_sample.exe
   ```

### <a name="run-the-service-local-proxy-application"></a>Запуск приложения локального прокси-сервера службы

Как обсуждалось [ранее](#how-it-works), для создания сквозного потока для туннелирования трафика SSH требуется локальный прокси-сервер на каждом конце (как на службе, так и на устройстве). На стадии общедоступной предварительной версии пакет SDK C Центра Интернета вещей поддерживает только потоки устройств на стороне устройства. Чтобы собрать и запустить локальный прокси-сервер, выполните указания из краткого руководства по [C#](./quickstart-device-streams-proxy-csharp.md) или [Node.js](./quickstart-device-streams-proxy-nodejs.md).

### <a name="establish-an-ssh-session"></a>Установка сеанса SSH

После запуска локальных прокси-серверов устройств и служб используйте клиентскую программу SSH и подключитесь к локальному прокси-серверу службы через порт 2222 (вместо подключения напрямую через управляющую программу SSH).

```cmd/sh
ssh <username>@localhost -p 2222
```

На этом этапе откроется командная строка входа SSH для ввода учетных данных.

Вывод на консоль на локальном прокси-сервере устройства, который подключается к управляющей программе SSH по адресу `IP_address:22`: ![Замещающий текст](./media/quickstart-device-streams-proxy-c/device-console-output.PNG "Вывод локального прокси-сервера устройства")

Вывод на консоль клиентской программы SSH (клиент SSH связывается с управляющей программой SSH, подключаясь к порту 22, который прослушивает локальный прокси-сервер службы). ![Замещающий текст](./media/quickstart-device-streams-proxy-csharp/ssh-console-output.png "Вывод клиента SSH")

## <a name="clean-up-resources"></a>Очистка ресурсов

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Дополнительная информация

В рамках этого краткого руководства вы настроили Центр Интернета вещей, зарегистрировали устройство, развернули программу локальных прокси-серверов устройств и служб, чтобы установить потоковую передачу устройств через Центр Интернета вещей, а также использовали прокси-серверы для туннелирования трафика SSH.

Используйте приведенные ниже ссылки для получения дополнительных сведений о потоках устройства:

> [!div class="nextstepaction"]
> [IoT Hub Device Streams (preview) (Потоки устройств Центра Интернета вещей (предварительная версия)](./iot-hub-device-streams-overview.md)
