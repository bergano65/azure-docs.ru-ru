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
ms.custom: mqtt
ms.openlocfilehash: 3cc74faa39b21b1ab275149db4f85de8f55fd07e
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81733475"
---
# <a name="get-started-with-device-management-net"></a>Начало работы с управлением устройствами (.NET)

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]

В этом учебнике описаны следующие процедуры.

* Создание экземпляра Центра Интернета вещей на портале Azure и удостоверения устройства в экземпляре Центра Интернета вещей.

* Создание приложения для имитации устройства с прямым методом, который позволяет выполнить перезагрузку устройства. Прямые методы вызываются из облака.

* Создание консольного приложения для .NET, вызывающего прямой метод перезагрузки в приложении имитации устройства с помощью Центра Интернета вещей.

По завершении работы с этим руководством у вас будет два консольных приложения .NET:

* **SimulateManagedDevice**. Это приложение подключается к вашему концентратору IoT с устройством, созданным ранее, получает метод перезагрузки, имитирует физическую перезагрузку и сообщает время для последней перезагрузки.

* **TriggerReboot**. Это приложение вызывает прямой метод в моделируемом приложении устройства, отображает ответ и отображает обновленные зарегистрированные свойства.

## <a name="prerequisites"></a>Предварительные требования

* приведенному.

