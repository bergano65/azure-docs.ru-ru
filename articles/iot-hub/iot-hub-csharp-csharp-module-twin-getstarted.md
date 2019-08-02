---
title: Приступая к работе с удостоверением и двойником модуля Центра Интернета вещей Azure (.NET) | Документация Майкрософт
description: Сведения о создании удостоверения модуля и обновлении двойника модуля с помощью пакетов SDK для Центра Интернета для .NET.
author: chrissie926
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 04/26/2018
ms.author: menchi
ms.openlocfilehash: ea42a459365848386082470deb5120e09cb92421
ms.sourcegitcommit: fecb6bae3f29633c222f0b2680475f8f7d7a8885
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/30/2019
ms.locfileid: "68668153"
---
# <a name="get-started-with-iot-hub-module-identity-and-module-twin-net"></a>Приступая к работе с удостоверениями модулей центра Интернета вещей и модулем двойника (.NET)

[!INCLUDE [iot-hub-selector-module-twin-getstarted](../../includes/iot-hub-selector-module-twin-getstarted.md)]

> [!NOTE]
> [Удостоверение и двойник модуля](iot-hub-devguide-module-twins.md) аналогичны удостоверению и двойнику устройства Центра Интернета вещей Azure, однако они предоставляют более высокую степень детализации. В то время как удостоверение и двойник устройства Центра Интернета вещей Azure позволяют серверному приложению настраивать устройство и отображать состояние устройства, удостоверение и двойник модуля предоставляют эти возможности для отдельных компонентов устройства. Благодаря этому можно изолировать конфигурацию и условия для каждого компонента на совместимых устройствах с несколькими компонентами, например устройствах на основе операционной системы или устройствах со встроенным ПО.

По завершении работы с этим руководством у вас будет два консольных приложения .NET:

* **CreateIdentities** — создает удостоверение устройства и модуля, а также соответствующий ключ безопасности для подключения к клиентам устройства и модуля.

* **UpdateModuleTwinReportedProperties** — отправляет обновленные сообщаемые свойства двойника модуля в Центр Интернета вещей.

> [!NOTE]
> Сведения о пакетах SDK Azure IoT, которые можно использовать для создания приложений, которые будут работать на устройствах и в серверной части решения, см. в [этой статье](iot-hub-devguide-sdks.md).

Для работы с этим учебником требуется:

* приведенному.

