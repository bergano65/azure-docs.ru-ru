---
title: Приступая к работе с функцией управления устройствами Центра Интернета вещей Azure (.NET и .NET) | Документация Майкрософт
description: Использование функции управления устройствами в Центре Интернета вещей Azure для начала удаленной перезагрузки устройства. Пакет SDK для устройств Azure IoT для .NET позволяет реализовать приложение имитации устройства, которое содержит прямой метод. Пакет SDK для служб Azure IoT для .NET нужен для того, чтобы реализовать приложение-службу, вызывающее этот прямой метод.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: robinsh
ms.openlocfilehash: 0ab714efc3e9eb0de9d6753854031110e09fe06b
ms.sourcegitcommit: aaa82f3797d548c324f375b5aad5d54cb03c7288
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/29/2019
ms.locfileid: "70147833"
---
# <a name="get-started-with-device-management-net"></a>Начало работы с управлением устройствами (.NET)

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]

В этом учебнике описаны следующие процедуры.

* Создание экземпляра Центра Интернета вещей на портале Azure и удостоверения устройства в экземпляре Центра Интернета вещей.

* Создание приложения для имитации устройства с прямым методом, который позволяет выполнить перезагрузку устройства. Прямые методы вызываются из облака.

* Создание консольного приложения для .NET, вызывающего прямой метод перезагрузки в приложении имитации устройства с помощью Центра Интернета вещей.

По завершении работы с этим руководством у вас будет два консольных приложения .NET:

* **SimulateManagedDevice**. Это приложение подключается к центру Интернета вещей с помощью созданного ранее удостоверения устройства, получает прямой метод перезагрузки, имитирует физическую перезагрузку и сообщает о времени последней перезагрузки.

* **TriggerReboot**. Это приложение вызывает прямой метод в приложении имитации устройства, отображает ответ и отображает обновленные сообщаемые свойства.

## <a name="prerequisites"></a>Предварительные требования

* приведенному.