* Активная учетная запись Azure. Если у вас нет учетной записи, вы можете создать [бесплатную учетную запись](https://azure.microsoft.com/pricing/free-trial/) всего за пару минут.

* Убедитесь, что в брандмауэре открыт порт 8883. Образец устройства в этой статье использует протокол МЗТТ, который общается по порту 8883. В некоторых корпоративных и академических сетях этот порт может быть заблокирован. Дополнительные сведения и способы устранения этой проблемы см. в разделе о [подключении к Центру Интернета вещей по протоколу MQTT](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="create-an-iot-hub"></a>Создание Центра Интернета вещей

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Регистрация нового устройства в центре Интернета вещей

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="get-the-iot-hub-connection-string"></a>Получите строку соединения концентратора IoT

[!INCLUDE [iot-hub-howto-device-management-shared-access-policy-text](../../includes/iot-hub-howto-device-management-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>Активация удаленной перезагрузки на устройстве с помощью прямого метода

В этом разделе вы создаете консоль .NET, используя c,, которая инициирует удаленную перезагрузку на устройстве с помощью прямого метода. Приложение использует запросы двойника устройства для определения времени последней перезагрузки этого устройства.

1. В Visual Studio выберите **Создать проект**.

1. При **создании нового проекта**найдите и выберите шаблон проекта Console App **(.NET Framework),** а затем выберите **следующий**.

1. При **настройке нового проекта**назовите проект *TriggerReboot*и выберите версию .NET Framework 4.5.1 или позже. Нажмите кнопку **создания**.

    ![Новый проект классического приложения Windows на языке Visual C#](./media/iot-hub-csharp-csharp-device-management-get-started/create-trigger-reboot-configure.png)

1. В **Solution Explorer**, правой кнопкой мыши проекта **TriggerReboot,** а затем выберите **Управление NuGet пакеты**.

1. Выберите **просмотр,** затем поиск и выбор **Microsoft.Azure.Devices**. Выберите **установку** для установки пакета **Microsoft.Azure.Devices.**

    ![Окно "Диспетчер пакетов NuGet"](./media/iot-hub-csharp-csharp-device-management-get-started/create-trigger-reboot-nuget-devices.png)

   В результате выполняется скачивание и установка пакета NuGet [SDK для служб Интернета вещей Azure](https://www.nuget.org/packages/Microsoft.Azure.Devices/) и его зависимостей, а также добавляется соответствующая ссылка.

1. Добавьте `using` следующие инструкции в верхней части файла **Program.cs:**

   ```csharp
   using Microsoft.Azure.Devices;
   using Microsoft.Azure.Devices.Shared;
   ```

1. Добавьте следующие поля в класс **Program** . Замените `{iot hub connection string}` значение заполнителя строкой подключения Концентратора, скопированной ранее в [строке подключения концентратора YouT.](#get-the-iot-hub-connection-string)

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

1. Наконец, добавьте следующие строки в **основной** метод:

   ```csharp
   registryManager = RegistryManager.CreateFromConnectionString(connString);
   StartReboot().Wait();
   QueryTwinRebootReported().Wait();
   Console.WriteLine("Press ENTER to exit.");
   Console.ReadLine();
   ```

1. Выберите решение **build** > **Build.**

> [!NOTE]
> В этом руководстве выполняется только один запрос свойств сообщаемого устройства. В рабочем коде мы рекомендуем выполнять опрос для поиска изменений в свойствах отчета.

## <a name="create-a-simulated-device-app"></a>Создание приложения виртуального устройства

В этом разделе выполняются следующие действия:

* Создайте консольное приложение .NET, отвечающее на прямой метод, вызываемый из облака.

* Запустите перезагрузку имитации устройства.

* Используйте сообщаемые свойства в запросах двойника устройства для определения устройств и времени последней перезагрузки.

Чтобы создать смоделированное приложение устройства, выполните следующие действия:

1. В Visual Studio, в уже созданном вами решении TriggerReboot выберите **Файл** > **Новый** > **проект.** При **создании нового проекта**найдите и выберите шаблон проекта Console App **(.NET Framework),** а затем выберите **следующий**.

1. В **настройке нового проекта,** назовите проект *SimulateManagedDevice,* а для **решения**выберите **Добавить в решение.** Нажмите кнопку **создания**.

    ![Назовите и добавьте свой проект в решение](./media/iot-hub-csharp-csharp-device-management-get-started/configure-device-app.png)

1. В Solution Explorer, правой кнопкой мыши новый проект **SimulateManagedDevice,** а затем выберите **Управление NuGet пакеты**.

1. Выберите **просмотр,** затем поиск и выберите **Microsoft.Azure.Devices.Client**. Выберите пункт **Установить**.

    ![Клиентское приложение в окне "Диспетчер пакетов NuGet"](./media/iot-hub-csharp-csharp-device-management-get-started/create-device-nuget-devices-client.png)

   Этот шаг загружает, устанавливает и добавляет ссылку на пакет [SDK NuGet-устройства Azure IoT](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/) и его зависимости.

1. Добавьте `using` следующие инструкции в верхней части файла **Program.cs:**

    ```csharp
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    ```

1. Добавьте следующие поля в класс **Program** . Замените `{device connection string}` значение заполнителя строкой подключения устройства, которую вы отметили ранее в [Регистрации нового устройства в концентраторе IoT.](#register-a-new-device-in-the-iot-hub)

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

       string result = @"{""result"":""Reboot started.""}";
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

1. В Solution Explorer нажмите правой кнопкой мыши, а затем выберите **Set StartUp Projects.**

1. Для**проекта запуска** **Common Properties** > , Выберите **одиночный проект запуска,** а затем выберите проект **SimulateManagedDevice.** Нажмите кнопку **ОК** , чтобы сохранить изменения.

1. Выберите решение **build** > **Build.**

> [!NOTE]
> Для простоты в этом руководстве не реализуются политики повтора. В производственном коде следует реализовать политики повторной попытки (например, экспоненциальное резервное копирование), как это предлагается при [обработке временных неисправностей.](/azure/architecture/best-practices/transient-faults)

## <a name="run-the-apps"></a>Запуск приложений

Теперь вы готовы к запуску приложений.

1. Для запуска приложения устройства .NET **SimulateManagedDevice**, в Solution Explorer, правой кнопкой мыши проекта **SimulateManagedDevice,** выберите **отбрасывайте**, а затем выберите **Запуск нового экземпляра**. Приложение должно начать прослушивание вызовов метода из вашего концентратора IoT.

1. После этого устройство подключено и ждет вызова метода, нажмите правой кнопкой мыши на проект **TriggerReboot,** выберите **Debug,** а затем выберите **Запуск нового экземпляра.**

   Должен вернуться ответ "Перезагрузка" в консоли **SimulatedManagedDevice**, а также сообщаемые свойства устройства, которые включают время последней перезагрузки, записанное в консоли **TriggerReboot**.

    ![Запуск приложений служб и устройств](./media/iot-hub-csharp-csharp-device-management-get-started/combinedrun.png)

[!INCLUDE [iot-hub-dm-followup](../../includes/iot-hub-dm-followup.md)]
