---
title: Руководство. Развертывание Функций Azure с помощью на устройстве в Azure IoT Edge | Документация Майкрософт
description: В этом руководстве описано, как разработать Функции Azure в виде модуля IoT Edge с последующим развертыванием на устройстве.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/25/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc, seodec18
ms.openlocfilehash: b0007f578efa1c9f8653e1ab6295b81e554245bd
ms.sourcegitcommit: fad368d47a83dadc85523d86126941c1250b14e2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/19/2019
ms.locfileid: "71123360"
---
# <a name="tutorial-deploy-azure-functions-as-iot-edge-modules"></a>Руководство. Развертывание Функций Azure как модулей IoT Edge

Службу "Функции Azure" можно использовать для развертывания кода, который реализует нужную бизнес-логику, непосредственно на устройствах Azure IoT Edge. В этом руководстве рассматривается создание и развертывание службы "Функции Azure", которая фильтрует данные датчика на имитированном устройстве IoT Edge. Вы используете имитированное устройство IoT Edge, созданное с помощью процедуры развертывания Azure IoT Edge на имитированном устройстве, описанной в кратких руководствах для [Windows](quickstart.md) или [Linux](quickstart-linux.md). Из этого руководства вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
>
> * Использовать Visual Studio Code для создания функции Azure.
> * Использовать VS Code и Docker для создания образа Docker и его публикации в реестре контейнеров.
> * Развертывать модуль из реестра контейнеров на устройстве IoT Edge.
> * Просматривать отфильтрованные данные.

<center>

![Схема архитектуры из руководства, а также размещение и развертывание Функций как модуля](./media/tutorial-deploy-function/functions-architecture.png)
</center>

