---
title: Приступая к работе с удостоверением и двойником модуля Центра Интернета вещей Azure (портал и .NET) | Документация Майкрософт
description: Сведения о создании удостоверения модуля и обновлении двойника модуля с помощью портала и .NET.
author: robinsh
manager: philmea
ms.author: robin.shahan
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 04/26/2018
ms.openlocfilehash: f323eb777d98a812ead8dd8ac78ee3d6b36a748a
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57545826"
---
# <a name="get-started-with-iot-hub-module-identity-and-module-twin-using-the-portal-and-net-device"></a>Начало работы с удостоверением модуля Центра Интернета вещей и двойником модуля с использованием портала и устройства .NET

> [!NOTE]
> [Удостоверение и двойник модуля](iot-hub-devguide-module-twins.md) аналогичны удостоверению и двойнику устройства Центра Интернета вещей Azure, однако они предоставляют более высокую степень детализации. В то время как удостоверение и двойник устройства Центра Интернета вещей Azure позволяют серверному приложению настраивать устройство и отображать состояние устройства, удостоверение и двойник модуля предоставляют эти возможности для отдельных компонентов устройства. Благодаря этому можно изолировать конфигурацию и условия для каждого компонента на совместимых устройствах с несколькими компонентами, например устройствах на основе операционной системы или устройствах со встроенным ПО.
>

Из этого руководства вы узнаете:

1. как создать удостоверение модуля на портале; 

2. как использовать SDK для устройства .NET, чтобы обновить двойник модуля из устройства.

> [!NOTE]
> Статья о [пакетах SDK для Центра Интернета вещей Azure][lnk-hub-sdks] содержит сведения о различных пакетах SDK, которые можно использовать для создания приложений, которые будут работать на устройствах и в серверной части решения.
>

Для работы с этим учебником требуется:

* Visual Studio 2015 или Visual Studio 2017.
* Активная учетная запись Azure. Если ее нет, можно создать [бесплатную учетную запись][lnk-free-trial] всего за несколько минут.

## <a name="create-an-iot-hub"></a>Создание Центра Интернета вещей

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

### <a name="retrieve-connection-string-for-iot-hub"></a>Получение строки подключения для центра Интернета вещей

