---
title: Руководство по разработке модулей C# для Windows (Azure IoT Edge) | Документация Майкрософт
description: В этом руководстве показано, как создать модуль IoT Edge c кодом C# и развернуть его на устройстве Windows IoT Edge.
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/23/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: 122028217a78463fa2ceaed63248a74257206345
ms.sourcegitcommit: f9448a4d87226362a02b14d88290ad6b1aea9d82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/07/2019
ms.locfileid: "66808774"
---
# <a name="tutorial-develop-a-c-iot-edge-module-for-windows-devices"></a>Руководство по Разработка модулей IoT Edge на языке C# для устройств Windows

Для разработки и развертывания кода C# на устройствах Windows с Azure IoT Edge используйте Visual Studio. 

Вы можете использовать модули Azure IoT Edge для развертывания кода, который реализует бизнес-логику непосредственно на устройствах IoT Edge. В этом руководстве рассматриваются создание и развертывание модуля IoT Edge, который фильтрует данные датчика. Из этого руководства вы узнаете, как выполнять следующие задачи:    

> [!div class="checklist"]
> * Создать модуль IoT Edge на основе пакета SDK C# с помощью Visual Studio.
> * Использовать Visual Studio и Docker для создания образа Docker и его публикации в реестре.
> * Развертывать модуль на устройстве IoT Edge.
> * Просматривать сформированные данные.

Модуль IoT Edge, создаваемый в этом руководстве, фильтрует данные температуры, которые создаются устройством. Оно отправляет сообщения, только если температура превышает заданное пороговое значение. Такой тип пограничного анализа удобен для сокращения объема данных, передаваемых в облако и сохраняемых в нем. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="solution-scope"></a>Область действия решения

В этом руководстве показано, как выполнить разработку модуля на языке **C#** с помощью **Visual Studio 2019** и его развертывание на **устройстве Windows**. Если вы разрабатываете модули для устройств Linux, обратитесь к этому руководству [по разработке модуля IoT Edge на языке C# для устройств Linux](tutorial-csharp-module.md). 

В следующей таблице перечислены возможные варианты для разработки и развертывания модулей C для устройств с Windows: 

