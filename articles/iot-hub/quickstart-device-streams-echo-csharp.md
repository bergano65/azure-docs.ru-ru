---
title: Руководство по потоковой передаче Центра Интернета вещей в C# (предварительная версия) | Документация Майкрософт
description: В этом кратком руководстве будут выполняться два примера приложений C#, взаимодействующих через поток устройств, установленный при помощи Центра Интернета вещей.
author: rezasherafat
manager: briz
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: quickstart
ms.custom: mvc
ms.date: 01/15/2019
ms.author: rezas
ms.openlocfilehash: edd3912b3674f3a80a81fd47ed490479f663852c
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/23/2019
ms.locfileid: "54830829"
---
# <a name="quickstart-communicate-to-device-applications-in-c-via-iot-hub-device-streams-preview"></a>Краткое руководство. Взаимодействие с приложениями устройств в C# с помощью потоков устройств Центра Интернета вещей (предварительная версия)

[!INCLUDE [iot-hub-quickstarts-3-selector](../../includes/iot-hub-quickstarts-3-selector.md)]

[Потоки устройств Центра Интернета вещей](./iot-hub-device-streams-overview.md) позволяют службам и приложениям устройств безопасным и подходящим методом обмениваться данными с брандмауэром. Краткое руководство включает две программы C#, использующие потоки устройств для обмена данными (вывод на экран).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Примеры приложений, запускаемые в рамках этого краткого руководства, написаны на языке C#. На компьютере, на котором ведется разработка, необходимо установить пакет SDK для .NET Core версии 2.1.0 или более поздней.

Пакет SDK для .NET Core, предназначенный для нескольких платформ, можно загрузить из [.NET](https://www.microsoft.com/net/download/all).

Текущую версию C# на компьютере, на котором ведется разработка, можно проверить, используя следующую команду:

```
dotnet --version
```

Скачайте пример проекта C# по ссылке https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip и извлеките ZIP-архив.


## <a name="create-an-iot-hub"></a>Создание Центра Интернета вещей

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub-device-streams.md)]

## <a name="register-a-device"></a>Регистрация устройства

Устройство должно быть зарегистрировано в Центре Интернета вещей, прежде чем оно сможет подключиться. В этом кратком руководстве для регистрации имитируемого устройства используется Azure Cloud Shell.

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

3. Понадобится также _строка подключения к службе_ из Центра Интернета вещей, чтобы включить приложение на стороне службы для подключения к Центру Интернета вещей и установить потоки устройств. Следующая команда получает это значение для Центра Интернета вещей:

   **YourIoTHubName**. Замените этот заполнитель именем вашего Центра Интернета вещей.

    ```azurecli-interactive
    az iot hub show-connection-string --policy-name service --hub-name YourIoTHubName
    ```

    Запомните или запишите возвращаемое значение, которое выглядит следующим образом:

   `"HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"`
    

## <a name="communicate-between-device-and-service-via-device-streams"></a>Обмен данными между устройством и службой через потоки устройств

### <a name="run-the-service-side-application"></a>Запуск приложения на стороне службы

Перейдите к `device-streams-echo/service` в распакованной папке проекта. Вам понадобятся следующие сведения.

| Имя параметра | Значение параметра |
|----------------|-----------------|
| `ServiceConnectionString` | Строка подключения к службе Центра Интернета вещей. |
| `DeviceId` | Идентификатор устройства, созданного ранее. |

Скомпилируйте и запустите код, как показано:

```
cd ./iot-hub/Quickstarts/device-streams-echo/service/

# Build the application
dotnet build

# Run the application
# In Linux/MacOS
dotnet run $ServiceConnectionString MyDevice

# In Windows
dotnet run %ServiceConnectionString% MyDevice
```

### <a name="run-the-device-side-application"></a>Запуск приложения на стороне устройства

Перейдите к каталогу `device-streams-echo/device` в распакованной папке проекта. Вам понадобятся следующие сведения.

| Имя параметра | Значение параметра |
|----------------|-----------------|
| `DeviceConnectionString` | Строка подключения созданного ранее устройства. |

Скомпилируйте и запустите код, как показано:

```
cd ./iot-hub/Quickstarts/device-streams-echo/device/

# Build the application
dotnet build

# Run the application
# In Linux/MacOS
dotnet run $DeviceConnectionString

# In Windows
dotnet run %DeviceConnectionString%
```

После последнего шага программа на стороне службы инициирует поток к устройству и после установления соединения отправит службе через поток строковый буфер. В этом примере программа на стороне службы просто возвращает те же данные устройству, демонстрируя успешную двустороннюю связь между двумя приложениями. Смотрите рисунок, приведенный ниже.

Выходные данные консоли на стороне устройства: ![замещающий текст](./media/quickstart-device-streams-echo-csharp/device-console-output.png "Выходные данные консоли на стороне устройства")


Выходные данные консоли на стороне службы: ![замещающий текст](./media/quickstart-device-streams-echo-csharp/service-console-output.png "Выходные данные консоли на стороне службы")



Трафик, передаваемый по потоку, будет туннелироваться через Центр Интернета вещей, а не напрямую. Это обеспечивает [такие преимущества](./iot-hub-device-streams-overview.md#benefits).


## <a name="clean-up-resources"></a>Очистка ресурсов

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]


## <a name="next-steps"></a>Дополнительная информация

В этом кратком руководстве вы настроили Центр Интернета вещей, зарегистрировали устройство, установили поток устройств между приложением C# на стороне устройства и службы и использовали поток для обмена данными между приложениями.

Используйте приведенные ниже ссылки для получения дополнительных сведений о потоках устройства:

> [!div class="nextstepaction"]
> [IoT Hub Device Streams (preview)](./iot-hub-device-streams-overview.md) (Потоки устройств (предварительная версия))