* Активная учетная запись Azure. Если ее нет, можно создать [бесплатную учетную запись](https://azure.microsoft.com/pricing/free-trial/) всего за несколько минут.

## <a name="create-an-iot-hub"></a>Создание Центра Интернета вещей

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="get-the-iot-hub-connection-string"></a>Получение строки подключения для центра Интернета вещей

[!INCLUDE [iot-hub-howto-module-twin-shared-access-policy-text](../../includes/iot-hub-howto-module-twin-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-registryrw-connection-string](../../includes/iot-hub-include-find-registryrw-connection-string.md)]

[!INCLUDE [iot-hub-get-started-create-module-identity-csharp](../../includes/iot-hub-get-started-create-module-identity-csharp.md)]

## <a name="update-the-module-twin-using-net-device-sdk"></a>Обновление двойника модуля с помощью пакета SDK для устройства .NET

В этом разделе вы создадите консольное приложение .NET на имитированном устройстве, которое обновляет сообщаемые свойства двойника модуля.

1. **Создайте проект Visual Studio**. В Visual Studio добавьте в имеющееся решение проект классического приложения Windows на языке Visual C# с помощью шаблона проекта **консольного приложения (.NET Framework)** . Убедитесь, что указана версия платформы .NET 4.6.1 или более поздняя. В качестве имени проекта укажите **UpdateModuleTwinReportedProperties**.

    ![Создание проекта Visual Studio](./media/iot-hub-csharp-csharp-module-twin-getstarted/update-twins-csharp1.png)

2. **Установите последний пакет SDK для устройств Центра Интернета вещей Azure для .NET**. Удостоверения модулей и двойники модулей предоставляются в общедоступной предварительной версии. Они доступны только в пакетах SDK предварительного выпуска для Центра Интернета вещей. В Visual Studio выберите для решения элементы "Сервис > Диспетчер пакетов NuGet > Управление пакетами NuGet". Найдите Microsoft.Azure.Devices.Client. Обязательно установите флажок "Включить предварительные выпуски". Выберите последнюю версию и установите ее. Теперь у вас есть доступ ко всем возможностям этого модуля. 

    ![Установка пакета SDK V1.16.0-preview-005 .NET для Центра Интернета вещей Azure](./media/iot-hub-csharp-csharp-module-twin-getstarted/install-sdk.png)

3. **Получение строки подключения модуля** — теперь, если вы входите в [портал Azure](https://portal.azure.com/). Перейдите к Центру Интернета вещей и щелкните "Устройства IoT". Найдите устройство myFirstDevice, откройте его и вы увидите, что модуль myFirstModule успешно создан. Скопируйте строку подключения модуля. Она понадобится на следующем шаге.

    ![Сведения о модуле на портале Azure](./media/iot-hub-csharp-csharp-module-twin-getstarted/module-detail.png)

4. **Создайте консольное приложение UpdateModuleTwinReportedProperties**.

    Добавьте следующие инструкции `using` в начало файла **Program.cs** :

    ```csharp
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    using System.Threading.Tasks;
    using Newtonsoft.Json;
    ```

    Добавьте следующие поля в класс **Program** . Замените значение заполнителя строкой подключения модуля.

    ```csharp
    private const string ModuleConnectionString = 
      "<Your module connection string>";
    private static ModuleClient Client = null;
    static void ConnectionStatusChangeHandler(ConnectionStatus status, 
      ConnectionStatusChangeReason reason)
    {
        Console.WriteLine("Connection Status Changed to {0}; the reason is {1}", 
          status, reason);
    }
    ```

    Добавьте метод **OnDesiredPropertyChanged** в класс **Program**:

    ```csharp
    private static async Task OnDesiredPropertyChanged(TwinCollection desiredProperties, 
      object userContext)
        {
            Console.WriteLine("desired property change:");
            Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));
            Console.WriteLine("Sending current time as reported property");
            TwinCollection reportedProperties = new TwinCollection
            {
                ["DateTimeLastDesiredPropertyChangeReceived"] = DateTime.Now
            };

            await Client.UpdateReportedPropertiesAsync(reportedProperties).ConfigureAwait(false);
        }
    ```

    Наконец, добавьте следующие строки в метод **Main** :

    ```csharp
    static void Main(string[] args)
    {
        Microsoft.Azure.Devices.Client.TransportType transport = 
          Microsoft.Azure.Devices.Client.TransportType.Amqp;

        try
        {
            Client = 
              ModuleClient.CreateFromConnectionString(ModuleConnectionString, transport);
            Client.SetConnectionStatusChangesHandler(ConnectionStatusChangeHandler);
            Client.SetDesiredPropertyUpdateCallbackAsync(OnDesiredPropertyChanged, null).Wait();

            Console.WriteLine("Retrieving twin");
            var twinTask = Client.GetTwinAsync();
            twinTask.Wait();
            var twin = twinTask.Result;
            Console.WriteLine(JsonConvert.SerializeObject(twin.Properties)); 

            Console.WriteLine("Sending app start time as reported property");
            TwinCollection reportedProperties = new TwinCollection();
            reportedProperties["DateTimeLastAppLaunch"] = DateTime.Now;

            Client.UpdateReportedPropertiesAsync(reportedProperties);
        }
        catch (AggregateException ex)
        {
            Console.WriteLine("Error in sample: {0}", ex);
        }

        Console.WriteLine("Waiting for Events.  Press enter to exit...");
        Console.ReadLine();
        Client.CloseAsync().Wait();
    }
    ```

    В этом примере кода показано, как извлечь двойник модуля и обновить сообщаемые свойства с помощью протокола AMQP. В общедоступной предварительной версии для операций двойника модуля поддерживается только протокол AMQP.

5. Помимо упомянутого метода **Main** можно добавить приведенный ниже блок кода для отправки событий из модуля в Центр Интернета вещей:

    ```csharp
    Byte[] bytes = new Byte[2];
    bytes[0] = 0;
    bytes[1] = 1;
    var sendEventsTask = Client.SendEventAsync(new Message(bytes));
    sendEventsTask.Wait();
    Console.WriteLine("Event sent to IoT Hub.");
    ```

## <a name="run-the-apps"></a>Запуск приложений

Теперь все готово к запуску приложений. В обозревателе решений Visual Studio щелкните правой кнопкой мыши решение и выберите пункт **Назначить запускаемые проекты**. Выберите **Несколько запускаемых проектов**, а затем — **Запустить** в качестве действия для консольного приложения. Нажмите клавишу F5, чтобы запустить приложение.

## <a name="next-steps"></a>Следующие шаги

Чтобы продолжить знакомство с Центром Интернета вещей и изучить другие сценарии Интернета вещей, см. следующие ресурсы:

* [Начало работы с управлением устройствами (Node)](iot-hub-node-node-device-management-get-started.md)
* [Краткое руководство. Развертывание первого модуля IoT Edge на устройстве под управлением 64-разрядной ОС Linux](../iot-edge/tutorial-simulate-device-linux.md)
