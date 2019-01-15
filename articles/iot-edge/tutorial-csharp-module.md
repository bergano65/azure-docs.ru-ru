---
title: Руководство по созданию пользовательского модуля C# — Azure IoT Edge | Документация Майкрософт
description: В этом руководстве показано, как создать модуль IoT Edge c кодом C# и развернуть его на граничном устройстве.
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/04/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc, seodec18
ms.openlocfilehash: 7d379be5e85d908424dae76ac148d931d1b7b4b8
ms.sourcegitcommit: d61faf71620a6a55dda014a665155f2a5dcd3fa2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54052212"
---
# <a name="tutorial-develop-a-c-iot-edge-module-and-deploy-to-your-simulated-device"></a>Руководство. Разработка модуля IoT Edge с кодом C# и его развертывание на имитированном устройстве

Вы можете использовать модули Azure IoT Edge для развертывания кода, который реализует бизнес-логику непосредственно на устройствах IoT Edge. В этом руководстве рассматриваются создание и развертывание модуля IoT Edge, который фильтрует данные датчика. Вы будете использовать имитированное устройство IoT Edge, созданное с помощью процедуры развертывания Azure IoT Edge на имитированном устройстве, описанной в кратких руководствах для [Windows](quickstart.md) или [Linux](quickstart-linux.md). Из этого руководства вы узнаете, как выполнять следующие задачи:    

> [!div class="checklist"]
> * Создать модуль IoT Edge на основе пакета SDK .NET Core 2.1 с помощью Visual Studio Code.
> * Использовать Visual Studio Code и Docker для создания образа Docker и его публикации в реестре.
> * Развертывать модуль на устройстве IoT Edge.
> * Просматривать сформированные данные.

>[!NOTE]
>[Visual Studio 2017 также можно использовать для разработки, отладки и развертывания модулей IoT Edge](how-to-visual-studio-develop-csharp-module.md).

Модуль IoT Edge, создаваемый в этом руководстве, фильтрует данные температуры, которые создаются устройством. Оно отправляет сообщения, только если температура превышает заданное пороговое значение. Такой тип пограничного анализа удобен для сокращения объема данных, передаваемых в облако и сохраняемых в нем. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Предварительные требования

Устройство Azure IoT Edge.

* В качестве устройства Azure IoT Edge можно использовать компьютер, на котором ведется разработка, или виртуальную машину. Для этого выполните действия, описанные в кратком руководстве для устройств [Linux](quickstart-linux.md) или [Windows](quickstart.md).

Облачные ресурсы.

* [Центр Интернета вещей](../iot-hub/iot-hub-create-through-portal.md) ценовой категории "Бесплатный" или "Стандартный" в Azure. 

Ресурсы разработки.

