---
title: Руководство по разработке модулей C# для Linux (Azure IoT Edge) | Документация Майкрософт
description: В этом руководстве показано, как создать модуль IoT Edge c кодом C# и развернуть его на пограничном устройстве Linux IoT Edge.
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/23/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc, seodec18
ms.openlocfilehash: 7714d065d8ac449d10d9b022005e7799f8280bda
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/18/2019
ms.locfileid: "71104458"
---
# <a name="tutorial-develop-a-c-iot-edge-module-for-linux-devices"></a>Руководство по Разработка модулей IoT Edge на языке C# для устройств Linux

Для разработки и развертывания кода C# на устройствах Linux с Azure IoT Edge используйте Visual Studio Code. 

Вы можете использовать модули Azure IoT Edge для развертывания кода, который реализует бизнес-логику непосредственно на устройствах IoT Edge. В этом руководстве рассматриваются создание и развертывание модуля IoT Edge, который фильтрует данные датчика. Вы будете использовать имитированное устройство IoT Edge, созданное с помощью процедуры развертывания Azure IoT Edge на имитированном устройстве, описанной в кратких руководствах для [Windows](quickstart.md) или [Linux](quickstart-linux.md). Из этого руководства вы узнаете, как выполнять следующие задачи:    

> [!div class="checklist"]
> * Создать модуль IoT Edge на основе пакета SDK .NET Core 2.1 с помощью Visual Studio Code.
> * Использовать Visual Studio Code и Docker для создания образа Docker и его публикации в реестре.
> * Развертывать модуль на устройстве IoT Edge.
> * Просматривать сформированные данные.

Модуль IoT Edge, создаваемый в этом руководстве, фильтрует данные температуры, которые создаются устройством. Оно отправляет сообщения, только если температура превышает заданное пороговое значение. Такой тип пограничного анализа удобен для сокращения объема данных, передаваемых в облако и сохраняемых в нем. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="solution-scope"></a>Область действия решения

Этот учебник демонстрирует разработку модуля на **C#** с помощью **Visual Studio Code** и его развертывание на **устройстве Linux**. Если вы разрабатываете модули для устройств Windows, вам нужен другой учебник [по разработке модуля IoT Edge на C# для устройств с Windows](tutorial-csharp-module-windows.md).

В приведенной ниже таблице перечислены возможные варианты для разработки и развертывания модулей C# для Linux. 