| C# | Visual Studio Code | Visual Studio 2017 или 2019 | 
| -- | ------------------ | ------------------ |
| **Разработка Windows AMD64** | ![Разработка модулей C# для WinAMD64 в VS Code](./media/tutorial-c-module/green-check.png) | ![Разработка модулей C# для WinAMD64 в Visual Studio](./media/tutorial-c-module/green-check.png) |
| **Отладка Windows AMD64** |   | ![Отладка модулей C# для WinAMD64 в Visual Studio](./media/tutorial-c-module/green-check.png) |

## <a name="prerequisites"></a>Предварительные требования

Перед началом работы с этим руководством вам нужно настроить среду разработки, выполнив инструкции, приведенные в статье о [разработке модуля IoT Edge для устройств Windows](tutorial-develop-for-windows.md). После работы с этим руководством у вас должны быть готовы все необходимые компоненты: 

* [Центр Интернета вещей](../iot-hub/iot-hub-create-through-portal.md) ценовой категории "Бесплатный" или "Стандартный" в Azure.
* [Устройство с Windows, на котором выполняется Azure IoT Edge](quickstart.md).
* реестр контейнеров, например [Реестр контейнеров Azure](https://docs.microsoft.com/azure/container-registry/);
* Настроенная среда [Visual Studio 2019](https://docs.microsoft.com/visualstudio/install/install-visual-studio) с расширением [Azure IoT Edge Tools](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools).
* Приложение [Docker CE](https://docs.docker.com/install/), настроенное для запуска контейнеров Windows.

> [!TIP]
> Если вы используете Visual Studio 2017 (15.7 или последующей версии), скачайте и установите [средства Azure IoT Edge (предварительная версия)](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) для этой версии из Visual Studio Marketplace.

## <a name="create-a-module-project"></a>Создание проекта модуля

Описанный ниже процесс позволяет создать проект модуля IoT Edge с использованием Visual Studio и расширения Azure IoT Edge Tools. После создания шаблона проекта добавьте новый код, чтобы модуль фильтровал сообщения по их сообщаемым свойствам. 

### <a name="create-a-new-project"></a>Создание нового проекта

Расширение Azure IoT Edge Tools предоставляет шаблоны проектов для всех поддерживаемых языков модулей IoT Edge в Visual Studio. Эти шаблоны содержат все файлы и код, необходимые для развертывания рабочего модуля для тестирования IoT Edge, а также станут отправной точкой для настройки шаблона в соответствии с вашей бизнес-логикой. 

1. Запустите Visual Studio 2019 и выберите **Создать проект**.

2. В окне нового проекта найдите проект **IoT Edge** и выберите для него тип **Azure IoT Edge (Windows amd64)** . Щелкните **Далее**. 

   ![Создание проекта Azure IoT Edge](./media/tutorial-csharp-module-windows/new-project.png)

3. В окне настроек проекта присвойте проекту и решению описательное имя, например **CSharpTutorialApp**. Щелкните **Создать**, чтобы создать проект. 

   ![Настройка нового проекта Azure IoT Edge](./media/tutorial-csharp-module-windows/configure-project.png)

4. В приложении и окне модуля IoT Edge настройте проект, указав следующие значения: 

   | Поле | Значение |
   | ----- | ----- |
   | Выберите шаблон | Выберите **Модуль C#** . | 
   | Имя проекта модуля | Присвойте модулю имя **CSharpModule**. | 
   | Репозиторий образа Docker | Репозиторий изображений включает в себя имя реестра контейнеров и имя образа контейнера. Образ контейнера предварительно заполняется значением имени проекта модуля. Замените **localhost:5000** на значение сервера входа из реестра контейнеров Azure. Вы можете извлечь сервер входа на странице "Обзор" реестра контейнеров на портале Azure. <br><br> Окончательный репозиторий образа выглядит так: \<имя_регистра\>.azurecr.io/csharpmodule. |

   ![Настройка целевого устройства, типа модуля и реестра контейнеров для проекта](./media/tutorial-csharp-module-windows/add-application-and-module.png)

5. Нажмите кнопку **ОК**, чтобы применить изменения. 

### <a name="add-your-registry-credentials"></a>Добавление учетных данных реестра

Манифест развертывания совместно использует учетные данные для реестра контейнеров со средой выполнения IoT Edge. Среде выполнения нужны эти учетные данные, чтобы извлечь частный образ на устройство IoT Edge. Используйте учетные данные из раздела **Ключи доступа** в реестре контейнеров Azure. 

1. В обозревателе решений Visual Studio откройте файл **deployment.template.json**. 

2. Найдите свойство **registryCredentials** в требуемых свойствах $edgeAgent. 

3. Обновите свойство, указав свои учетные данные в следующем формате: 

   ```json
   "registryCredentials": {
     "<registry name>": {
       "username": "<username>",
       "password": "<password>",
       "address": "<registry name>.azurecr.io"
     }
   }
   ```

4. Сохраните файл deployment.template.json. 

### <a name="update-the-module-with-custom-code"></a>Обновление модуля с помощью пользовательского кода

Код стандартного модуля получает сообщения из очереди входящих сообщений и передает их через очередь исходящих сообщений. Давайте добавим еще немного кода, чтобы модуль обрабатывал сообщения на границе до передачи в Центр Интернета вещей. Обновите модуль таким образом, чтобы он анализировал данные о температуре, получаемые в каждом сообщении, и отправлял в Центр Интернета вещей только сообщения со сведениями о том, что температура превышает определенный порог. 

1. В Visual Studio откройте **CSharpModule** > **Program.cs**.

2. В верхней части пространства имен **CSharpModule** добавьте три **инструкции** для типов, которые будут использоваться позже.

    ```csharp
    using System.Collections.Generic;     // For KeyValuePair<>
    using Microsoft.Azure.Devices.Shared; // For TwinCollection
    using Newtonsoft.Json;                // For JsonConvert
    ```

3. Добавьте переменную **temperatureThreshold** к классу **Program** после переменной счетчика. Для переменной temperatureThreshold указывается значение измеренной температуры, при превышении которого данные отправляются в Центр Интернета вещей. 

    ```csharp
    static int temperatureThreshold { get; set; } = 25;
    ```

4. Добавьте классы **MessageBody**, **Machine** и **Ambient** к классу **Program** после объявления переменных. Эти классы определяют ожидаемую схему текста входящего сообщения.

    ```csharp
    class MessageBody
    {
        public Machine machine {get;set;}
        public Ambient ambient {get; set;}
        public string timeCreated {get; set;}
    }
    class Machine
    {
        public double temperature {get; set;}
        public double pressure {get; set;}         
    }
    class Ambient
    {
        public double temperature {get; set;}
        public int humidity {get; set;}         
    }
    ```

5. Найдите метод **Init**. Этот метод создает и настраивает объект **ModuleClient**, позволяющий модулю подключаться к локальной среде выполнения Azure IoT Edge для отправки и получения сообщений. Этот код также регистрирует обратный вызов для получения сообщений из центра IoT Edge через конечную точку **input1**.

   Замените весь метод Init приведенным ниже кодом.
   
   ```csharp
   static async Task Init()
   {
       AmqpTransportSettings amqpSetting = new AmqpTransportSettings(TransportType.Amqp_Tcp_Only);
       ITransportSettings[] settings = { amqpSetting };

       // Open a connection to the Edge runtime
       ModuleClient ioTHubModuleClient = await ModuleClient.CreateFromEnvironmentAsync(settings);
       await ioTHubModuleClient.OpenAsync();
       Console.WriteLine("IoT Hub module client initialized.");

       // Read the TemperatureThreshold value from the module twin's desired properties
       var moduleTwin = await ioTHubModuleClient.GetTwinAsync();
       await OnDesiredPropertiesUpdate(moduleTwin.Properties.Desired, ioTHubModuleClient);

       // Attach a callback for updates to the module twin's desired properties.
       await ioTHubModuleClient.SetDesiredPropertyUpdateCallbackAsync(OnDesiredPropertiesUpdate, null);

       // Register a callback for messages that are received by the module.
       await ioTHubModuleClient.SetInputMessageHandlerAsync("input1", FilterMessages, ioTHubModuleClient);
   }
   ```
   
   Этот обновленный метод Init по-прежнему устанавливает подключение к среде выполнения IoT Edge с помощью ModuleClient, а также добавляет новые функции. Он считывает требуемые свойства двойника модуля, чтобы получить значение **temperatureThreshold**. Затем он создает обратный вызов, который прослушивает все будущие обновления требуемых свойств двойника модуля. С помощью этого обратного вызова можно удаленно обновить пороговое значение температуры в двойнике модуля, и эти изменения будут включены в модуль. 

   Обновленный метод Init также изменяет существующий метод **SetInputMessageHandlerAsync**. В примере кода входящие сообщения на *input1* обрабатываются с помощью функции *PipeMessage*. Но нам нужно изменить ее на функцию *FilterMessages*, которую создадим на следующих этапах. 

6. Добавьте новый метод **onDesiredPropertiesUpdate** к классу **Program**. Этот метод принимает изменения требуемых свойств из двойника модуля и соответствующим образом изменяет переменную **temperatureThreshold**. У каждого модуля есть собственный модуль-двойник, что позволяет настроить код, выполняемый в модуле, непосредственно из облака.

    ```csharp
    static Task OnDesiredPropertiesUpdate(TwinCollection desiredProperties, object userContext)
    {
        try
        {
            Console.WriteLine("Desired property change:");
            Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));

            if (desiredProperties["TemperatureThreshold"]!=null)
                temperatureThreshold = desiredProperties["TemperatureThreshold"];

        }
        catch (AggregateException ex)
        {
            foreach (Exception exception in ex.InnerExceptions)
            {
                Console.WriteLine();
                Console.WriteLine("Error when receiving desired property: {0}", exception);
            }
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error when receiving desired property: {0}", ex.Message);
        }
        return Task.CompletedTask;
    }
    ```

7. Удалите пример метода **PipeMessage** и замените его новым методом **FilterMessages**. Этот метод вызывается каждый раз, когда модуль получает сообщение из центра IoT Edge. Он отфильтровывает сообщения о температуре ниже порогового значения, настроенного с помощью двойника модуля. Он также добавляет свойство **MessageType** в сообщение со значением **Alert**. 

    ```csharp
    static async Task<MessageResponse> FilterMessages(Message message, object userContext)
    {
        var counterValue = Interlocked.Increment(ref counter);
        try
        {
            ModuleClient moduleClient = (ModuleClient)userContext;
            var messageBytes = message.GetBytes();
            var messageString = Encoding.UTF8.GetString(messageBytes);
            Console.WriteLine($"Received message {counterValue}: [{messageString}]");

            // Get the message body.
            var messageBody = JsonConvert.DeserializeObject<MessageBody>(messageString);

            if (messageBody != null && messageBody.machine.temperature > temperatureThreshold)
            {
                Console.WriteLine($"Machine temperature {messageBody.machine.temperature} " +
                    $"exceeds threshold {temperatureThreshold}");
                var filteredMessage = new Message(messageBytes);
                foreach (KeyValuePair<string, string> prop in message.Properties)
                {
                    filteredMessage.Properties.Add(prop.Key, prop.Value);
                }

                filteredMessage.Properties.Add("MessageType", "Alert");
                await moduleClient.SendEventAsync("output1", filteredMessage);
            }

            // Indicate that the message treatment is completed.
            return MessageResponse.Completed;
        }
        catch (AggregateException ex)
        {
            foreach (Exception exception in ex.InnerExceptions)
            {
                Console.WriteLine();
                Console.WriteLine("Error in sample: {0}", exception);
            }
            // Indicate that the message treatment is not completed.
            var moduleClient = (ModuleClient)userContext;
            return MessageResponse.Abandoned;
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
            // Indicate that the message treatment is not completed.
            ModuleClient moduleClient = (ModuleClient)userContext;
            return MessageResponse.Abandoned;
        }
    }
    ```

8. Сохраните файл Program.cs.

9. Откройте файл **deployment.template.json** в папке решения IoT Edge. Этот файл указывает агенту IoT Edge на модуль для развертывания, в нашем случае это **tempSensor** и **CSharpModule**, а центру IoT Edge на то, как маршрутизировать сообщения между ними.

10. Добавьте двойник модуля **CSharpModule** в манифест развертывания. Вставьте следующее содержимое JSON в нижней части раздела **modulesContent** после двойника модуля **$edgeHub**: 

    ```json
       "CSharpModule": {
           "properties.desired":{
               "TemperatureThreshold":25
           }
       }
    ```

    ![Добавление двойника модуля в шаблон развертывания](./media/tutorial-csharp-module-windows/module-twin.png)

11. Сохраните файл deployment.template.json.


## <a name="build-and-push-your-module"></a>Сборка и отправка модуля

В предыдущем разделе вы создали решение IoT Edge и добавили код **CSharpModule**, который будет отфильтровывать сообщения, где указанная температура компьютера ниже допустимого порогового значения. Теперь необходимо создать решение в качестве образа контейнера и передать его в реестр контейнеров. 

1. Используйте следующую команду, чтобы войти в Docker на компьютере разработки. Выполните вход в систему, используя имя пользователя, пароль и сервер входа из реестра контейнеров Azure. Вы можете получить эти значения из раздела **Ключи доступа** в реестре на портале Azure.

   ```cmd
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Возможно, появится предупреждение системы безопасности, в котором рекомендуется использовать `--password-stdin`. Для рабочих сценариев это лучшая методика, но мы не будем рассматривать ее в этом учебнике. Дополнительные сведения см. в описании команды [docker login](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) в справочнике.

2. В обозревателе решений Visual Studio щелкните правой кнопкой мыши имя проекта, который хотите скомпилировать. Имя по умолчанию — **AzureIotEdgeApp1**, и так как вы создаете модуль Windows, должно быть расширение **Windows.Amd64**. 

3. Выберите **Build and Push IoT Edge Modules** (Создание и отправка модулей IoT Edge). 

   Команда сборки и отправки запускает три операции. Во-первых,в решении создается папка с именем **config**, которая содержит полный манифест развертывания на основе информации из шаблона развертывания и других файлов решения. Во-вторых, выполняется `docker build` для сборки образа контейнера на основе подходящего файла dockerfile для целевой архитектуры. В-третьих, выполняется `docker push` для отправки образа в реестр контейнеров. 

## <a name="deploy-modules-to-device"></a>Развертывание модулей на устройстве

Разверните проект модуля на устройство IoT Edge с помощью обозревателя Visual Studio Cloud Explorer и расширения Azure IoT Edge Tools. У вас уже есть манифест развертывания, подготовленный для вашего сценария (файл **deployment.json** в папке config). Теперь вам осталось выбрать устройство для получения развертывания.

Убедитесь, что устройство IoT Edge запущено и работает. 

1. В Visual Studio Cloud Explorer разверните ресурсы, чтобы увидеть список устройств IoT. 

2. Щелкните правой кнопкой мыши имя устройства IoT Edge, на котором необходимо выполнить развертывание. 

3. Выберите **Создать развертывание**.

4. В проводнике выберите файл **deployment.windows amd64** из папки config решения. 

5. Обновите Cloud Explorer для просмотра развернутых модулей, перечисленных для устройства. 

## <a name="view-generated-data"></a>Просмотр сформированных данных

Когда вы примените манифест развертывания к устройству IoT Edge, в среде выполнения IoT Edge на устройстве начнется сбор сведений о новом развертывании и выполнение операций. Работа всех выполняющихся на устройстве модулей, которые не включены в манифест развертывания, будет остановлена. А модули, которых нет на устройстве, будут запущены. 

Расширение IoT Edge Tools можно использовать для просмотра сообщений по мере их поступления в Центр Интернета вещей. 

1. В Visual Studio Cloud Explorer выберите имя устройства IoT Edge. 

2. Из списка **Actions** (Действия) выберите **Start Monitoring Built-in Event Endpoint** (Начать мониторинг встроенной конечной точки событий). 

3. Просмотрите сообщения, поступающие в Центр Интернета вещей. Для поступления сообщений может потребоваться некоторое время, так как измененный нами код CSharpModule ожидает, пока температура компьютера не достигнет 25 градусов, и лишь затем отправляет сообщения. Он также присваивает тип **Предупреждение** всем сообщениям, которые сообщают о достижении порогового значения температуры. 

   ![Просмотр поступающих сообщений в Центре Интернета вещей](./media/tutorial-csharp-module-windows/view-d2c-message.png)

## <a name="edit-the-module-twin"></a>Изменение двойника модуля

Мы использовали двойник модуля CSharpModule, чтобы задать порог температуры 25 градусов. С помощью двойника модуля вы можете изменять функциональные возможности без необходимости обновлять код модуля.

1. В Visual Studio откройте файл **deployment.windows-amd64.json**. (Не файл deployment.template. Если вы не видите манифест развертывания в файле конфигурации в обозревателе решений, щелкните значок **Show all files** (Показать все файлы) на панели инструментов обозревателя.)

2. Найдите двойник модуля CSharpModule и измените значение параметра **temperatureThreshold** на новое, которое будет на 5–10 градусов выше, чем последнее полученное значение температуры. 

3. Сохраните файл **deployment.windows-amd64.json**.

4. Выполните шаги по развертыванию еще раз, чтобы применить к устройству обновленный манифест развертывания. 

5. Выполните мониторинг сообщений, поступающих с устройства в облако. Вы увидите, что поток сообщений прекратится, пока не будет достигнут новый порог температуры. 

## <a name="clean-up-resources"></a>Очистка ресурсов 

Если вы планируете перейти к следующей рекомендуемой статье, можно сохранить созданные и повторно используемые ресурсы и конфигурации. Это же устройство IoT Edge также можно использовать в качестве тестового устройства. 

В противном случае вы можете удалить локальные конфигурации и ресурсы Azure, созданные в рамках этой статьи, чтобы избежать расходов. 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Дополнительная информация

В этом руководстве вы создали модуль IoT Edge с кодом для фильтрации необработанных данных, созданных устройством IoT Edge. Дополнительные сведения о создании собственных модулей см. в статьях [о разработке модулей IoT Edge](module-development.md) и [о разработке модулей с помощью Visual Studio](how-to-visual-studio-develop-module.md). Ознакомьтесь со следующими учебниками, чтобы узнать, как развернуть облачные службы Azure для обработки и анализа данных на пограничном устройстве с помощью Azure IoT Edge.

> [!div class="nextstepaction"]
> [Функции](tutorial-deploy-function.md)
> [Stream Analytics](tutorial-deploy-stream-analytics.md)
> [Машинное обучение](tutorial-deploy-machine-learning.md)
> [Служба пользовательского визуального распознавания](tutorial-deploy-custom-vision.md)