* Активная учетная запись Azure. Если ее нет, можно создать [бесплатную учетную запись](https://azure.microsoft.com/pricing/free-trial/) всего за несколько минут.

## <a name="create-an-iot-hub"></a>Создание Центра Интернета вещей

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Регистрация нового устройства в центре Интернета вещей

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="get-the-iot-hub-connection-string"></a>Получение строки подключения для центра Интернета вещей

[!INCLUDE [iot-hub-howto-device-management-shared-access-policy-text](../../includes/iot-hub-howto-device-management-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>Активация удаленной перезагрузки на устройстве с помощью прямого метода

В этом разделе вы создадите консольное приложение .NET с помощью C#, которое инициирует удаленную перезагрузку на устройстве с помощью прямого метода. Приложение использует запросы двойника устройства для определения времени последней перезагрузки этого устройства.

1. В Visual Studio выберите **создать новый проект**.

1. В окне **Создание нового проекта**найдите и выберите шаблон проекта **консольное приложение (.NET Framework)** , а затем нажмите кнопку **Далее**.

1. В окне **Настройка нового проекта**назовите проект *TriggerReboot*и выберите .NET Framework версии 4.5.1 или более поздней. Нажмите кнопку **Создать**.

    ![Новый проект классического приложения Windows на языке Visual C#](./media/iot-hub-csharp-csharp-device-management-get-started/create-trigger-reboot-configure.png)

1. В **Обозреватель решений**щелкните правой кнопкой мыши проект **TriggerReboot** , а затем выберите пункт **Управление пакетами NuGet**.

1. Нажмите кнопку **Обзор**, найдите и выберите **Microsoft. Azure. Devices**. Нажмите кнопку **установить** , чтобы установить пакет **Microsoft. Azure. Devices** .

    ![Окно "Диспетчер пакетов NuGet"](./media/iot-hub-csharp-csharp-device-management-get-started/create-trigger-reboot-nuget-devices.png)

   В результате выполняется скачивание и установка пакета NuGet [SDK для служб Azure IoT](https://www.nuget.org/packages/Microsoft.Azure.Devices/) и его зависимостей, а также добавляется соответствующая ссылка.

1. Добавьте следующие инструкции `using` в начало файла **Program.cs** :

   ```csharp
   using Microsoft.Azure.Devices;
   using Microsoft.Azure.Devices.Shared;
   ```

1. Добавьте следующие поля в класс **Program** . Замените значение заполнителя строкой подключения центра Интернета вещей, скопированным ранее в поле [Получение строки подключения для центра Интернета вещей.](#get-the-iot-hub-connection-string) `{iot hub connection string}`

   ```csharp
   static RegistryManager registryManager;
   static string connString = "{iot hub connection string}";
   static ServiceClient client;
   static string targetDevice = "myDeviceId";
   ```

1. Добавьте следующий метод в класс **Program**.  Этот код получает двойник устройства для перезагрузки устройства и выводит сообщаемые свойства.

   ```csharp
   public static async Task QueryTwinRebootReported()
   {
       Twin twin = await registryManager.GetTwinAsync(targetDevice);
       Console.WriteLine(twin.Properties.Reported.ToJson());
   }
   ```

1. Добавьте следующий метод в класс **Program**.  Этот код инициирует удаленную перезагрузку на устройстве с помощью прямого метода.

   ```csharp
   public static async Task StartReboot()
   {
       client = ServiceClient.CreateFromConnectionString(connString);
       CloudToDeviceMethod method = new CloudToDeviceMethod("reboot");
       method.ResponseTimeout = TimeSpan.FromSeconds(30);

       CloudToDeviceMethodResult result = await 
         client.InvokeDeviceMethodAsync(targetDevice, method);

       Console.WriteLine("Invoked firmware update on device.");
   }
   ```

1. Наконец, добавьте следующие строки в метод **Main** :

   ```csharp
   registryManager = RegistryManager.CreateFromConnectionString(connString);
   StartReboot().Wait();
   QueryTwinRebootReported().Wait();
   Console.WriteLine("Press ENTER to exit.");
   Console.ReadLine();
   ```

1. Выберите **Сборка** > сборка**решения**.

> [!NOTE]
> В этом руководстве выполняется только один запрос свойств сообщаемого устройства. В рабочем коде мы рекомендуем выполнять опрос для поиска изменений в свойствах отчета.

## <a name="create-a-simulated-device-app"></a>Создание приложения виртуального устройства

В этом разделе выполняются следующие действия:

* Создайте консольное приложение .NET, отвечающее на прямой метод, вызываемый из облака.

* Запустите перезагрузку имитации устройства.

* Используйте сообщаемые свойства в запросах двойника устройства для определения устройств и времени последней перезагрузки.

Чтобы создать приложение для имитации устройства, выполните следующие действия.

1. В уже созданном решении TriggerReboot в Visual Studio выберите **файл** > **создать** > **проект**. В окне **Создание нового проекта**найдите и выберите шаблон проекта **консольное приложение (.NET Framework)** , а затем нажмите кнопку **Далее**.

1. В окне **Настройка нового проекта**назовите проект *SimulateManagedDevice*, а для **решения**выберите **Добавить в решение**. Нажмите кнопку **Создать**.

    ![Присвойте проекту имя и добавьте его в решение.](./media/iot-hub-csharp-csharp-device-management-get-started/configure-device-app.png)

1. В обозреватель решений щелкните правой кнопкой мыши новый проект **SimulateManagedDevice** , а затем выберите **Управление пакетами NuGet**.

1. Нажмите кнопку **Обзор**, найдите и выберите **Microsoft. Azure. Devices. Client**. Щелкните **Установить**.

    ![Клиентское приложение в окне "Диспетчер пакетов NuGet"](./media/iot-hub-csharp-csharp-device-management-get-started/create-device-nuget-devices-client.png)

   На этом шаге выполняется скачивание, установка и Добавление ссылки на пакет [SDK для устройств Azure IOT](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/) и его зависимости.

1. Добавьте следующие инструкции `using` в начало файла **Program.cs** :

    ```csharp
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    ```

1. Добавьте следующие поля в класс **Program** . Замените значение заполнителя строкой подключения устройства, записанной ранее в поле [Регистрация нового устройства в центре Интернета вещей.](#register-a-new-device-in-the-iot-hub) `{device connection string}`

    ```csharp
    static string DeviceConnectionString = "{device connection string}";
    static DeviceClient Client = null;
    ```

1. Добавьте следующий код, чтобы реализовать прямой метод на устройстве:

   ```csharp
   static Task<MethodResponse> onReboot(MethodRequest methodRequest, object userContext)
   {
       // In a production device, you would trigger a reboot 
       //   scheduled to start after this method returns.
       // For this sample, we simulate the reboot by writing to the console
       //   and updating the reported properties.
       try
       {
           Console.WriteLine("Rebooting!");

           // Update device twin with reboot time. 
           TwinCollection reportedProperties, reboot, lastReboot;
           lastReboot = new TwinCollection();
           reboot = new TwinCollection();
           reportedProperties = new TwinCollection();
           lastReboot["lastReboot"] = DateTime.Now;
           reboot["reboot"] = lastReboot;
           reportedProperties["iothubDM"] = reboot;
           Client.UpdateReportedPropertiesAsync(reportedProperties).Wait();
       }
       catch (Exception ex)
       {
           Console.WriteLine();
           Console.WriteLine("Error in sample: {0}", ex.Message);
       }

       string result = "'Reboot started.'";
       return Task.FromResult(new MethodResponse(Encoding.UTF8.GetBytes(result), 200));
   }
   ```

1. И наконец, добавьте этот код в метод **Main**, чтобы открыть подключение к Центру Интернета вещей и инициализировать прослушиватель метода:

   ```csharp
   try
   {
       Console.WriteLine("Connecting to hub");
       Client = DeviceClient.CreateFromConnectionString(DeviceConnectionString, 
         TransportType.Mqtt);

       // setup callback for "reboot" method
       Client.SetMethodHandlerAsync("reboot", onReboot, null).Wait();
       Console.WriteLine("Waiting for reboot method\n Press enter to exit.");
       Console.ReadLine();

       Console.WriteLine("Exiting...");

       // as a good practice, remove the "reboot" handler
       Client.SetMethodHandlerAsync("reboot", null, null).Wait();
       Client.CloseAsync().Wait();
   }
   catch (Exception ex)
   {
       Console.WriteLine();
       Console.WriteLine("Error in sample: {0}", ex.Message);
   }
   ```

1. В обозреватель решений щелкните решение правой кнопкой мыши и выберите команду **задать запускаемые проекты**.

1. В поле "**запускаемый проект**" **общих свойств** > выберите **один запускаемый проект**, а затем выберите проект **SimulateManagedDevice** . Нажмите кнопку **ОК** , чтобы сохранить изменения.

1. Выберите **Сборка** > сборка**решения**.

> [!NOTE]
> Для простоты в этом руководстве не реализуются политики повтора. В рабочем коде следует реализовать политики повтора (например, экспоненциальную отработку отказа), как это было предложено при [обработке временных сбоев](/azure/architecture/best-practices/transient-faults).

## <a name="run-the-apps"></a>Запуск приложений

Теперь все готово для запуска приложений.

1. Чтобы запустить приложение **SimulateManagedDevice**для устройства .NET, в Обозреватель решений щелкните правой кнопкой мыши проект **SimulateManagedDevice** , выберите пункт **Отладка**, а затем выберите пункт **запустить новый экземпляр**. Приложение должно начать прослушивание вызовов методов из центра Интернета вещей.

1. После подключения устройства и ожидания вызова метода щелкните правой кнопкой мыши проект **TriggerReboot** , выберите пункт Отладка, а затем выберитепункт **запустить новый экземпляр**.

   Должен вернуться ответ "Перезагрузка" в консоли **SimulatedManagedDevice**, а также сообщаемые свойства устройства, которые включают время последней перезагрузки, записанное в консоли **TriggerReboot**.

    ![Запуск приложений служб и устройств](./media/iot-hub-csharp-csharp-device-management-get-started/combinedrun.png)

[!INCLUDE [iot-hub-dm-followup](../../includes/iot-hub-dm-followup.md)]