| C# | Visual Studio Code | Visual Studio | 
| -- | ------------------ | ------------- |
| **Linux AMD64** | ![Модули C# для LinuxAMD64 в VS Code](./media/tutorial-c-module/green-check.png) | ![Модули C# для LinuxAMD64 в Visual Studio](./media/tutorial-c-module/green-check.png) |
| **Linux ARM32** | ![Модули C# для LinuxARM32 в VS Code](./media/tutorial-c-module/green-check.png) | ![Модули C# для LinuxARM64 в Visual Studio](./media/tutorial-c-module/green-check.png) |

>[!NOTE]
>Поддержка устройств Linux ARM64 предоставляется в [общедоступной предварительной версии](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Подробные сведения см. в статье [Develop and debug ARM64 IoT Edge modules in Visual Studio Code (preview)](https://devblogs.microsoft.com/iotdev/develop-and-debug-arm64-iot-edge-modules-in-visual-studio-code-preview) (Разработка и отладка модулей IoT Edge для устройств ARM64 в Visual Studio Code (предварительная версия)).

## <a name="prerequisites"></a>Предварительные требования

Перед началом работы с этим учебником вы должны были настроить среду разработки, выполнив инструкции, приведенные [в этой статье](tutorial-develop-for-linux.md). После работы с этим учебником у вас должны быть готовы все необходимые компоненты: 

* [Центр Интернета вещей](../iot-hub/iot-hub-create-through-portal.md) ценовой категории "Бесплатный" или "Стандартный" в Azure.
* [Устройство Linux, на котором выполняется Azure IoT Edge](quickstart-linux.md).
* реестр контейнеров, например [Реестр контейнеров Azure](https://docs.microsoft.com/azure/container-registry/);
* средство [Visual Studio Code](https://code.visualstudio.com/), настроенное с помощью [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools);
* выпуск [Docker CE](https://docs.docker.com/install/), настроенный для выполнения контейнеров Linux.

Для работы с этими учебниками подготовьте дополнительные необходимые компоненты на компьютере для разработки: 

* [C# для расширения Visual Studio Code (на платформе OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).
* [Пакет SDK для .NET Core 2.1](https://www.microsoft.com/net/download).

## <a name="create-a-module-project"></a>Создание проекта модуля

Описанный ниже процесс позволяет создать проект модуля IoT Edge для языка C# с использованием Visual Studio Code и расширения Azure IoT Tools. После создания шаблона проекта добавьте новый код, чтобы модуль фильтровал сообщения по их сообщаемым свойствам. 

### <a name="create-a-new-project"></a>Создание нового проекта

Создайте шаблон решения C#, который можно настроить с помощью собственного кода. 

1. В Visual Studio Code выберите **Представление** > **Палитра команд** для открытия палитры команд VS Code. 

2. В палитре команд введите и выполните команду **Azure: Sign in** (Azure: войти). Следуйте инструкциям, чтобы войти в свою учетную запись Azure. Если вход был выполнен, то этот шаг можно пропустить.

3. В палитре команд введите и выполните команду **Azure IoT Edge: New IoT Edge Solution** (Azure IoT Edge: создать решение IoT Edge). Следуйте инструкциям на экране в палитре команд для создания решения.

   | Поле | Значение |
   | ----- | ----- |
   | Выбор папки | Выберите расположение на компьютере разработчика для VS Code, чтобы создать файлы решения. |
   | Введите название решения. | Введите описательное имя решения или примите имя по умолчанию **EdgeSolution**. |
   | Выбор шаблона модуля | Выберите **модуль C#** . |
   | Указание имени модуля | Присвойте модулю имя **CSharpModule**. |
   | Указание репозитория изображений Docker для модуля | Репозиторий изображений включает в себя имя реестра контейнеров и имя образа контейнера. Образ контейнера предварительно заполняется именем, которое вы указали на последнем шаге. Замените **localhost:5000** на значение сервера входа из реестра контейнеров Azure. Вы можете извлечь сервер входа на странице "Обзор" реестра контейнеров на портале Azure. <br><br>Окончательный репозиторий образа выглядит так: \<имя_регистра\>.azurecr.io/csharpmodule. |
 
   ![Выбор репозитория образа Docker](./media/tutorial-csharp-module/repository.png)


### <a name="add-your-registry-credentials"></a>Добавление учетных данных реестра

Файл среды хранит учетные данные для реестра контейнеров и совместно использует их со средой выполнения IoT Edge. Среде выполнения нужны эти учетные данные, чтобы извлечь частный образ на устройство IoT Edge. Используйте учетные данные из раздела **Ключи доступа** в реестре контейнеров Azure. 

1. Откройте в обозревателе VS Code файл с расширением **ENV**. 
2. Обновите поля, указав **имя пользователя** и **пароль**, скопированные из реестра контейнера Azure. 
3. Сохраните этот файл. 

### <a name="select-your-target-architecture"></a>Выбор целевой архитектуры

Сейчас Visual Studio Code позволяет разрабатывать модули C# для устройств Linux AMD64 и Linux ARM32v7. Для каждого решения вам нужно выбрать одну целевую платформу, так как сборка и запуск контейнера для разных архитектур различается. По умолчанию используется Linux AMD64. 

1. Откройте палитру команд и выполните поиск **Azure IoT Edge: Set Default Target Platform for Edge Solution** (Azure IoT Edge: установить целевую платформу по умолчанию для решения Edge) или выберите значок ярлыка на боковой панели в нижней части окна. 

2. В палитре команд выберите целевую архитектуру из списка параметров. В рамках этого руководства мы используем в качестве устройства IoT Edge виртуальную машину Ubuntu. Поэтому сохраним значение по умолчанию **amd64**. 

### <a name="update-the-module-with-custom-code"></a>Обновление модуля с помощью пользовательского кода

1. В обозревателе VS Code выберите **modules** > **NodeModule** > **app.js**.

2. В верхней части пространства имен **CSharpModule** добавьте три **инструкции** для типов, которые будут использоваться позже.

    ```csharp
    using System.Collections.Generic;     // For KeyValuePair<>
    using Microsoft.Azure.Devices.Shared; // For TwinCollection
    using Newtonsoft.Json;                // For JsonConvert
    ```

3. Добавьте переменную **temperatureThreshold** к классу **Program**. Эта переменная устанавливает значение, которое должно быть измеренной температурой, чтобы данные были отправлены в Центр Интернета вещей. 

    ```csharp
    static int temperatureThreshold { get; set; } = 25;
    ```

4. Добавьте **MessageBody**, **Machine** и **Ambient** к классу **Program**. Эти классы определяют ожидаемую схему текста входящего сообщения.

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

5. Найдите функцию **Init**. Эта функция создает и настраивает объект **ModuleClient**, позволяющий модулю подключаться к локальной среде выполнения Azure IoT Edge для отправки и получения сообщений. После создания **ModuleClient** код считывает значение **temperatureThreshold** из требуемых свойств двойника модуля. Этот код регистрирует обратный вызов для получения сообщений из концентратора IoT Edge через **input1** конечной точки. Замените метод  **SetInputMessageHandlerAsync** новым и добавьте метод **SetDesiredPropertyUpdateCallbackAsync** для обновления нужного свойства. Чтобы внести это изменение, замените последнюю строку метода **Init** следующим кодом:

    ```csharp
    // Register a callback for messages that are received by the module.
    // await ioTHubModuleClient.SetInputMessageHandlerAsync("input1", PipeMessage, iotHubModuleClient);

    // Read the TemperatureThreshold value from the module twin's desired properties
    var moduleTwin = await ioTHubModuleClient.GetTwinAsync();
    await OnDesiredPropertiesUpdate(moduleTwin.Properties.Desired, ioTHubModuleClient);
    
    // Attach a callback for updates to the module twin's desired properties.
    await ioTHubModuleClient.SetDesiredPropertyUpdateCallbackAsync(OnDesiredPropertiesUpdate, null);

    // Register a callback for messages that are received by the module.
    await ioTHubModuleClient.SetInputMessageHandlerAsync("input1", FilterMessages, ioTHubModuleClient);
    ```

6. Добавьте метод **onDesiredPropertiesUpdate** к классу **Program**. Этот метод принимает изменения требуемых свойств из двойника модуля и соответствующим образом изменяет переменную **temperatureThreshold**. У каждого модуля есть собственный модуль-двойник, что позволяет настроить код, выполняемый в модуле, непосредственно из облака.

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

7. Замените метод **PipeMessage** методом **FilterMessages**. Этот метод вызывается каждый раз, когда модуль получает сообщение из центра IoT Edge. Он отфильтровывает сообщения о температуре ниже порогового значения, настроенного с помощью двойника модуля. Он также добавляет свойство **MessageType** в сообщение со значением **Alert**. 

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
                using (var filteredMessage = new Message(messageBytes))
                {
                    foreach (KeyValuePair<string, string> prop in message.Properties)
                    {
                        filteredMessage.Properties.Add(prop.Key, prop.Value);
                    }

                    filteredMessage.Properties.Add("MessageType", "Alert");
                    await moduleClient.SendEventAsync("output1", filteredMessage);
                }
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

9. В обозревателе VS Code откройте файл **deployment.template.json** в рабочей области решения IoT Edge. 

10. Добавьте двойник модуля **CSharpModule** в манифест развертывания. Вставьте следующее содержимое JSON в нижней части раздела **modulesContent** после двойника модуля **$edgeHub**: 

    ```json
       "CSharpModule": {
           "properties.desired":{
               "TemperatureThreshold":25
           }
       }
    ```

    ![Добавление двойника модуля в шаблон развертывания](./media/tutorial-csharp-module/module-twin.png)

11. Сохраните файл deployment.template.json.


## <a name="build-and-push-your-module"></a>Сборка и отправка модуля

В предыдущем разделе вы создали решение IoT Edge и добавили код в CSharpModule, который будет отфильтровывать сообщения, в которых указанная температура компьютера находится в рамках допустимых границ. Теперь необходимо создать решение в качестве образа контейнера и передать его в реестр контейнеров.

1. Откройте интегрированный терминал VS Code, выбрав **Вид** > **Терминал**.

1. Войдите в Docker, введя следующую команду в окне терминала. Выполните вход в систему, используя имя пользователя, пароль и сервер входа из реестра контейнеров Azure. Вы можете получить эти значения в разделе **Ключи доступа** в разделе реестра на портале Azure.
     
   ```bash
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Возможно, появится предупреждение системы безопасности, в котором рекомендуется использовать `--password-stdin`. Для рабочих сценариев это лучшая методика, но мы не будем рассматривать ее в этом учебнике. Дополнительные сведения см. в описании команды [docker login](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) в справочнике.

2. В обозревателе VS Code щелкните правой кнопкой мыши файл **deployment.template.json** и выберите **Build and Push IoT Edge solution** (Создать и отправить решение IoT Edge).

   Эта команда сборки и отправки позволяет запустить три операции. Во-первых,в решении создается папка с именем **config**, которая содержит полный манифест развертывания на основе информации из шаблона развертывания и других файлов решения. Во-вторых, выполняется `docker build` для сборки образа контейнера на основе подходящего файла dockerfile для целевой архитектуры. В-третьих, она выполняет команду `docker push` для отправки репозитория образов в реестр контейнеров.

## <a name="deploy-and-run-the-solution"></a>Развертывание и запуск решения

Разверните проект модуля на устройство IoT Edge с помощью обозревателя Visual Studio Code и расширения Azure IoT Tools. У вас уже есть манифест развертывания, подготовленный для вашего сценария (файл **deployment.json** в папке config). Теперь вам осталось выбрать устройство для получения развертывания.

Убедитесь, что устройство IoT Edge работает.

1. В обозревателе Visual Studio Code разверните раздел **Azure IoT Hub Devices** (Устройства Центра Интернета вещей Azure), чтобы отобразился список устройство Интернета вещей.

2. Щелкните имя устройства IoT Edge правой кнопкой мыши, а затем выберите **Create Deployment for Single Device** (Создание развертывания для одного устройства). 

5. Выберите файл **deployment.json** в папке **config** и щелкните **Select Edge Deployment Manifest** (Выбрать манифест развертывания Edge). Не используйте файл deployment.template.json. 

6. Нажмите кнопку "Обновить". Появится новый запущенный модуль **CSharpFunction**, модуль **SimulatedTemperatureSensor**, а также **$edgeAgent** и **$edgeHub**.  

## <a name="view-generated-data"></a>Просмотр сформированных данных

Когда вы примените манифест развертывания к устройству IoT Edge, в среде выполнения IoT Edge на устройстве начнется сбор сведений о новом развертывании и выполнение операций. Работа всех выполняющихся на устройстве модулей, которые не включены в манифест развертывания, будет остановлена. А модули, которых нет на устройстве, будут запущены.

Сведения о состоянии устройства IoT Edge можно просмотреть в разделе **Azure IoT Hub Devices** (Устройства Центра Интернета вещей Azure) в обозревателе Visual Studio Code. Разверните раздел со сведениями об устройстве, чтобы просмотреть список развернутых и запущенных модулей.

1. В обозревателе Visual Studio Code щелкните имя устройства IoT Edge правой кнопкой мыши и выберите **Start Monitoring Built-in Event Endpoint** (Начать мониторинг строенной конечной точки событий).

2. Просмотрите сообщения, поступающие в Центр Интернета вещей. Для поступления сообщений может потребоваться некоторое время, так как устройство IoT Edge должно сначала получить новое развертывание и запустить все модули. После этого измененный нами код CModule ожидает, пока температура компьютера не достигнет 25 градусов, и лишь затем отправляет сообщения. С помощью кода также присваивается тип **Оповещение** всем сообщениям со сведениями о достижении порога температуры. 

   ![Просмотр поступающих сообщений в Центре Интернета вещей](./media/tutorial-csharp-module/view-d2c-message.png)
 
## <a name="edit-the-module-twin"></a>Изменение двойника модуля

Мы использовали в манифесте развертывания двойник модуля CSharpModule, чтобы задать порог температуры 25 градусов. С помощью двойника модуля вы можете изменять функциональные возможности без обновления кода модуля.

1. В Visual Studio Code разверните подробные сведения об устройстве IoT Edge, чтобы просмотреть список выполняющихся модулей. 

2. Щелкните **CSharpModule** правой кнопкой мыши и выберите **Edit module twin** (Изменить двойник модуля). 

3. Найдите **TemperatureThreshold** в списке требуемых свойств. Измените его значение, увеличив температуру на 5 или 10 градусов относительно последней сообщаемой температуры. 

4. Сохраните файл двойника модуля.

5. Щелкните правой кнопкой мыши в любой точки на панели редактирования двойника модуля и выберите **Update module twin** (Обновление двойника модуля). 

5. Выполните мониторинг сообщений, поступающих с устройства в облако. Вы увидите, что поток сообщений прекратится, пока не будет достигнут новый порог температуры. 

## <a name="clean-up-resources"></a>Очистка ресурсов 

Если вы планируете перейти к следующей рекомендуемой статье, можно сохранить созданные и повторно используемые ресурсы и конфигурации. Это же устройство IoT Edge также можно использовать в качестве тестового устройства. 

В противном случае вы можете удалить локальные конфигурации и ресурсы Azure, созданные в рамках этой статьи, чтобы избежать расходов. 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]


## <a name="next-steps"></a>Дополнительная информация

В этом руководстве вы создали модуль IoT Edge, который содержит код для фильтрации необработанных данных, созданных вашим устройством IoT Edge. Дополнительные сведения см. в статьях о [разработке модулей IoT Edge](module-development.md) и [разработке модулей с помощью Visual Studio Code](how-to-vs-code-develop-module.md). Перейдите к следующим руководствам, чтобы узнать, как Azure IoT Edge поможет развернуть облачные службы Azure для обработки данных на пограничном устройстве.

> [!div class="nextstepaction"]
> [Функции](tutorial-deploy-function.md)
> [Stream Analytics](tutorial-deploy-stream-analytics.md)
> [Машинное обучение](tutorial-deploy-machine-learning.md)
> [Служба пользовательского визуального распознавания](tutorial-deploy-custom-vision.md)
