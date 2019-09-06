---
title: Начало работы с двойниками устройств Центра Интернета вещей Azure (.NET/.NET) | Документы Майкрософт
description: Добавление тегов и последующее использование запроса Центра Интернета вещей с помощью двойников устройств Центра Интернета вещей. Используйте пакет SDK для устройств Azure IoT для .NET, чтобы реализовать приложение имитации устройства, и пакет SDK для служб Azure IoT для .NET, чтобы реализовать приложение-службу, которое добавит теги и выполнит запрос к Центру Интернета вещей.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 08/26/2019
ms.author: robinsh
ms.openlocfilehash: c07b110f0d4c31713ab432b5b5e337f3b69dfc55
ms.sourcegitcommit: aaa82f3797d548c324f375b5aad5d54cb03c7288
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/29/2019
ms.locfileid: "70147712"
---
# <a name="get-started-with-device-twins-net"></a>Начало работы с двойниковами устройств (.NET)

[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

В этом руководстве вы создадите следующие консольные приложения .NET:

* **CreateDeviceIdentity**. Это приложение создает удостоверение устройства и связанный с ним ключ безопасности для подключения приложения имитации устройства.

* **AddTagsAndQuery**. Это серверное приложение добавляет теги и запрашивает двойникови устройств.

* **ReportConnectivity**. Это приложение устройства имитирует устройство, которое подключается к центру Интернета вещей, используя удостоверение устройства, созданное ранее, и сообщает о его состоянии подключения.

> [!NOTE]
> Статья [Общие сведения о пакетах SDK для Azure IoT и их использование](iot-hub-devguide-sdks.md) содержит сведения о разных пакетах SDK для Интернета вещей Azure, с помощью которых можно создать приложения для устройств и внутренние приложения.
>

## <a name="prerequisites"></a>Предварительные требования

* приведенному.

* Активная учетная запись Azure. Если ее нет, можно создать [бесплатную учетную запись](https://azure.microsoft.com/pricing/free-trial/) всего за несколько минут.

## <a name="create-an-iot-hub"></a>Создание Центра Интернета вещей

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Регистрация нового устройства в центре Интернета вещей

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="get-the-iot-hub-connection-string"></a>Получение строки подключения для центра Интернета вещей

[!INCLUDE [iot-hub-howto-twin-shared-access-policy-text](../../includes/iot-hub-howto-twin-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-custom-connection-string](../../includes/iot-hub-include-find-custom-connection-string.md)]

## <a name="create-the-service-app"></a>Создание приложения службы

В этом разделе вы создадите консольное приложение .NET с помощью C#, которое добавляет метаданные расположения в двойника устройства, связанные с **myDeviceId**. После этого оно запрашивает данные двойников устройств, хранящихся в Центре Интернета вещей, выбирает устройства, находящиеся в США, а затем те, которые сообщили о подключении по сети мобильной связи.

1. В Visual Studio выберите **создать новый проект**. В окне **Создание нового проекта**выберите **консольное приложение (.NET Framework)** , а затем нажмите кнопку **Далее**.

1. В окне **Настройка нового проекта**назовите проект **AddTagsAndQuery**.

    ![Настройка проекта AddTagsAndQuery](./media/iot-hub-csharp-csharp-twin-getstarted/config-addtagsandquery-app.png)

1. В обозреватель решений щелкните правой кнопкой мыши проект **AddTagsAndQuery** , а затем выберите пункт **Управление пакетами NuGet**.

1. Нажмите кнопку **Обзор** и найдите и выберите **Microsoft. Azure. Devices**. Щелкните **Установить**.

    ![Окно "Диспетчер пакетов NuGet"](./media/iot-hub-csharp-csharp-twin-getstarted/nuget-package-addtagsandquery-app.png)

   В результате выполняется скачивание и установка пакета NuGet [SDK для служб Azure IoT](https://www.nuget.org/packages/Microsoft.Azure.Devices/) и его зависимостей, а также добавляется соответствующая ссылка.

1. Добавьте следующие инструкции `using` в начало файла **Program.cs** :

    ```csharp  
    using Microsoft.Azure.Devices;
    ```

1. Добавьте следующие поля в класс **Program** . Замените `{iot hub connection string}` строкой подключения центра Интернета вещей, скопированной в поле [Получение строки подключения для центра Интернета вещей](#get-the-iot-hub-connection-string).

    ```csharp  
    static RegistryManager registryManager;
    static string connectionString = "{iot hub connection string}";
    ```

1. Добавьте следующий метод в класс **Program** .

    ```csharp  
    public static async Task AddTagsAndQuery()
    {
        var twin = await registryManager.GetTwinAsync("myDeviceId");
        var patch =
            @"{
                tags: {
                    location: {
                        region: 'US',
                        plant: 'Redmond43'
                    }
                }
            }";
        await registryManager.UpdateTwinAsync(twin.DeviceId, patch, twin.ETag);

        var query = registryManager.CreateQuery(
          "SELECT * FROM devices WHERE tags.location.plant = 'Redmond43'", 100);
        var twinsInRedmond43 = await query.GetNextAsTwinAsync();
        Console.WriteLine("Devices in Redmond43: {0}", 
          string.Join(", ", twinsInRedmond43.Select(t => t.DeviceId)));

        query = registryManager.CreateQuery("SELECT * FROM devices WHERE tags.location.plant = 'Redmond43' AND properties.reported.connectivity.type = 'cellular'", 100);
        var twinsInRedmond43UsingCellular = await query.GetNextAsTwinAsync();
        Console.WriteLine("Devices in Redmond43 using cellular network: {0}", 
          string.Join(", ", twinsInRedmond43UsingCellular.Select(t => t.DeviceId)));
    }
    ```

    Класс **RegistryManager** предоставляет все методы, необходимые для взаимодействия с двойниками устройства из службы. Предыдущий код сначала инициализирует объект **RegistryManager**, затем извлекает двойник устройства для **MyDeviceId** и обновляет его теги, используя сведения о требуемом расположении.

    После обновления он выполняет два запроса: первый выбирает только двойники устройств, расположенные на фабрике **Redmond43**, а второй уточняет условия первого запроса и выбирает устройства, подключенные по сети мобильной связи.

    При создании объекта **query** предыдущий код указывает максимальное количество возвращаемых документов. Объект **Query** содержит логическое свойство **HasMoreResults**, которое можно использовать для вызова методов **GetNextAsTwinAsync** несколько раз, чтобы получить все результаты. Метод **GetNextAsJson** доступен для результатов, которые не являются двойниками устройств, например результатов статистических запросов.

1. Наконец, добавьте следующие строки в метод **Main** :

    ```csharp  
    registryManager = RegistryManager.CreateFromConnectionString(connectionString);
    AddTagsAndQuery().Wait();
    Console.WriteLine("Press Enter to exit.");
    Console.ReadLine();
    ```

1. Запустите это приложение, щелкнув правой кнопкой мыши проект **AddTagsAndQuery** и выбрав **Отладка**, а затем — **Запустить новый экземпляр**. В результатах запроса на все устройства, расположенные на фабрике **Redmond43**, отобразится одно устройство, а для запроса на ограничение результатов устройствами, использующими сеть мобильной связи, не отобразится ни одного устройства.

    ![Результаты запроса в окне](./media/iot-hub-csharp-csharp-twin-getstarted/addtagapp.png)

В следующем разделе рассказывается о том, как создать приложение устройства, которое сообщает сведения о подключении и изменяет результат запроса, описанного в предыдущем разделе.

## <a name="create-the-device-app"></a>Создание приложения устройства

В этом разделе вы создадите консольное приложение .NET, которое подключается к центру как **myDeviceId** и обновляет передаваемые свойства, добавив в них сведения о подключении по сети мобильной связи.

1. В Visual Studio выберите **Файл** > **Создать** > **Проект**. В окне **Создание нового проекта**выберите **консольное приложение (.NET Framework)** , а затем нажмите кнопку **Далее**.

1. В окне **Настройка нового проекта**назовите проект **ReportConnectivity**. В качестве **решения**выберите **Добавить в решение**и нажмите кнопку **создать**.

1. В обозреватель решений щелкните правой кнопкой мыши проект **ReportConnectivity** , а затем выберите пункт **Управление пакетами NuGet**.

1. Нажмите кнопку **Обзор** и найдите и выберите **Microsoft. Azure. Devices. Client**. Щелкните **Установить**.

   На этом шаге выполняется скачивание, установка и Добавление ссылки на пакет [SDK для устройств Azure IOT](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/) и его зависимости.

1. Добавьте следующие инструкции `using` в начало файла **Program.cs** :

    ```csharp  
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    using Newtonsoft.Json;
    ```

1. Добавьте следующие поля в класс **Program** . Замените `{device connection string}` строкой подключения устройства, записанной в окне [Регистрация нового устройства в центре Интернета вещей](#register-a-new-device-in-the-iot-hub).

    ```csharp  
    static string DeviceConnectionString = "HostName=<yourIotHubName>.azure-devices.net;DeviceId=<yourIotDeviceName>;SharedAccessKey=<yourIotDeviceAccessKey>";
    static DeviceClient Client = null;
    ```

1. Добавьте следующий метод в класс **Program** .

    ```csharp
    public static async void InitClient()
    {
        try
        {
            Console.WriteLine("Connecting to hub");
            Client = DeviceClient.CreateFromConnectionString(DeviceConnectionString, 
              TransportType.Mqtt);
            Console.WriteLine("Retrieving twin");
            await Client.GetTwinAsync();
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
        }
    }
    ```

    Объект **Client** позволяет получить все методы, необходимые для взаимодействия с двойниками устройства с устройства. Приведенный выше код инициализирует **клиентский** объект, а затем получает двойника устройства для **myDeviceId**.

1. Добавьте следующий метод в класс **Program** .

    ```csharp  
    public static async void ReportConnectivity()
    {
        try
        {
            Console.WriteLine("Sending connectivity data as reported property");

            TwinCollection reportedProperties, connectivity;
            reportedProperties = new TwinCollection();
            connectivity = new TwinCollection();
            connectivity["type"] = "cellular";
            reportedProperties["connectivity"] = connectivity;
            await Client.UpdateReportedPropertiesAsync(reportedProperties);
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
        }
    }
    ```

   Приведенный выше код обновляет сообщаемое свойство объекта **myDeviceId** , используя сведения о подключении.

1. Наконец, добавьте следующие строки в метод **Main** :

    ```csharp
    try
    {
        InitClient();
        ReportConnectivity();
    }
    catch (Exception ex)
    {
        Console.WriteLine();
        Console.WriteLine("Error in sample: {0}", ex.Message);
    }
    Console.WriteLine("Press Enter to exit.");
    Console.ReadLine();
    ```

1. В обозреватель решений щелкните решение правой кнопкой мыши и выберите команду **задать запускаемые проекты**.

1. В меню **стандартные свойства** > **Запуск проекта**выберите **Несколько запускаемых проектов**. Для **ReportConnectivity**выберите **Запуск** в качестве **действия**. Нажмите кнопку **ОК** , чтобы сохранить изменения.  

1. Запустите это приложение, щелкнув правой кнопкой мыши проект **ReportConnectivity** и выбрав пункт **Отладка**, а затем **запустите новый экземпляр**. Вы увидите, что приложение получает сведения о двойника, а затем отправляет подключение как ***сообщаемое свойство***.

    ![Запуск приложения устройства для передачи данных подключения](./media/iot-hub-csharp-csharp-twin-getstarted/rundeviceapp.png)

   После того как устройство сообщило сведения о подключении, оно должно появиться в обоих запросах.

1. Щелкните правой кнопкой мыши проект **AddTagsAndQuery** и выберите **Отладка** > **запустить новый экземпляр** для повторного выполнения запросов. На этот раз **myDeviceId** должен появиться в обоих результатах запроса.

    ![Сведения о подключении устройства успешно переданы](./media/iot-hub-csharp-csharp-twin-getstarted/tagappsuccess.png)

## <a name="next-steps"></a>Следующие шаги

В этом руководстве мы настроили новый Центр Интернета вещей на портале Azure и создали удостоверение устройства в реестре удостоверений Центра Интернета вещей. Вы добавили метаданные устройства в качестве тегов из внутреннего приложения и написали код приложения имитации устройства, чтобы сообщить сведения о подключении в двойнике устройства. Вы также узнали, как запрашивать эти сведения, используя похожий на SQL язык запросов Центра Интернета вещей.

Дополнительные сведения можно узнать из следующих ресурсов:

* Чтобы узнать, как отправить данные телеметрии с устройств, см. Руководство по [отправке данных телеметрии с устройства в центр Интернета вещей](quickstart-send-telemetry-dotnet.md) .

* Сведения о настройке устройств с помощью требуемых свойств двойникаа устройства см. в учебнике [Использование требуемых свойств для настройки устройств](tutorial-device-twins.md) .

* Сведения об управлении устройствами в интерактивном режиме, например о включении вентилятора из управляемого пользователем приложения, см. в руководстве по [использованию прямых методов](quickstart-control-device-dotnet.md) .
