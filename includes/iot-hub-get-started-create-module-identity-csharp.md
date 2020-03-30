---
title: включить файл
description: включить файл
services: iot-hub
author: chrissie926
ms.service: iot-hub
ms.topic: include
ms.date: 08/07/2019
ms.author: menchi
ms.custom: include file
ms.openlocfilehash: a5c1ddd085ae65b9920d73f50f993f4646785a69
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "68883733"
---
## <a name="create-a-module-identity"></a>Создание удостоверения модуля

В этом разделе создается приложение консоли .NET, которое создает идентификацию устройства и удостоверение модуля в реестре идентификации в центре. Устройство или модуль не могут подключиться к концентратору, если у него нет входа в реестр идентификационных данных. Для получения дополнительной информации смотрите [раздел реестра идентификационных данных в руководстве разработчика IoT Hub.](../articles/iot-hub/iot-hub-devguide-identity-registry.md)

Запущенное консольное приложение создает уникальные идентификаторы и ключи одновременно для устройства и для модуля. Устройство и модуль используют эти значения для идентификации себя при отправке сообщений от устройства к облаку в Концентратор IoT. В идентификаторах учитывается регистр символов.

1. Открыть Visual Studio, и выберите **Создать новый проект**.

1. При **создании нового проекта**выберите **консольное приложение (.NET Framework).**

1. Выберите **Далее,** чтобы открыть **Настроить свой новый проект.** Присвойте проекту имя *CreateIdentities*, а решению — *IoTHubGetStarted*. Убедитесь, что указана версия платформы .NET 4.6.1 или более поздняя.

    ![Введите имя и рамки для решения Visual Studio](./media/iot-hub-get-started-create-module-identity-csharp/configure-createidentities-project.png)

1. В Visual Studio, открытые **инструменты** > **NuGet менеджер** > пакета**управления NuGet пакеты для решения**. Выберите вкладку **обзора**.

1. Поиск по **Microsoft.Azure.Devices**. Выберите его, а затем выберите **Установить.**

    ![Установка Azure IoT концентратор .NET службы SDK текущей версии](./media/iot-hub-get-started-create-module-identity-csharp/install-service-sdk.png)

1. Добавьте следующие инструкции `using` в начало файла **Program.cs** :

   ```csharp
   using Microsoft.Azure.Devices;
   using Microsoft.Azure.Devices.Common.Exceptions;
   ```

1. Добавьте следующие поля в класс **Program** . Замените значение заполнителя строкой подключения к Центру Интернета вещей, созданному в предыдущем разделе.

   ```csharp
   const string connectionString = "<replace_with_iothub_connection_string>";
   const string deviceID = "myFirstDevice";
   const string moduleID = "myFirstModule";
   ```

1. Добавьте следующий код в класс **Main**.

   ```csharp
   static void Main(string[] args)
   {
       AddDeviceAsync().Wait();
       AddModuleAsync().Wait();
   }
   ```

1. Добавьте следующие методы в класс **Program**.

    ```csharp
    private static async Task AddDeviceAsync()
    {
       RegistryManager registryManager = 
         RegistryManager.CreateFromConnectionString(connectionString);
       Device device;

       try
       {
           device = await registryManager.AddDeviceAsync(new Device(deviceID));
       }
       catch (DeviceAlreadyExistsException)
        {
            device = await registryManager.GetDeviceAsync(deviceID);
        }

        Console.WriteLine("Generated device key: {0}", 
          device.Authentication.SymmetricKey.PrimaryKey);
    }

    private static async Task AddModuleAsync()
    {
        RegistryManager registryManager = 
          RegistryManager.CreateFromConnectionString(connectionString);
        Module module;

        try
        {
            module = 
              await registryManager.AddModuleAsync(new Module(deviceID, moduleID));
        }
        catch (ModuleAlreadyExistsException)
        {
            module = await registryManager.GetModuleAsync(deviceID, moduleID);
        }

        Console.WriteLine("Generated module key: {0}", module.Authentication.SymmetricKey.PrimaryKey);
    }
    ```

    Метод `AddDeviceAsync` создает идентификацию устройства с ID **myFirstDevice.** Если идентификатор этого устройства уже существует в реестре идентификации, код просто извлекает существующую информацию об устройстве. Затем приложение отображает первичный ключ для данного удостоверения. Этот ключ используется в приложении для имитации устройства для подключения к концентратору.

    Метод `AddModuleAsync` создает идентификацию модуля с ID **myFirstModule** под **устройством myFirstDevice.** Если этот идентификатор модуля уже существует в реестре идентификации, код просто извлекает существующую информацию о модуле. Затем приложение отображает первичный ключ для данного удостоверения. Этот ключ используется в приложении для имитации модуля для подключения к концентратору.

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

1. Выполнить это приложение, и сделать к сведению ключ устройства и модуль ключа.

> [!NOTE]
> Реестр идентификационных данных IoT Hub хранит только идентификаторы устройств и модулей, чтобы обеспечить безопасный доступ к концентратору. Реестр удостоверений хранит идентификаторы устройств и ключи, используемые в качестве учетных данных безопасности. Реестр удостоверений также хранит флаги включения и отключения для каждого устройства. Эти флаги можно использовать для отключения доступа для этого устройства. Если приложению необходимо хранить другие метаданные, связанные с устройством, оно должно использовать магазин, в частности в конкретном приложении. У вас нет возможности включать и отключать удостоверения модулей. Дополнительные сведения см. в [руководстве разработчика для Центра Интернета вещей](../articles/iot-hub/iot-hub-devguide-identity-registry.md).
