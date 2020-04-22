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
ms.custom: mqtt
ms.openlocfilehash: 620e0213733d278a28ec1bcad4b031f5764ccda9
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81733133"
---
# <a name="get-started-with-device-twins-net"></a>Начало работы с близнецами устройств (.NET)

[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

В этом уроке вы создаете эти приложения консоли .NET:

* **СозданиеDeviceIdentity**. Это приложение создает идентификацию устройства и связанный с ним ключ безопасности для подключения смоделированного приложения устройства.

* **AddTagsИКери**. Это приложение бэк-энда добавляет теги и запросы близнецов устройств.

* **ОтчетОсначтов**. Это устройство имитирует устройство, которое подключается к концентратору IoT с созданным ранее устройством, и сообщает о состоянии его подключения.

> [!NOTE]
> Статья [IoT Hub SDKs](iot-hub-devguide-sdks.md) (Пакеты SDK для Центра Интернета вещей) содержит сведения о разных пакетах SDK для Azure IoT, с помощью которых можно создать приложения для устройств и внутренние приложения.
>

## <a name="prerequisites"></a>Предварительные требования

* приведенному.

* Активная учетная запись Azure. Если у вас нет учетной записи, вы можете создать [бесплатную учетную запись](https://azure.microsoft.com/pricing/free-trial/) всего за пару минут.

* Убедитесь, что в брандмауэре открыт порт 8883. Образец устройства в этой статье использует протокол МЗТТ, который общается по порту 8883. В некоторых корпоративных и академических сетях этот порт может быть заблокирован. Дополнительные сведения и способы устранения этой проблемы см. в разделе о [подключении к Центру Интернета вещей по протоколу MQTT](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="create-an-iot-hub"></a>Создание Центра Интернета вещей

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Регистрация нового устройства в центре Интернета вещей

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="get-the-iot-hub-connection-string"></a>Получите строку соединения концентратора IoT

[!INCLUDE [iot-hub-howto-twin-shared-access-policy-text](../../includes/iot-hub-howto-twin-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-custom-connection-string](../../includes/iot-hub-include-find-custom-connection-string.md)]

## <a name="create-the-service-app"></a>Создание приложения службы

В этом разделе вы создаете консоль .NET приложение, используя C, которое добавляет метаданные местоположения в устройство twin, связанное с **myDeviceId**. После этого оно запрашивает данные двойников устройств, хранящихся в Центре Интернета вещей, выбирает устройства, находящиеся в США, а затем те, которые сообщили о подключении по сети мобильной связи.

1. В Visual Studio выберите **Создать проект**. В **Создать новый проект**, выберите **консоль App (.NET Framework)**, а затем выберите **Следующий**.

1. В **настройке нового проекта**назовите проект **AddTagsAnd'еry**.

    ![Настройте свой проект AddTagsAnd'еry](./media/iot-hub-csharp-csharp-twin-getstarted/config-addtagsandquery-app.png)

1. В Solution Explorer, правой кнопкой мыши **проекта AddTagsии,,,ry,** а затем выберите **Управление NuGet пакеты**.

1. Выберите **просмотр** и поиск и выберите **Microsoft.Azure.Devices**. Выберите пункт **Установить**.

    ![Окно "Диспетчер пакетов NuGet"](./media/iot-hub-csharp-csharp-twin-getstarted/nuget-package-addtagsandquery-app.png)

   В результате выполняется скачивание и установка пакета NuGet [SDK для служб Интернета вещей Azure](https://www.nuget.org/packages/Microsoft.Azure.Devices/) и его зависимостей, а также добавляется соответствующая ссылка.

1. Добавьте `using` следующие инструкции в верхней части файла **Program.cs:**

    ```csharp  
    using Microsoft.Azure.Devices;
    ```

1. Добавьте следующие поля в класс **Program** . Заменить `{iot hub connection string}` строку соединения IoT Hub, которую вы скопировали в [строке подключения концентратора IoT.](#get-the-iot-hub-connection-string)

    ```csharp  
    static RegistryManager registryManager;
    static string connectionString = "{iot hub connection string}";
    ```

1. Добавьте в класс **Program** следующий метод:

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

    При создании объекта **query** предыдущий код указывает максимальное количество возвращаемых документов. Объект **Query** содержит логическое свойство **HasMoreResults**, которое можно использовать для вызова методов **GetNextAsTwinAsync** несколько раз, чтобы получить все результаты. Метод под названием **GetNextAsJson** доступен для результатов, которые не являются близнецами устройств, например, для результатов агрегации запросов.

1. Наконец, добавьте следующие строки в **основной** метод:

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

1. В Visual Studio выберите **Файл** > **Создать** > **Проект**. В **создании нового проекта**выберите **консоль app (.NET Framework),** а затем выберите **Следующий**.

1. В **настройке нового проекта**назовите проект **ReportConnectivity**. Для **решения** **выберите Добавить в решение,** а затем выберите **Создать.**

1. В Solution Explorer нажмите правой кнопкой мыши на проект **ReportConnectivity,** а затем выберите **пакеты Управления NuGet.**

1. Выберите **просмотр** и поиск и выберите **Microsoft.Azure.Devices.Client**. Выберите пункт **Установить**.

   Этот шаг загружает, устанавливает и добавляет ссылку на пакет [SDK NuGet-устройства Azure IoT](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/) и его зависимости.

1. Добавьте `using` следующие инструкции в верхней части файла **Program.cs:**

    ```csharp  
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    using Newtonsoft.Json;
    ```

1. Добавьте следующие поля в класс **Program** . Замените `{device connection string}` строку подключения устройства, которую вы отметили в [Регистрации нового устройства в концентраторе IoT.](#register-a-new-device-in-the-iot-hub)

    ```csharp  
    static string DeviceConnectionString = "HostName=<yourIotHubName>.azure-devices.net;DeviceId=<yourIotDeviceName>;SharedAccessKey=<yourIotDeviceAccessKey>";
    static DeviceClient Client = null;
    ```

1. Добавьте в класс **Program** следующий метод:

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

    Объект **Client** позволяет получить все методы, необходимые для взаимодействия с двойниками устройства с устройства. Код, показанный выше, инициализирует объект **Клиента,** а затем получает устройство twin для **myDeviceId.**

1. Добавьте в класс **Program** следующий метод:

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

   Приведенный выше код обновляет указанное свойство **myDeviceId** с информацией о подключении.

1. Наконец, добавьте следующие строки в **основной** метод:

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

1. В Solution Explorer нажмите правой кнопкой мыши на ваше решение и выберите **Проекты StartUp.**

1. В**проекте запуска** **Common Properties** > выберите **несколько проектов запуска.** Для **ReportConnectivity**выберите **«Начни»** в качестве **действия.** Нажмите кнопку **ОК** , чтобы сохранить изменения.  

1. Выполнить это приложение, нажав правой щелчком проекта **ReportConnectivity** и выбрав **Debug,** а затем **запустите новый экземпляр.** Вы должны увидеть приложение, получая информацию о двойнике, а затем отправлять подключение в качестве ***объекта недвижимости.***

    ![Запуск приложения устройства для передачи данных подключения](./media/iot-hub-csharp-csharp-twin-getstarted/rundeviceapp.png)

   После того, как устройство сообщило информацию о подключении, оно должно появиться в обоих запросах.

1. Нажмите правой кнопкой мыши на **проекте AddTags And'еry** и выберите Новый экземпляр **Debug** > **Start** для повторного выполнения запросов. На этот раз **myDeviceId** должен отображаться в результатах обоих запросов.

    ![Сведения о подключении устройства успешно переданы](./media/iot-hub-csharp-csharp-twin-getstarted/tagappsuccess.png)

## <a name="next-steps"></a>Следующие шаги

В этом руководстве мы настроили новый Центр Интернета вещей на портале Azure и создали удостоверение устройства в реестре удостоверений Центра Интернета вещей. Вы добавили метаданные устройства в качестве тегов из внутреннего приложения и написали код приложения имитации устройства, чтобы сообщить сведения о подключении в двойнике устройства. Вы также узнали, как запрашивать эти сведения, используя похожий на SQL язык запросов Центра Интернета вещей.

Вы можете узнать больше из следующих ресурсов:

* Чтобы узнать, как отправлять телеметрию с устройств, см. [телеметрию Отправки с устройства на учебник концентратора IoT.](quickstart-send-telemetry-dotnet.md)

* Чтобы узнать, как настроить устройства с помощью желаемых свойств близнеца устройства, [см.](tutorial-device-twins.md)

* Чтобы узнать, как управлять устройствами в интерактивном режиме, например, включить вентилятор из приложения, управляемого пользователем, см. [Use direct methods](quickstart-control-device-dotnet.md)
