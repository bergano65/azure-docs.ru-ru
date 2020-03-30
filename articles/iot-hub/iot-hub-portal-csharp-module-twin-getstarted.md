---
title: Идентификационный модуль Azure IoT концентратор & двойник модуля (портал и .NET)
description: Сведения о создании удостоверения модуля и обновлении двойника модуля с помощью портала и .NET.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 08/20/2019
ms.openlocfilehash: 289c269100eb6ab672bb8d60562c1fa77d8d091a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73954616"
---
# <a name="get-started-with-iot-hub-module-identity-and-module-twin-using-the-portal-and-net-device"></a>Начало работы с удостоверением модуля Центра Интернета вещей и двойником модуля с использованием портала и устройства .NET

[!INCLUDE [iot-hub-selector-module-twin-getstarted](../../includes/iot-hub-selector-module-twin-getstarted.md)]

> [!NOTE]
> [Удостоверение и двойник модуля](iot-hub-devguide-module-twins.md) аналогичны удостоверению и двойнику устройства Центра Интернета вещей Azure, однако они предоставляют более высокую степень детализации. В то время как идентификация устройства Azure IoT Hub и двойная устройство позволяют задней части приложения настроить устройство и обеспечить видимость условий устройства, модуль идентичности и модуль двойной обеспечить эти возможности для отдельных компонентов устройства. На дееспособных устройствах с несколькими компонентами, такими как устройства на основе операционной системы или устройства прошивки, идентификаторы модулей и близнецы модулей позволяют проводить изолированную конфигурацию и условия для каждого компонента.
>

Из этого руководства вы узнаете:

* как создать удостоверение модуля на портале;

* Как использовать устройство SDK .NET для обновления модуля-близнеца с вашего устройства.

> [!NOTE]
> Для получения информации о SDK Azure IoT, которые можно использовать для создания [Azure IoT SDKs](iot-hub-devguide-sdks.md)как приложений для запуска на устройствах, так и задней части решения, см.
>

## <a name="prerequisites"></a>Предварительные требования

* приведенному.

* Активная учетная запись Azure. Если у вас нет учетной записи, вы можете создать [бесплатную учетную запись](https://azure.microsoft.com/pricing/free-trial/) всего за пару минут.

## <a name="create-a-hub"></a>Создание концентратора.

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-hub"></a>Регистрация нового устройства в концентраторе

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="create-a-module-identity-in-the-portal"></a>Создание удостоверения модуля на портале

В рамках одного удостоверения устройства можно создать до 20 удостоверений модуля. Чтобы добавить идентификацию, выполните следующие действия:

1. Для устройства, созданного в предыдущем разделе, выберите **«Добавить идентичность модуля»,** чтобы создать свой первый модульный итог.

1. Введите имя *myFirstModule*. Сохраните идентификацию модуля.

    ![Добавление итогового модуля](./media/iot-hub-portal-csharp-module-twin-getstarted/add-module-identity.png)

    Новая идентификация модуля отображается в нижней части экрана. Выберите его, чтобы увидеть данные модуля идентификации.

    ![Посмотреть данные о личности модуля](./media/iot-hub-portal-csharp-module-twin-getstarted/module-identity-details.png)

Сохранить **строку Connect - основной ключ.** Вы используете его в следующем разделе, чтобы настроить модуль на устройстве.

## <a name="update-the-module-twin-using-net-device-sdk"></a>Обновление двойника модуля с помощью пакета SDK для устройства .NET

Вы успешно создали удостоверение модуля в своем Центре Интернета вещей. Давайте попробуем обменяться данными с облаком со своего имитированного устройства. После создания удостоверения модуля двойник модуля будет неявно создан в Центре Интернета вещей. В этом разделе вы создадите консольное приложение .NET на имитированном устройстве, которое обновляет сообщаемые свойства двойника модуля.

### <a name="create-a-visual-studio-project"></a>Создание проекта Visual Studio

Чтобы создать приложение, обновляя свойства, сообщаемые двумя версиями модуля, выполните следующие действия:

1. В Visual Studio выберите **Создайте новый проект,** затем выберите **Console App (.NET Framework)** и выберите **Следующий**.

1. В **настройке нового проекта,** введите *UpdateModuleTwinReportedСвойства* как **название проекта**. Щелкните **Создать** , чтобы продолжить.

    ![Наверстывание проекта визуальной студии](./media/iot-hub-portal-csharp-module-twin-getstarted/configure-twins-project.png)

### <a name="install-the-latest-azure-iot-hub-net-device-sdk"></a>Установка последнего пакета SDK для устройств Центра Интернета вещей Azure для .NET

Удостоверения модулей и двойники модулей предоставляются в общедоступной предварительной версии. Он доступен только в предвыпускном устройстве IoT Hub SDK. Чтобы установить его, выполните следующие действия:

1. В Visual Studio, открытые **инструменты** > **NuGet менеджер** > пакета**управления NuGet пакеты для решения**.

1. Выберите **Просмотр,** а затем выберите **Включить пререлиз**. Поиск по *microsoft.Azure.Devices.Client*. Выберите последнюю версию и установите ее.

    ![Установка предварительного просмотра сервиса SDK Azure IoT .NET](./media/iot-hub-csharp-csharp-module-twin-getstarted/install-sdk.png)

    Теперь у вас есть доступ ко всем возможностям этого модуля.

### <a name="get-your-module-connection-string"></a>Получение строки подключения модуля

Вам нужна строка соединения модуля для приложения консоли. Выполните следующие действия.

1. Войдите на [портал Azure](https://portal.azure.com/).

1. Перейдите к своему концентратору IoT и выберите **IoT-устройства.** Откройте **myFirstDevice** и увидите, что **myFirstModule** был успешно создан.

1. Выберите **myFirstModule** под **удостоверениями модуля.** В **сведениях об идентификации модуля**скопируйте **строку Connection (основной ключ).**

    ![Сведения о модуле на портале Azure](./media/iot-hub-portal-csharp-module-twin-getstarted/module-identity-details.png)

### <a name="create-updatemoduletwinreportedproperties-console-app"></a>Создание консольного приложения UpdateModuleTwinReportedProperties

Чтобы создать приложение, выполните следующие действия:

1. Добавьте следующие инструкции `using` в начало файла **Program.cs** :

  ```csharp
  using Microsoft.Azure.Devices.Client;
  using Microsoft.Azure.Devices.Shared;
  using Newtonsoft.Json;
  ```

2. Добавьте следующие поля в класс **Program** . Замените значение заполнителя строкой подключения модуля.

  ```csharp
  private const string ModuleConnectionString = "<Your module connection string>";
  private static ModuleClient Client = null;
  ```

3. Добавьте метод **OnDesiredPropertyChanged** в класс **Program**:

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

4. Наконец, замените **основной** метод следующим кодом:

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
  
  Вы можете построить и запустить это приложение с помощью **F5**.

В этом примере кода показано, как извлечь двойник модуля и обновить сообщаемые свойства с помощью протокола AMQP. В общедоступной предварительной версии для операций двойника модуля поддерживается только протокол AMQP.

## <a name="next-steps"></a>Дальнейшие действия

Чтобы продолжить знакомство с Центром Интернета вещей и изучить другие сценарии Интернета вещей, см. следующие ресурсы:

* [Приступая к работе с удостоверением Центра Интернета вещей и двойником модуля с использованием резервной копии и устройства .NET](iot-hub-csharp-csharp-module-twin-getstarted.md)

* [Краткое руководство. Развертывание первого модуля IoT Edge на устройстве под управлением 64-разрядной ОС Linux](../iot-edge/tutorial-simulate-device-linux.md)
