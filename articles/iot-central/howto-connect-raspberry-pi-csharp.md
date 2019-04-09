---
title: Подключение Raspberry Pi к приложению Azure IoT Central (C#) | Документация Майкрософт
description: Как разработчик устройств подключение устройства Raspberry Pi к Azure IoT Central приложения с помощью C#.
author: viv-liu
ms.author: viviali
ms.date: 04/05/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 8137f7d167cc697671de99699c6031014d6a966e
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/08/2019
ms.locfileid: "59275969"
---
# <a name="connect-a-raspberry-pi-to-your-azure-iot-central-application-c"></a>Подключение Raspberry Pi к приложению Azure IoT Central (C#)

[!INCLUDE [howto-raspberrypi-selector](../../includes/iot-central-howto-raspberrypi-selector.md)]

В этой статье описано, каким образом разработчик устройства может подключить устройство Raspberry Pi к приложению Microsoft Azure IoT Central, используя язык C#.

## <a name="before-you-begin"></a>Перед началом работы

Чтобы выполнить действия, описанные в этой статье, вам потребуются следующие компоненты:

* Приложение Azure IoT Central, созданное на основе шаблона приложения **Sample Devkits** (Образец Devkits). Дополнительные сведения см. в [кратком руководстве по созданию приложения](quick-deploy-iot-central.md).
* Устройство Raspberry Pi с операционной системой Raspbian. Raspberry Pi необходимо иметь возможность подключения к Интернету. Дополнительные сведения см. в разделе [Настройка Raspberry Pi](https://projects.raspberrypi.org/en/projects/raspberry-pi-setting-up/3).

## <a name="sample-devkits-application"></a>Пример **приложения Devkits**

Приложение, созданное на основе шаблона приложения **Sample Devkits** (Образец Devkits), включает в себя шаблон приложения **Raspberry Pi** со следующими характеристиками:

- Данные телеметрии. К ним относятся следующие показатели, которые устройство будет собирать:
  - влажность.
  - температура;
  - Давление
  - магнитометр (X, Y, Z);
  - акселерометр (X, Y, Z);
  - гироскоп (X, Y, Z).
- Параметры
  - Напряжение
  - Текущее значение
  - Скорость вращения вентилятора
  - ИК-переключатель.
- properties
  - Свойство серийного номера устройства
  - Свойство расположения облака

Подробные сведения о конфигурации шаблона устройства, см. в разделе [сведения о шаблоне устройство Raspberry Pi](#raspberry-pi-device-template-details).

## <a name="add-a-real-device"></a>Добавление реального устройства

В приложении Azure IoT Central, добавьте реального устройства из **Raspberry Pi** шаблон устройства. Запомните или запишите устройства сведения о подключении (**идентификатор области**, **идентификатор устройства**, и **первичный ключ**). Дополнительные сведения см. в статье [Добавление реального устройства в приложение Azure IoT Central](tutorial-add-device.md).

### <a name="create-your-net-application"></a>Создание приложения .NET

Вы создадите и протестируете приложение устройства на настольном компьютере.

Чтобы выполнить следующие действия, можно использовать Visual Studio Code. Дополнительные сведения см. в разделе, посвященном [работе с языком C#](https://code.visualstudio.com/docs/languages/csharp).

> [!NOTE]
> При желании вы можете выполнить следующие действия с помощью другого редактора кода.

1. Чтобы инициализировать проект .NET и добавить необходимые пакеты NuGet, выполните следующие команды:

   ```cmd/sh
   mkdir pisample
   cd pisample
   dotnet new console
   dotnet add package Microsoft.Azure.Devices.Client
   dotnet restore
   ```

1. Откройте папку `pisample` в Visual Studio Code. Затем откройте файл проекта **pisample.csproj**. Добавьте тег `<RuntimeIdentifiers>`, показанный в следующем фрагменте кода:

    ```xml
    <Project Sdk="Microsoft.NET.Sdk">
      <PropertyGroup>
        <OutputType>Exe</OutputType>
        <TargetFramework>netcoreapp2.0</TargetFramework>
        <RuntimeIdentifiers>win-arm;linux-arm</RuntimeIdentifiers>
      </PropertyGroup>
      <ItemGroup>
        <PackageReference Include="Microsoft.Azure.Devices.Client" Version="1.19.0" />
      </ItemGroup>
    </Project>
    ```

    > [!NOTE]
    > Номер версии пакета **Microsoft.Azure.Devices.Client** может быть выше, чем в фрагменте кода.

1. Сохраните файл **pisample.csproj**. Если в Visual Studio Code появится запрос на выполнение команды восстановления, выберите **Восстановить**.

1. Откройте файл **Program.cs** и замените его содержимое следующим кодом:

    ```csharp
    using System;
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;

    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    using Newtonsoft.Json;

    namespace pisample
    {
      class Program
      {
        static string DeviceConnectionString = "{your device connection string}";
        static DeviceClient Client = null;
        static TwinCollection reportedProperties = new TwinCollection();
        static CancellationTokenSource cts;
        static double baseTemperature = 60;
        static double basePressure = 500;
        static double baseHumidity = 50;
        static void Main(string[] args)
        {
          Console.WriteLine("Raspberry Pi Azure IoT Central example");

          try
          {
            InitClient();
            SendDeviceProperties();

            cts = new CancellationTokenSource();
            SendTelemetryAsync(cts.Token);

            Console.WriteLine("Wait for settings update...");
            Client.SetDesiredPropertyUpdateCallbackAsync(HandleSettingChanged, null).Wait();
            Console.ReadKey();
            cts.Cancel();
          }
          catch (Exception ex)
          {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
          }
        }

        public static void InitClient()
        {
          try
          {
            Console.WriteLine("Connecting to hub");
            Client = DeviceClient.CreateFromConnectionString(DeviceConnectionString, TransportType.Mqtt);
          }
          catch (Exception ex)
          {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
          }
        }

        public static async void SendDeviceProperties()
        {
          try
          {
            Console.WriteLine("Sending device properties:");
            Random random = new Random();
            TwinCollection telemetryConfig = new TwinCollection();
            reportedProperties["dieNumber"] = random.Next(1, 6);
            Console.WriteLine(JsonConvert.SerializeObject(reportedProperties));

            await Client.UpdateReportedPropertiesAsync(reportedProperties);
          }
          catch (Exception ex)
          {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
          }
        }

        private static async void SendTelemetryAsync(CancellationToken token)
        {
          try
          {
            Random rand = new Random();

            while (true)
            {
              double currentTemperature = baseTemperature + rand.NextDouble() * 20;
              double currentPressure = basePressure + rand.NextDouble() * 100;
              double currentHumidity = baseHumidity + rand.NextDouble() * 20;

              var telemetryDataPoint = new
              {
                humidity = currentHumidity,
                pressure = currentPressure,
                temp = currentTemperature
              };
              var messageString = JsonConvert.SerializeObject(telemetryDataPoint);
              var message = new Message(Encoding.ASCII.GetBytes(messageString));

              token.ThrowIfCancellationRequested();
              await Client.SendEventAsync(message);

              Console.WriteLine("{0} > Sending telemetry: {1}", DateTime.Now, messageString);

              await Task.Delay(1000);
            }
          }
          catch (Exception ex)
          {
            Console.WriteLine();
            Console.WriteLine("Intentional shutdown: {0}", ex.Message);
          }
        }

        private static async Task HandleSettingChanged(TwinCollection desiredProperties, object userContext)
        {
          try
          {
            Console.WriteLine("Received settings change...");
            Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));

            string setting = "fanSpeed";
            if (desiredProperties.Contains(setting))
            {
              // Act on setting change, then
              AcknowledgeSettingChange(desiredProperties, setting);
            }
            setting = "setVoltage";
            if (desiredProperties.Contains(setting))
            {
              // Act on setting change, then
              AcknowledgeSettingChange(desiredProperties, setting);
            }
            setting = "setCurrent";
            if (desiredProperties.Contains(setting))
            {
              // Act on setting change, then
              AcknowledgeSettingChange(desiredProperties, setting);
            }
            setting = "activateIR";
            if (desiredProperties.Contains(setting))
            {
              // Act on setting change, then
              AcknowledgeSettingChange(desiredProperties, setting);
            }
            await Client.UpdateReportedPropertiesAsync(reportedProperties);
          }

          catch (Exception ex)
          {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
          }
        }

        private static void AcknowledgeSettingChange(TwinCollection desiredProperties, string setting)
        {
          reportedProperties[setting] = new
          {
            value = desiredProperties[setting]["value"],
            status = "completed",
            desiredVersion = desiredProperties["$version"],
            message = "Processed"
          };
        }
      }
    }
    ```

    > [!NOTE]
    > Обновите заполнитель `{your device connection string}` на следующем шаге.

## <a name="run-your-net-application"></a>Запуск приложения .NET

Добавьте строку подключения к устройству в код, чтобы устройство выполнило проверку подлинности в Azure IoT Central. Следуйте указаниям, приведенным к [создать строку подключения устройства](concepts-connectivity.md#get-a-connection-string) с помощью **идентификатор области**, **идентификатор устройства**, и **первичный ключ** вы создали Обратите внимание на то из ранее.

1. Замените `{your device connection string}` в **Program.cs** файла в строке подключения, был создан.

1. В среде командной строки выполните следующую команду:

   ```cmd/sh
   dotnet restore
   dotnet publish -r linux-arm
   ```

1. Скопируйте папку `pisample\bin\Debug\netcoreapp2.1\linux-arm\publish` на устройство Raspberry Pi. Вы можете использовать команду **scp**, чтобы скопировать файлы, например:

    ```cmd/sh
    scp -r publish pi@192.168.0.40:publish
    ```

    Дополнительные сведения см. в разделе, посвященном [удаленному доступу Raspberry Pi](https://www.raspberrypi.org/documentation/remote-access/).

1. Войдите в систему на устройстве Raspberry Pi и выполните следующие команды в оболочке.

    ```cmd/sh
    sudo apt-get update
    sudo apt-get install libc6 libcurl3 libgcc1 libgssapi-krb5-2 liblttng-ust0 libstdc++6 libunwind8 libuuid1 zlib1g
    ```

1. В Raspberry Pi выполните следующие команды:

    ```cmd/sh
    cd publish
    chmod 777 pisample
    ./pisample
    ```

    ![Запуск программы](./media/howto-connect-raspberry-pi-csharp/device_begin.png)

1. В приложении Azure IoT Central вы увидите, как код, выполняющийся в Raspberry Pi, взаимодействует с приложением:

   * На странице **Measurements** (Измерения) для реального устройства можно просмотреть данные телеметрии.
   * На странице **Свойства** можно просмотреть значение передаваемого свойства **Серийный номер**.
   * На странице **Параметры** можно изменить различные параметры Raspberry Pi (например, напряжение и скорость вращения вентилятора).

     На следующем снимке экрана показано, как Raspberry Pi получает изменение параметра:

     ![Получение изменения параметра в Raspberry Pi](./media/howto-connect-raspberry-pi-csharp/device_switch.png)

## <a name="raspberry-pi-device-template-details"></a>Raspberry Pi шаблона подробной информации

Приложение, созданное на основе шаблона приложения **Sample Devkits** (Образец Devkits), включает в себя шаблон приложения **Raspberry Pi** со следующими характеристиками:

### <a name="telemetry-measurements"></a>Измерения телеметрии

| Имя поля     | Units  | Минимальная | Максимальная | Число десятичных знаков |
| -------------- | ------ | ------- | ------- | -------------- |
| Влажность       | %      | 0       | 100     | 0              |
| temp           | °C     | –40     | 120     | 0              |
| pressure       | гПа    | 260     | 1260    | 0              |
| magnetometerX  | мГс | –1000   | 1000    | 0              |
| magnetometerY  | мГс | –1000   | 1000    | 0              |
| magnetometerZ  | мГс | –1000   | 1000    | 0              |
| accelerometerX | mg     | –2000   | 2000    | 0              |
| accelerometerY | mg     | –2000   | 2000    | 0              |
| accelerometerZ | mg     | –2000   | 2000    | 0              |
| gyroscopeX     | милиградусов/с   | –2000   | 2000    | 0              |
| gyroscopeY     | милиградусов/с   | –2000   | 2000    | 0              |
| gyroscopeZ     | милиградусов/с   | –2000   | 2000    | 0              |

### <a name="settings"></a>Параметры

Числовые параметры

| Отображаемое имя | Имя поля | Units | Число десятичных знаков | Минимальная | Максимальная | Initial |
| ------------ | ---------- | ----- | -------------- | ------- | ------- | ------- |
| Напряжение      | setVoltage | В | 0              | 0       | 240     | 0       |
| Текущее значение      | setCurrent | Амперы  | 0              | 0       | 100     | 0       |
| Скорость вращения вентилятора    | fanSpeed   | Об/мин   | 0              | 0       | 1000    | 0       |

Параметры переключения

| Отображаемое имя | Имя поля | Включение текста | Отключение текста | Initial |
| ------------ | ---------- | ------- | -------- | ------- |
| IR           | activateIR | ВКЛ      | ВЫКЛ.      | Отключить     |

### <a name="properties"></a>properties

| type            | Отображаемое имя | Имя поля | Тип данных |
| --------------- | ------------ | ---------- | --------- |
| Свойство устройства | Серийный номер   | dieNumber  | number    |
| текст            | Расположение     | location   | Н/Д       |

## <a name="next-steps"></a>Дальнейшие действия

Теперь, когда вы узнали, как подключение устройства Raspberry Pi к приложению Azure IoT Central, ниже приведены предлагаемые дальнейшие действия.

* [Подключить универсального клиентское приложение Node.js для Azure IoT Central](howto-connect-nodejs.md)