>[!NOTE]
>Модули службы "Функции Azure", используемые в Azure IoT Edge, находятся на стадии [общедоступной предварительной версии](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 

Функция Azure, создаваемая в этом руководстве, фильтрует данные температуры, созданные устройством. Она отправляет сообщения в вышестоящий Центр Интернета вещей Azure, когда температура превышает заданное пороговое значение. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Предварительные требования

Предполагается, что перед началом работы с этим руководством вы прошли предыдущее, в рамках которого настроили окружение для разработки контейнеров Linux: [Tutorial: Develop IoT Edge modules for Linux devices](tutorial-develop-for-linux.md) (Руководство. Разработка модулей IoT Edge для устройств с Linux). После работы с ним у вас должны быть готовы все необходимые компоненты: 

* [Центр Интернета вещей](../iot-hub/iot-hub-create-through-portal.md) ценовой категории "Бесплатный" или "Стандартный" в Azure.
* [устройство Linux, на котором выполняется Azure IoT Edge](quickstart-linux.md);
* реестр контейнеров, например [Реестр контейнеров Azure](https://docs.microsoft.com/azure/container-registry/);
* средство [Visual Studio Code](https://code.visualstudio.com/), настроенное с помощью [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools);
* выпуск [Docker CE](https://docs.docker.com/install/), настроенный для выполнения контейнеров Linux.

Для разработки модуля IoT Edge с Функциями Azure установите на компьютере разработки следующие дополнительные компоненты: 

* [C# для расширения Visual Studio Code (на платформе OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).
* [пакет SDK для .NET Core 2.1](https://www.microsoft.com/net/download).

## <a name="create-a-function-project"></a>Создание проекта функции

Средства Azure IoT для Visual Studio Code, установленные при выполнении предварительных требований, предоставляют возможности управления, а также некоторые шаблоны кода. В этом разделе Visual Studio Code используется для создания решения IoT Edge, содержащего функцию Azure. 

### <a name="create-a-new-project"></a>Создание нового проекта

Создайте шаблон решения C# с Функциями Azure, который можно настроить с помощью собственного кода.

1. Откройте Visual Studio Code на компьютере для разработки.

2. Выберите **Представление** > **Палитра команд** для открытия палитры команд VS Code.

3. В палитре команд введите и выполните команду **Azure IoT Edge: New IoT Edge Solution** (Azure IoT Edge: создать решение IoT Edge). Следуйте инструкциям на экране в палитре команд для создания решения.

   | Поле | Значение |
   | ----- | ----- |
   | Выбор папки | Выберите расположение на компьютере разработчика для VS Code, чтобы создать файлы решения. |
   | Введите название решения. | Введите описательное имя для решения, например **FunctionSolution**, или примите значение по умолчанию. |
   | Выбор шаблона модуля | Выберите **Функции Azure — C#** . |
   | Указание имени модуля | Назовите модуль **CSharpFunction**. |
   | Указание репозитория изображений Docker для модуля | Репозиторий изображений включает в себя имя реестра контейнеров и имя образа контейнера. Образ контейнера предварительно заполняется на последнем шаге. Замените **localhost:5000** на значение сервера входа из реестра контейнеров Azure. Вы можете извлечь сервер входа на странице "Обзор" реестра контейнеров на портале Azure. Последняя строка выглядит следующим образом: \<имя реестра\>.azurecr.io/CSharpFunction. |

   ![Выбор репозитория образа Docker](./media/tutorial-deploy-function/repository.png)

### <a name="add-your-registry-credentials"></a>Добавление учетных данных реестра

Файл среды хранит учетные данные для реестра контейнеров и совместно использует их со средой выполнения IoT Edge. Среде выполнения нужны эти учетные данные, чтобы извлечь частный образ на устройство IoT Edge.

1. Откройте в обозревателе VS Code файл с расширением ENV.
2. Обновите поля с **именем пользователя** и **паролем**, скопированные из своего реестра контейнера Azure.
3. Сохраните этот файл.

### <a name="select-your-target-architecture"></a>Выбор целевой архитектуры

Сейчас Visual Studio Code позволяет разрабатывать модули C для устройств Linux AMD64 и Linux ARM32v7. Для каждого решения вам нужно выбрать одну целевую платформу, так как сборка и запуск контейнера для разных архитектур различается. По умолчанию используется Linux AMD64. 

1. Откройте палитру команд и выполните поиск **Azure IoT Edge: Set Default Target Platform for Edge Solution** (Azure IoT Edge: установить целевую платформу по умолчанию для решения Edge) или выберите значок ярлыка на боковой панели в нижней части окна. 

2. В палитре команд выберите целевую архитектуру из списка параметров. В рамках этого руководства мы используем в качестве устройства IoT Edge виртуальную машину Ubuntu. Поэтому сохраним значение по умолчанию **amd64**. 

### <a name="update-the-module-with-custom-code"></a>Обновление модуля с помощью пользовательского кода

Давайте добавим еще немного кода, чтобы модуль обрабатывал сообщения на границе до передачи в центр Интернета вещей.

1. В Visual Studio Code откройте **Модули** > **CSharpFunction** > **CSharpFunction.cs**.

1. Измените содержимое файла **CSharpFunction.cs** на код, приведенный ниже. Этот код получает данные телеметрии о температуре компьютера и окружающей среды и передает сообщение в Центр Интернета вещей, только если температура компьютера превышает указанный порог.

   ```csharp
   using System;
   using System.Collections.Generic;
   using System.IO;
   using System.Text;
   using System.Threading.Tasks;
   using Microsoft.Azure.Devices.Client;
   using Microsoft.Azure.WebJobs;
   using Microsoft.Azure.WebJobs.Extensions.EdgeHub;
   using Microsoft.Azure.WebJobs.Host;
   using Microsoft.Extensions.Logging;
   using Newtonsoft.Json;

   namespace Functions.Samples
   {
       public static class CSharpFunction
       {
           [FunctionName("CSharpFunction")]
           public static async Task FilterMessageAndSendMessage(
               [EdgeHubTrigger("input1")] Message messageReceived,
               [EdgeHub(OutputName = "output1")] IAsyncCollector<Message> output,
               ILogger logger)
           {
               const int temperatureThreshold = 20;
               byte[] messageBytes = messageReceived.GetBytes();
               var messageString = System.Text.Encoding.UTF8.GetString(messageBytes);

               if (!string.IsNullOrEmpty(messageString))
               {
                   logger.LogInformation("Info: Received one non-empty message");
                   // Get the body of the message and deserialize it.
                   var messageBody = JsonConvert.DeserializeObject<MessageBody>(messageString);

                   if (messageBody != null && messageBody.machine.temperature > temperatureThreshold)
                   {
                       // Send the message to the output as the temperature value is greater than the threshold.
                       using (var filteredMessage = new Message(messageBytes))
                       {
                            // Copy the properties of the original message into the new Message object.
                            foreach (KeyValuePair<string, string> prop in messageReceived.Properties)
                            {filteredMessage.Properties.Add(prop.Key, prop.Value);}
                            // Add a new property to the message to indicate it is an alert.
                            filteredMessage.Properties.Add("MessageType", "Alert");
                            // Send the message.       
                            await output.AddAsync(filteredMessage);
                            logger.LogInformation("Info: Received and transferred a message with temperature above the threshold");
                       }
                   }
               }
           }
       }
       //Define the expected schema for the body of incoming messages.
       class MessageBody
       {
           public Machine machine {get; set;}
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
   }
   ```

1. Сохраните файл.

## <a name="build-your-iot-edge-solution"></a>Сборка решения IoT Edge

В предыдущем разделе было создано решение IoT Edge и добавлен код в **CSharpFunction**, который будет фильтровать сообщения, в которых температура компьютера ниже допустимого порогового значения. Теперь необходимо создать решение в качестве образа контейнера и передать его в реестр контейнеров.

В этом разделе вы предоставите учетные данные для реестра контейнеров во второй раз (в первый раз вы это сделали в файле **ENV** решения IoT Edge) при локальном входе на компьютере для разработки, чтобы среда Visual Studio Code смогла отправлять образы в реестр.

1. Откройте интегрированный терминал VS Code, выбрав **Вид** > **Терминал**. 

2. Войдите в свой реестр контейнеров, введя следующую команду в окне интегрированного терминала. Укажите имя пользователя и сервер входа, которые были скопированы из реестра контейнеров Azure ранее.

    ```csh/sh
    docker login -u <ACR username> <ACR login server>
    ```

    При запросе на ввод пароля вставьте пароль (он не будет отображаться в окне терминала) для реестра контейнеров и нажмите клавишу **ВВОД**.

    ```csh/sh
    Password: <paste in the ACR password and press enter>
    Login Succeeded
    ```

3. В обозревателе VS Code щелкните правой кнопкой мыши файл deployment.template.json и выберите **Build and Push IoT Edge solution** (Создать и отправить решение IoT Edge). 

После указания службе Visual Studio Code создать решение, сначала она запишет данные в шаблон развертывания, а затем в новой папке **config** создаст файл deployment.json. Затем выполняются две команды в интегрированном терминале: `docker build` и `docker push`. Они создают код, упаковывают функции в контейнеры, а затем отправляют его в реестр контейнера, который был указан при инициализации решения. 

## <a name="view-your-container-image"></a>Просмотр образа контейнера

Visual Studio Code выводит сообщение об успешном завершении, когда образ контейнера передается в ваш реестр контейнеров. Если требуется лично убедиться в успешности выполнения, можно просмотреть образ в реестре. 

1. Перейдите в реестр контейнеров на портале Azure. 
2. Выберите **Репозитории**.
3. Репозиторий **csharpfunction** отобразится в списке. Чтобы просмотреть дополнительные сведения о репозитории, выберите его.
4. В разделе **Теги** должен отобразиться тег **0.0.1-amd64**. Он указывает версию и платформу созданного образа. Эти значения устанавливаются в файле module.json в папке CSharpFunction. 

## <a name="deploy-and-run-the-solution"></a>Развертывание и запуск решения

Для развертывания модуля функции в устройстве IoT Edge можно использовать портал Azure (как это было показано в кратком руководстве). Также в Visual Studio Code можно развертывать модули и проводить их мониторинг. В следующих разделах используются средства Azure IoT для VS Code, указанные в предварительных требованиях. Если расширение не было установлено, то теперь это можно сделать. 

1. В обозревателе VS Code разверните раздел **Azure IoT Hub Devices** (Устройства Центра Интернета вещей Azure). 

2. Щелкните имя устройства IoT Edge правой кнопкой мыши, а затем выберите **Create Deployment for Single Device** (Создание развертывания для одного устройства). 

3. Перейдите в папку решения, содержащую **CSharpFunction**. Откройте папку конфигурации, выберите файл **deployment.json** и затем **Select Edge Deployment Manifest** (Выбрать манифест развертывания Edge).

4. Обновите раздел **Azure IoT Hub Devices** (Устройства Центра Интернета вещей Azure). Появится новый запущенный модуль **CSharpFunction**, модуль **SimulatedTemperatureSensor**, а также **$edgeAgent** и **$edgeHub**. Может потребоваться несколько минут, чтобы новые модули отобразились. Устройство IoT Edge должно получить сведения о новом развертывании из Центра Интернета вещей, запустить новые контейнеры и затем сообщить о своем состоянии в Центр Интернета вещей. 

   ![Просмотр развернутых модулей в VS Code](./media/tutorial-deploy-function/view-modules.png)

## <a name="view-generated-data"></a>Просмотр сформированных данных

Чтобы отобразить все сообщения, поступающие в Центр Интернета вещей, в палитре команд выполните команду **Центр Интернета вещей: Start Monitoring Built-in Event Endpoint** (Начать мониторинг встроенной конечной точки событий).

Также можно отфильтровать представления, чтобы увидеть все сообщения, поступающие в Центр Интернета вещей с определенного устройства. Щелкните имя устройства правой кнопкой мыши в разделе **Azure IoT Hub Devices** (Устройства Центра Интернета вещей Azure) и выберите **Start Monitoring Built-in Event Endpoint** (Начать мониторинг встроенной конечной точки событий).

Чтобы перестать отслеживать сообщения, в палитре команд выполните команду **Центр Интернета вещей: Stop Monitoring Built-in Event Endpoint** (Остановить мониторинг встроенной конечной точки событий). 

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вы планируете перейти к следующей рекомендуемой статье, можно сохранить созданные и повторно используемые ресурсы и конфигурации. Это же устройство IoT Edge также можно использовать в качестве тестового устройства. 

В противном случае чтобы избежать расходов, можно удалить локальные конфигурации и ресурсы Azure, созданные в рамках этой статьи. 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Дополнительная информация

Из этого руководства вы узнали, как создать модуль функции Azure, содержащий код для фильтрации необработанных данных, которые были созданы при помощи устройства IoT Edge. Дополнительные сведения о создании собственных модулей см. в статье [Use Visual Studio Code to develop and debug C modules for Azure IoT Edge](how-to-vs-code-develop-module.md) (Использование Visual Studio Code для разработки и отладки модулей C для IoT Edge Azure). 

Чтобы узнать о других способах, с помощью которых Azure IoT Edge может помочь превратить данные пограничного устройства в бизнес-аналитику, перейдите к следующим руководствам.

> [!div class="nextstepaction"]
> [Руководство по развертыванию Azure Stream Analytics в качестве модуля IoT Edge (предварительная версия)](tutorial-deploy-stream-analytics.md)
