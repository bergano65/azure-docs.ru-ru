---
title: Начало работы w/ Azure IoT Концентратор модуль идентичности & модуль ный (.NET)
description: Сведения о создании удостоверения модуля и обновлении двойника модуля с помощью пакетов SDK для Центра Интернета для .NET.
author: chrissie926
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 08/07/2019
ms.author: menchi
ms.openlocfilehash: e728d0ef8f52927687d56bd1d4c64f03c53ef401
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73947683"
---
# <a name="get-started-with-iot-hub-module-identity-and-module-twin-net"></a>Начало работы с IoT концентратора модуль идентичности и модуля twin (.NET)

[!INCLUDE [iot-hub-selector-module-twin-getstarted](../../includes/iot-hub-selector-module-twin-getstarted.md)]

> [!NOTE]
> [Удостоверение и двойник модуля](iot-hub-devguide-module-twins.md) аналогичны удостоверению и двойнику устройства Центра Интернета вещей Azure, однако они предоставляют более высокую степень детализации. В то время как идентификация устройства Azure IoT Hub и двойная устройство позволяют задней части приложения настроить устройство и обеспечить видимость условий устройства, модуль идентичности и модуль двойной обеспечить эти возможности для отдельных компонентов устройства. На дееспособных устройствах с несколькими компонентами, такими как устройства на основе операционной системы или устройства прошивки, идентификаторы модулей и близнецы модулей позволяют проводить изолированную конфигурацию и условия для каждого компонента.

По завершении работы с этим руководством у вас будет два консольных приложения .NET:

* **Созданиеидентификационных данных**. Это приложение создает идентификацию устройства, идентификатор модуля и связанный с ним ключ безопасности для подключения клиентов устройства и модуля.

* **UpdateModuleTwinReportedProperties**. Это приложение отправляет обновленный модуль близнецов сообщил свойства в ваш концентратор IoT.

> [!NOTE]
> Сведения о пакетах SDK Azure IoT, которые можно использовать для создания приложений, которые будут работать на устройствах и в серверной части решения, см. в [этой статье](iot-hub-devguide-sdks.md).

## <a name="prerequisites"></a>Предварительные требования

* приведенному.

* Активная учетная запись Azure. Если у вас нет учетной записи, вы можете создать [бесплатную учетную запись](https://azure.microsoft.com/pricing/free-trial/) всего за пару минут.

## <a name="create-a-hub"></a>Создание концентратора.

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="get-the-iot-hub-connection-string"></a>Получите строку соединения концентратора IoT

[!INCLUDE [iot-hub-howto-module-twin-shared-access-policy-text](../../includes/iot-hub-howto-module-twin-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-registryrw-connection-string](../../includes/iot-hub-include-find-registryrw-connection-string.md)]

[!INCLUDE [iot-hub-get-started-create-module-identity-csharp](../../includes/iot-hub-get-started-create-module-identity-csharp.md)]

## <a name="update-the-module-twin-using-net-device-sdk"></a>Обновление двойника модуля с помощью пакета SDK для устройства .NET

В этом разделе вы создадите консольное приложение .NET на имитированном устройстве, которое обновляет сообщаемые свойства двойника модуля.

Перед тем, как начать, получите строку соединения модуля. Войдите на [портал Azure](https://portal.azure.com/). Перейдите к концентратору и выберите **IoT-устройства.** Найти **myFirstDevice**. Выберите **myFirstDevice,** чтобы открыть его, а затем выберите **myFirstModule,** чтобы открыть его. В **сведениях об идентификации модуля**скопируйте **строку Connection (основной ключ)** при необходимости в следующей процедуре.

   ![Сведения о модуле на портале Azure](./media/iot-hub-csharp-csharp-module-twin-getstarted/module-identity-detail.png)

1. В Visual Studio добавьте новый проект в свое решение, выбрав **Файл** > **Новый** > **проект**. При создании нового проекта выберите **консольное приложение (.NET Framework)** и выберите **Следующий**.

1. В качестве имени проекта укажите *UpdateModuleTwinReportedProperties*. Для **решения**выберите **Добавить в решение.** Убедитесь, что указана версия платформы .NET 4.6.1 или более поздняя.

    ![Создание проекта Visual Studio](./media/iot-hub-csharp-csharp-module-twin-getstarted/configure-update-twins-csharp1.png)

1. Выберите **Создать** для создания проекта.

1. В Visual Studio, открытые **инструменты** > **NuGet менеджер** > пакета**управления NuGet пакеты для решения**. Выберите вкладку **обзора**.

1. Поиск и выбор **Microsoft.Azure.Devices.Client**, а затем выберите **Install.**

    ![Установка Azure IoT концентратор .NET службы SDK текущей версии](./media/iot-hub-csharp-csharp-module-twin-getstarted/install-client-sdk.png)

1. Добавьте следующие инструкции `using` в начало файла **Program.cs** :

    ```csharp
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    using System.Threading.Tasks;
    using Newtonsoft.Json;
    ```

1. Добавьте следующие поля в класс **Program** . Замените значение заполнителя строкой подключения модуля.

    ```csharp
    private const string ModuleConnectionString = "<Your module connection string>";
    private static ModuleClient Client = null;
    static void ConnectionStatusChangeHandler(ConnectionStatus status, 
      ConnectionStatusChangeReason reason)
    {
        Console.WriteLine("Connection Status Changed to {0}; the reason is {1}", 
          status, reason);
    }
    ```

1. Добавьте метод **OnDesiredPropertyChanged** в класс **Program**:

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

1. Добавьте следующие строки в **основной** метод:

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

1. Дополнительно эти операторы можно добавить в **основной** метод для отправки события в Концентратор IoT из модуля. Поместите эти `try catch` строки под блоком.

    ```csharp
    Byte[] bytes = new Byte[2];
    bytes[0] = 0;
    bytes[1] = 1;
    var sendEventsTask = Client.SendEventAsync(new Message(bytes));
    sendEventsTask.Wait();
    Console.WriteLine("Event sent to IoT Hub.");
    ```

## <a name="run-the-apps"></a>Запуск приложений

Теперь вы можете запустить приложения.

1. В Visual Studio, в **Solution Explorer**, правой кнопкой мыши ваше решение, а затем выберите **Set StartUp проектов.**

1. Под **общими свойствами**, выберите **проект запуска.**

1. Выберите **несколько проектов запуска,** а затем выберите **Начало** в качестве действия для приложений, и **OK,** чтобы принять ваши изменения.

1. Нажмите **F5,** чтобы запустить приложения.

## <a name="next-steps"></a>Дальнейшие действия

Чтобы продолжить знакомство с Центром Интернета вещей и изучить другие сценарии Интернета вещей, см. следующие ресурсы:

* [How to get started with device management (Node)](iot-hub-node-node-device-management-get-started.md) (Начало работы с управлением устройствами (Node))

* [Краткое руководство. Развертывание первого модуля IoT Edge на устройстве под управлением 64-разрядной ОС Linux](../iot-edge/tutorial-simulate-device-linux.md)
