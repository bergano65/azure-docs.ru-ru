---
title: Взаимодействие с приложением устройства в C с помощью потоков устройств Центра Интернета вещей (предварительная версия) | Документация Майкрософт
description: С помощью этого краткого руководства вы запустите приложение C на стороне устройства, которое обменивается данными с устройством Интернета вещей через поток устройств.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/14/2019
ms.author: robinsh
ms.openlocfilehash: 4b6f987c68f9fe3ef95c82017b7d8be1d83083ea
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67446128"
---
# <a name="quickstart-communicate-to-a-device-application-in-c-via-iot-hub-device-streams-preview"></a>Краткое руководство. Взаимодействие с приложением устройства в C с помощью потоков устройств Центра Интернета вещей (предварительная версия)

[!INCLUDE [iot-hub-quickstarts-3-selector](../../includes/iot-hub-quickstarts-3-selector.md)]

Центр Интернета вещей Azure поддерживает потоки устройств, которые сейчас доступны в режиме [предварительной версии](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[Потоки устройств Центра Интернета вещей](iot-hub-device-streams-overview.md) позволяют службам и приложениям устройств безопасным и подходящим методом обмениваться данными с брандмауэром. На этапе предварительной версии пакет SDK для C поддерживает только потоки устройств на стороне устройства. В результате это краткое руководство охватывает инструкции по запуску приложения только на стороне устройства. Чтобы запустить сопутствующее приложение со стороны службы, см. следующие статьи.
 
   * [Обмен данными с приложениями устройств в C# с помощью потоков устройств Центра Интернета вещей](./quickstart-device-streams-echo-csharp.md)
   * [Взаимодействие с приложением устройства в Node.js с помощью потоков устройств Центра Интернета вещей (предварительная версия)](./quickstart-device-streams-echo-nodejs.md)

Приложение C на стороне устройства, используемое в этом кратком руководстве, имеет следующие функции:

* установка потока устройств к устройству Интернета вещей;
* получение данных, отправленных приложением со стороны службы, и отправка их обратно.

Код демонстрирует процесс инициализации потока устройств, а также его использование для отправки и получения данных.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

* Предварительная версия потоков устройств сейчас поддерживается только в центрах Интернета вещей, созданных в следующих регионах:

  * Центральный регион США
  * Центральная часть США (EUAP)

* Установите [Visual Studio 2017](https://www.visualstudio.com/vs/) с включенной рабочей нагрузкой [Разработка классических приложений на C++](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/).

* Установите последнюю версию [Git](https://git-scm.com/download/).

* Выполните следующую команду, чтобы добавить расширение Интернета вещей Azure для Azure CLI в экземпляр Cloud Shell. Расширение Интернета вещей добавляет в Azure CLI специальные команды Центра Интернета вещей, IoT Edge и Службы подготовки устройств к добавлению в центр Интернета вещей (DPS).

   ```azurecli-interactive
   az extension add --name azure-cli-iot-ext
   ```

## <a name="prepare-the-development-environment"></a>Подготовка среды разработки

При работе с этим кратким руководством используйте [пакет SDK для устройств Azure IoT для C](iot-hub-device-sdk-c-intro.md). Вы подготовите среду разработки, которая используется для клонирования и сборки [пакета SDK Azure IoT для C](https://github.com/Azure/azure-iot-sdk-c) с сайта GitHub. Пакет SDK на сайте GitHub содержит пример кода, используемый в этом кратком руководстве.

1. Скачайте [систему сборки CMake](https://cmake.org/download/).

    Перед установкой CMake важно установить на компьютер необходимые компоненты Visual Studio (Visual Studio и рабочую нагрузку *Разработка классических приложений на C++* ). После установки компонентов и проверки загрузки вы можете установить систему сборки CMake.

2. Откройте командную строку или оболочку Git Bash. Выполните следующую команду для клонирования репозитория GitHub [пакета SDK для устройства C Интернета вещей Azure](https://github.com/Azure/azure-iot-sdk-c):

    ```
    git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive -b public-preview
    ```

    Это должно занять несколько минут.

3. Создайте подкаталог *cmake* в корневом каталоге репозитория Git, как показано в следующей команде, и перейдите в эту папку.

    ```
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

4. Выполните приведенную ниже команду из каталога *cmake*, чтобы создать версию пакета SDK для используемой клиентской платформы разработки.

   * В Linux:

      ```bash
      cmake ..
      make -j
      ```

   * В Windows выполните приведенные ниже команды в Командной строке разработчика Visual Studio 2015 или 2017. Эта команда также создает решение Visual Studio для имитированного устройства в каталоге *cmake*.

      ```cmd
      rem For VS2015
      cmake .. -G "Visual Studio 14 2015"

      rem Or for VS2017
      cmake .. -G "Visual Studio 15 2017"

      rem Then build the project
      cmake --build . -- /m /p:Configuration=Release
      ```

## <a name="create-an-iot-hub"></a>Создание Центра Интернета вещей

[!INCLUDE [iot-hub-include-create-hub-device-streams](../../includes/iot-hub-include-create-hub-device-streams.md)]

## <a name="register-a-device"></a>Регистрация устройства

Устройство должно быть зарегистрировано в центре Интернета вещей, прежде чем оно сможет подключиться. В этом разделе описано, как зарегистрировать имитированное устройство с помощью [расширения Интернета вещей](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot?view=azure-cli-latest) для Azure Cloud Shell.

1. Чтобы создать удостоверение устройства, выполните приведенные ниже команды в Cloud Shell.

   > [!NOTE]
   > * Замените заполнитель *YourIoTHubName* именем созданного центра Интернета вещей.
   > * Используйте имя *MyDevice*, как показано в примере. Это имя, присвоенное зарегистрированному устройству. Если вы выбрали другое имя для устройства, используйте его при работе с этой статьей и обновите имя устройства в примерах приложений перед их запуском.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyDevice
    ```

2. Выполните следующие команды в Cloud Shell, чтобы получить *строку подключения* зарегистрированного устройства.

   > [!NOTE]
   > Замените заполнитель *YourIoTHubName* именем созданного центра Интернета вещей.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name YourIoTHubName --device-id MyDevice --output table
    ```

    Запишите строку подключения устройства для последующего использования в этом кратком руководстве. Это должно выглядеть следующим образом:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyDevice;SharedAccessKey={YourSharedAccessKey}`

## <a name="communicate-between-the-device-and-the-service-via-device-streams"></a>Обмен данными между устройством и службой через потоки устройств

В рамках этого раздела вы запустите приложения на стороне устройства и на стороне службы, а также настроите обмен данными между двумя этими приложениями.

### <a name="run-the-device-side-application"></a>Запуск приложения на стороне устройства

Для запуска приложения на стороне устройства выполните следующие действия.

1. Предоставьте учетные данные устройства, отредактировав исходный файл *iothub_client_c2d_streaming_sample.c* в папке *iothub_client/samples/iothub_client_c2d_streaming_sample*, и укажите строку подключения устройства.

   ```C
   /* Paste in your iothub connection string  */
   static const char* connectionString = "[device connection string]";
   ```

2. Скомпилируйте код, как показано:

   ```bash
   # In Linux
   # Go to the sample's folder cmake/iothub_client/samples/iothub_client_c2d_streaming_sample
   make -j
   ```

   ```cmd
   rem In Windows
   rem Go to the cmake folder at the root of repo
   cmake --build . -- /m /p:Configuration=Release
   ```

3. Запустите скомпилированную программу:

   ```bash
   # In Linux
   # Go to the sample's folder cmake/iothub_client/samples/iothub_client_c2d_streaming_sample
   ./iothub_client_c2d_streaming_sample
   ```

   ```cmd
   rem In Windows
   rem Go to the sample's release folder cmake\iothub_client\samples\iothub_client_c2d_streaming_sample\Release
   iothub_client_c2d_streaming_sample.exe
   ```

### <a name="run-the-service-side-application"></a>Запуск приложения на стороне службы

Как упоминалось ранее, пакет SDK для C центра Интернета вещей поддерживает потоки устройств только на стороне устройства. Чтобы скомпилировать и запустить приложение на стороне службы, выполните инструкции, приведенные в одном из следующих кратких руководств.

* [Взаимодействие с приложениями устройств в C# с помощью потоков устройств Центра Интернета вещей (предварительная версия)](./quickstart-device-streams-echo-csharp.md)
* [Взаимодействие с приложением устройства в Node.js с помощью потоков устройств Центра Интернета вещей (предварительная версия)](./quickstart-device-streams-echo-nodejs.md)

## <a name="clean-up-resources"></a>Очистка ресурсов

[!INCLUDE [iot-hub-quickstarts-clean-up-resources-device-streams](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Дополнительная информация

В этом кратком руководстве вы настроили центр Интернета вещей, зарегистрировали устройство, установили поток устройств между приложением C на стороне устройства и еще одним приложением на стороне службы, а затем использовали поток для обмена данными между приложениями.

Дополнительные сведения о потоках устройств см. в следующей статье:

> [!div class="nextstepaction"]
> [IoT Hub Device Streams (preview)](./iot-hub-device-streams-overview.md) (Потоки устройств (предварительная версия))