* [Visual Studio Code](https://code.visualstudio.com/). 
* [C# для расширения Visual Studio Code (на платформе OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).
* [Средства Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) для Visual Studio Code. 
* [Пакет SDK для .NET Core 2.1](https://www.microsoft.com/net/download).
* [Docker CE](https://docs.docker.com/install/)


## <a name="create-a-container-registry"></a>Создание реестра контейнеров

В этом руководстве описано, как с помощью средств Azure IoT для Visual Studio Code создать модуль и **образ контейнера** из файлов. Затем вы отправите этот образ в **реестр**, содержащий ваши образы и управляющий ими. Наконец, вы развернете свой образ из реестра для выполнения на устройстве IoT Edge.  

Для хранения образов контейнеров можно использовать любые реестры, совместимые с Docker. Две популярные службы реестров Docker — [Реестр контейнеров Azure](https://docs.microsoft.com/azure/container-registry/) и [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags). В этом руководстве используется реестр контейнеров Azure. 

Если вы еще не создали реестр контейнеров, выполните эти действия, чтобы создать реестр в Azure:

1. На [портале Azure](https://portal.azure.com) выберите **Создать ресурс** > **Контейнеры** > **Реестр контейнеров**.

2. Предоставьте следующие значения для создания реестра контейнеров:

   | Поле | Значение | 
   | ----- | ----- |
   | Имя реестра | Укажите уникальное имя. |
   | Подписка | Из раскрывающегося списка выберите подписку. |
   | Группа ресурсов | Мы рекомендуем использовать одну группу ресурсов для всех тестовых ресурсов, которые вы создаете во время работы с краткими руководствами по IoT Edge. Например, **IoTEdgeResources**. |
   | Расположение | Выберите расположение рядом с вами. |
   | Пользователь-администратор | Установите значение **Включить**. |
   | SKU | Выберите **Базовый**. | 

5. Нажмите кнопку **Создать**.

6. После создания реестра контейнеров перейдите к нему, а затем выберите **Ключи доступа**. 

7. Скопируйте значения **Сервер входа**, **Имя пользователя** и **Пароль**. Они потребуются позже в этом руководстве для предоставления доступа к реестру контейнеров. 

## <a name="create-an-iot-edge-module-project"></a>Создание проекта модуля IoT Edge
На следующих этапах показано, как создать проект модуля IoT Edge на основе пакета SDK для .NET Сore 2.0 с использованием Visual Studio Code и средств Azure IoT.

### <a name="create-a-new-solution"></a>Создание решения

Создайте шаблон решения C#, который можно настроить с помощью собственного кода. 

1. В Visual Studio Code выберите **Представление** > **Палитра команд** для открытия палитры команд VS Code. 

2. В палитре команд введите и выполните команду **Azure: Sign in** (Azure: войти). Следуйте инструкциям, чтобы войти в свою учетную запись Azure. Если вход был выполнен, то этот шаг можно пропустить.

3. В палитре команд введите и выполните команду **Azure IoT Edge: New IoT Edge Solution** (Azure IoT Edge: создать решение IoT Edge). Следуйте инструкциям на экране в палитре команд для создания решения.

   | Поле | Значение |
   | ----- | ----- |
   | Выбор папки | Выберите расположение на компьютере разработчика для VS Code, чтобы создать файлы решения. |
   | Введите название решения. | Введите описательное имя решения или примите имя по умолчанию **EdgeSolution**. |
   | Выбор шаблона модуля | Выберите **модуль C#**. |
   | Указание имени модуля | Присвойте модулю имя **CSharpModule**. |
   | Указание репозитория изображений Docker для модуля | Репозиторий изображений включает в себя имя реестра контейнеров и имя образа контейнера. Образ контейнера предварительно заполняется на последнем шаге. Замените **localhost:5000** на значение сервера входа из реестра контейнеров Azure. Вы можете извлечь сервер входа на странице "Обзор" реестра контейнеров на портале Azure. Окончательная строка выглядит так: \<registry name\>.azurecr.io/csharpmodule. |
 
   ![Выбор репозитория образа Docker](./media/tutorial-csharp-module/repository.png)

Окно VS Code загружает рабочую область решения IoT Edge. Рабочая область решения содержит пять компонентов верхнего уровня. Папка **modules** содержит код C# для модуля, а также файлы Dockerfile для создания модуля в качестве образа контейнера. Файл **\.env** хранит учетные данные реестра контейнеров. Файл **deployment.template.json** содержит сведения, которые среда выполнения IoT Edge использует для развертывания модулей на устройстве. Файл **deployment.debug.template.json** содержит отладочные версии модулей. Работая с этим руководством, вы не будете изменять папку **\.vscode** или файл **\.gitignore**.

Если вы не указывали реестр контейнеров при создании решения, но приняли значение по умолчанию localhost:5000, у вас не будет файла \.env. 

<!--
   ![C# solution workspace](./media/tutorial-csharp-module/workspace.png)
-->

### <a name="add-your-registry-credentials"></a>Добавление учетных данных реестра

Файл среды хранит учетные данные для реестра контейнеров и совместно использует их со средой выполнения IoT Edge. Среде выполнения нужны эти учетные данные, чтобы извлечь частный образ на устройство IoT Edge. 

1. Откройте в обозревателе VS Code файл с расширением ENV. 
2. Обновите поля с **именем пользователя** и **паролем**, скопированные из своего реестра контейнера Azure. 
3. Сохраните этот файл. 

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

5. В методе **Init** код создает и настраивает объект **ModuleClient**. Этот объект позволяет модулю подключаться к локальной среде выполнения Azure IoT Edge для отправки и получения сообщений. Строка подключения, используемая в методе **Init**, предоставляется модулю средой выполнения IoT Edge. После создания **ModuleClient** код считывает значение **temperatureThreshold** из требуемых свойств двойника модуля. Этот код регистрирует обратный вызов для получения сообщений из концентратора IoT Edge через **input1** конечной точки. Замените метод  **SetInputMessageHandlerAsync** новым и добавьте метод **SetDesiredPropertyUpdateCallbackAsync** для обновления нужного свойства. Чтобы внести это изменение, замените последнюю строку метода **Init** следующим кодом:

    ```csharp
    // Register a callback for messages that are received by the module.
    // await ioTHubModuleClient.SetInputMessageHandlerAsync("input1", PipeMessage, iotHubModuleClient);

    // Read the TemperatureThreshold value from the module twin's desired properties
    var moduleTwin = await ioTHubModuleClient.GetTwinAsync();
    OnDesiredPropertiesUpdate(moduleTwin.Properties.Desired, ioTHubModuleClient);
    
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

9. В обозревателе VS Code откройте файл **deployment.template.json** в рабочей области решения IoT Edge. Этот файл указывает агенту IoT Edge на модуль для развертывания, в нашем случае это **tempSensor** и **CSharpModule**, а центру IoT Edge на то, как маршрутизировать сообщения между ними. Расширение Visual Studio Code автоматически заполняет шаблон развертывания большинством нужной информации, но проверьте, все ли подходит для вашего решения: 

   1. На панели состояния VS Code для устройства IoT Edge указана платформа по умолчанию **amd64**, т. е. модуль **CSharpModule** использует версию образа для Linux amd64. При необходимости измените платформу по умолчанию на панели состояния с **amd64** на **arm32v7** или **windows-amd64** в соответствии с архитектурой вашего устройства IoT Edge. 

      ![Снимок экрана обновления модуля платформы](./media/tutorial-csharp-module/image-platform.png)

   2. Убедитесь, что шаблон содержит правильное имя модуля, а не имя **SampleModule** по умолчанию, которое можно изменить при создании решения IoT Edge.

   3. В разделе **registryCredentials** хранятся ваши учетные данные реестра Docker, чтобы агент IoT Edge мог извлекать ваш образ модуля. Фактическое имя пользователя хранится в файле ENV, который Git игнорирует. Если это еще не сделано, добавьте ваши учетные данные в файл с расширением .env.  

   4. Для получения дополнительных сведений о манифестах развертывания см. статью [Сведения о развертывании модулей и установлении маршрутов в IoT Edge](module-composition.md).

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


## <a name="build-your-iot-edge-solution"></a>Сборка решения IoT Edge

В предыдущем разделе вы создали решение IoT Edge и добавили код **CSharpModule**, который будет отфильтровывать сообщения, где указанная температура компьютера ниже допустимого порогового значения. Теперь необходимо создать решение в качестве образа контейнера и передать его в реестр контейнеров. 

1. Войдите в Docker, введя следующую команду в окне интегрированного терминала Visual Studio Code. Затем можно отправить образ модуля в Реестр контейнеров Azure.
     
   ```csh/sh
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```
   Укажите имя пользователя, пароль и сервер входа, скопированные из Реестра контейнеров Azure при его создании. Вы также можете получить эти значения из раздела **Ключи доступа** в разделе реестра на портале Azure.

2. В обозревателе VS Code щелкните правой кнопкой мыши файл deployment.template.json и выберите **Build and Push IoT Edge solution** (Создать и отправить решение IoT Edge). 

После указания службе Visual Studio Code создать решение, сначала она запишет данные в шаблон развертывания, а затем в новой папке **config** создаст файл deployment.json. Затем выполняются две команды в интегрированном терминале: `docker build` и `docker push`. Они создают код, упаковывают его в контейнер CSharpModule.dll и передают в реестр контейнера, который был указан при инициализации решения. 

Полный адрес образа контейнера с тегом можно увидеть в окне интегрированного терминала VS Code. Адрес образа создается на основе информации из файла module.json в формате \<repository\>:\<version\>-\<platform\>. В этом руководстве он должен выглядеть так: registryname.azurecr.io/csharpmodule:0.0.1-amd64.

## <a name="deploy-and-run-the-solution"></a>Развертывание и запуск решения

При использовании руководства по настройке устройства IoT Edge модуль был развернут с помощью портала Azure. Модули также можно развертывать с помощью расширения "Набор средств для Центра Интернета вещей Azure" (прежнее название — "Набор средств для Интернета вещей Azure") для Visual Studio Code. У вас уже есть манифест развертывания, подготовленный для вашего сценария (см. файл **deployment.json**). Теперь вам осталось выбрать устройство для получения развертывания.

1. В палитре команд VS Code выполните команду **Центр Интернета вещей Azure: Select IoT Hub** (Центр Интернета вещей Azure: выбрать Центр Интернета вещей). 

2. Выберите подписку и Центр Интернета вещей, содержащий устройство IoT Edge, которое нужно настроить. 

3. В обозревателе VS Code разверните раздел **Azure IoT Hub Devices** (Устройства Центра Интернета вещей Azure). 

4. Щелкните имя устройства IoT Edge правой кнопкой мыши, а затем выберите **Create Deployment for Single Device** (Создание развертывания для одного устройства). 

   ![Создание развертывания для одного устройства](./media/tutorial-csharp-module/create-deployment.png)

5. Выберите файл **deployment.json** в папке **config** и щелкните **Select Edge Deployment Manifest** (Выбрать манифест развертывания Edge). Не используйте файл deployment.template.json. 

6. Нажмите кнопку "Обновить". Вы должны увидеть новый модуль **CSharpModule**, работающий вместе с модулем **TempSensor**, а также **$edgeAgent** и **$edgeHub**.  

## <a name="view-generated-data"></a>Просмотр сформированных данных

Когда вы примените манифест развертывания к устройству IoT Edge, в среде выполнения IoT Edge на устройстве начнется сбор сведений о новом развертывании и выполнение операций. Работа всех выполняющихся на устройстве модулей, которые не включены в манифест развертывания, будет остановлена. А модули, которых нет на устройстве, будут запущены. 

Сведения о состоянии устройства IoT Edge можно просмотреть в разделе **Azure IoT Hub Devices** (Устройства Центра Интернета вещей Azure) в обозревателе Visual Studio Code. Разверните раздел со сведениями об устройстве, чтобы просмотреть список развернутых и запущенных модулей. 

На самом устройстве IoT Edge можно просмотреть состояние развертывания модулей с помощью команды `iotedge list`. Вы увидите четыре модуля: два модуля среды выполнения IoT Edge, tempSensor и пользовательский модуль, который вы создали в рамках этого руководства. Запуск всех модулей может занять несколько минут, поэтому выполните команду повторно, если сразу отображаются не все модули. 

Чтобы просмотреть сообщения, создаваемые любым модулем, используйте команду `iotedge logs <module name>`. 

Сообщения можно просматривать после их поступления в центр Интернета вещей с помощью Visual Studio Code. 

1. Для отслеживания данных, поступающих в Центр Интернета вещей, нажмите кнопку с многоточием (**...**), а затем выберите **Start Monitoring D2C Messages** (Начать мониторинг сообщений D2C).
2. Для отслеживания сообщений D2C для конкретного устройства щелкните его правой кнопкой мыши в списке и выберите **Start Monitoring D2C Messages** (Начать мониторинг сообщений D2C).
3. Чтобы перестать отслеживать данные, в палитре команд выполните команду **Azure IoT Hub: Stop monitoring D2C message** (Центр Интернета вещей: остановить мониторинг сообщений D2C). 
4. Для просмотра или обновления двойника модуля щелкните модуль правой кнопкой мыши в списке и выберите **Edit module twin** (Редактирование двойника модуля). Чтобы обновить двойник модуля, сохраните двойник файла JSON, затем щелкните правой кнопкой мыши область редактора и выберите **Update Module Twin** (Обновить двойник модуля).
5. Чтобы просмотреть журналы Docker, установите [Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) для VS Code. Локально запущенные модули можно найти в обозревателе Docker. В контекстном меню выберите пункт **Показать журналы** для просмотра в интегрированном терминале.
 
## <a name="clean-up-resources"></a>Очистка ресурсов 

Если вы планируете перейти к следующей рекомендуемой статье, можно сохранить созданные и повторно используемые ресурсы и конфигурации. Это же устройство IoT Edge также можно использовать в качестве тестового устройства. 

В противном случае чтобы избежать расходов, можно удалить локальные конфигурации и ресурсы Azure, созданные в рамках этой статьи. 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

[!INCLUDE [iot-edge-clean-up-local-resources](../../includes/iot-edge-clean-up-local-resources.md)]


## <a name="next-steps"></a>Дополнительная информация

В этом руководстве вы создали модуль IoT Edge с кодом для фильтрации необработанных данных, созданных устройством IoT Edge. Когда вы будете готовы создавать собственные модули, больше об этом см. в разделе [Использование Visual Studio Code для разработки и отладки модулей C# для Azure IoT Edge](how-to-develop-csharp-module.md). Перейдите к следующим руководствам, чтобы узнать о других способах, с помощью которых Azure IoT Edge может помочь вам превратить данные в бизнес-аналитику на пограничном устройстве.

> [!div class="nextstepaction"]
> [Руководство по хранению данных в пограничной системе с помощью баз данных SQL Server](tutorial-store-data-sql-server.md)