[!INCLUDE [iot-hub-include-find-connection-string](../../includes/iot-hub-include-find-connection-string.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Регистрация нового устройства в центре Интернета вещей

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="create-a-module-identity-in-the-portal"></a>Создание удостоверения модуля на портале

В рамках одного удостоверения устройства можно создать до 20 удостоверений модуля. Нажмите кнопку **Add Module Identity** (Добавить удостоверение модуля) сверху, чтобы создать свое первое удостоверение модуля под названием **myFirstModule**. 

  ![Сведения об устройстве][9]

Нажмите кнопку "Сохранить" и щелкните только что созданное удостоверение модуля. Отобразятся сведения об удостоверении модуля. Сохраните значение параметра "Строка подключения — первичный ключ". Оно будет использоваться в следующем разделе при настройке модуля на устройстве.

  ![Сведения об устройстве][12]

## <a name="update-the-module-twin-using-net-device-sdk"></a>Обновление двойника модуля с помощью пакета SDK для устройства .NET

Вы успешно создали удостоверение модуля в своем Центре Интернета вещей. Давайте попробуем обменяться данными с облаком со своего имитированного устройства. После создания удостоверения модуля двойник модуля будет неявно создан в Центре Интернета вещей. В этом разделе вы создадите консольное приложение .NET на имитированном устройстве, которое обновляет сообщаемые свойства двойника модуля.

## <a name="create-a-visual-studio-project"></a>Создание проекта Visual Studio

В Visual Studio добавьте в имеющееся решение проект классического приложения Windows на языке Visual C# с помощью шаблона проекта **консольного приложения (.NET Framework)**. Убедитесь, что указана версия платформы .NET 4.6.1 или более поздняя. В качестве имени проекта укажите **UpdateModuleTwinReportedProperties**.

  ![Создание проекта Visual Studio][13]

## <a name="install-the-latest-azure-iot-hub-net-device-sdk"></a>Установка последнего пакета SDK для устройств Центра Интернета вещей Azure для .NET

Удостоверения модулей и двойники модулей предоставляются в общедоступной предварительной версии. Они доступны только в пакетах SDK предварительного выпуска для Центра Интернета вещей. В Visual Studio выберите для решения элементы "Сервис > Диспетчер пакетов NuGet > Управление пакетами NuGet". Найдите Microsoft.Azure.Devices.Client. Обязательно установите флажок "Включить предварительные выпуски". Выберите последнюю версию и установите ее. Теперь у вас есть доступ ко всем возможностям этого модуля. 

  ![Установка пакета SDK V1.16.0-preview-005 .NET для Центра Интернета вещей Azure][14]

## <a name="get-your-module-connection-string"></a>Получение строки подключения модуля

Войдите на [портал Azure][lnk-portal]. Перейдите к Центру Интернета вещей и щелкните "Устройства IoT". Найдите устройство myFirstDevice, откройте его и вы увидите, что модуль myFirstModule успешно создан. Скопируйте строку подключения модуля. Она понадобится на следующем шаге.

  ![Сведения о модуле на портале Azure][15]

## <a name="create-updatemoduletwinreportedproperties-console-app"></a>Создание консольного приложения UpdateModuleTwinReportedProperties

Добавьте следующие инструкции `using` в начало файла **Program.cs** :

```csharp
using Microsoft.Azure.Devices.Client;
using Microsoft.Azure.Devices.Shared;
```

Добавьте следующие поля в класс **Program** . Замените значение заполнителя строкой подключения модуля.

```csharp
private const string ModuleConnectionString = "<Your module connection string>";
private static ModuleClient Client = null;
```

Добавьте метод **OnDesiredPropertyChanged** в класс **Program**:

```csharp
private static async Task OnDesiredPropertyChanged(TwinCollection desiredProperties, object userContext)
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
    Microsoft.Azure.Devices.Client.TransportType transport = Microsoft.Azure.Devices.Client.TransportType.Amqp;

    try
    {
        Client = ModuleClient.CreateFromConnectionString(ModuleConnectionString, transport);
        Client.SetConnectionStatusChangesHandler(ConnectionStatusChangeHandler);
        Client.SetDesiredPropertyUpdateCallbackAsync(OnDesiredPropertyChanged, null).Wait();

        Console.WriteLine("Retrieving twin");
        var twinTask = Client.GetTwinAsync();
        twinTask.Wait();
        var twin = twinTask.Result;
        Console.WriteLine(JsonConvert.SerializeObject(twin));

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
    Console.ReadKey();
    Client.CloseAsync().Wait();
}

private static void ConnectionStatusChangeHandler(ConnectionStatus status, ConnectionStatusChangeReason reason)
{
    Console.WriteLine($"Status {status} changed: {reason}");
}
```

В этом примере кода показано, как извлечь двойник модуля и обновить сообщаемые свойства с помощью протокола AMQP. В общедоступной предварительной версии для операций двойника модуля поддерживается только протокол AMQP.

## <a name="run-the-apps"></a>Запуск приложений

Теперь все готово к запуску приложений. В обозревателе решений Visual Studio щелкните правой кнопкой мыши решение и выберите пункт **Назначить запускаемые проекты**. Выберите **Несколько запускаемых проектов**, а затем — **Запустить** в качестве действия для консольного приложения. Затем нажмите клавишу F5, чтобы запустить оба приложения. 

## <a name="next-steps"></a>Дальнейшие действия

Чтобы продолжить знакомство с Центром Интернета вещей и изучить другие сценарии Интернета вещей, см. следующие ресурсы:

* [Приступая к работе с удостоверением Центра Интернета вещей и двойником модуля с использованием резервной копии и устройства .NET][lnk-csharp-csharp-getstarted]
* [Explore Azure IoT Edge architecture on Linux][lnk-iot-edge] (Приступая к работе с архитектурой Azure IoT Edge в Linux)


<!-- Images. -->
[9]:./media/iot-hub-portal-csharp-module-twin-getstarted/create-module-id.JPG
[12]:./media/iot-hub-portal-csharp-module-twin-getstarted/module-details.JPG
[13]: ./media/iot-hub-csharp-csharp-module-twin-getstarted/update-twins-csharp1.JPG
[14]: ./media/iot-hub-csharp-csharp-module-twin-getstarted/install-sdk.png
[15]: ./media/iot-hub-csharp-csharp-module-twin-getstarted/module-detail.JPG
<!-- Links -->
[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/

[lnk-csharp-csharp-getstarted]: iot-hub-csharp-csharp-module-twin-getstarted.md
[lnk-iot-edge]: ../iot-edge/tutorial-simulate-device-linux.md
