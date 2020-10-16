---
title: Интеграция со службой Azure SignalR
titleSuffix: Azure Digital Twins
description: Узнайте, как выполнять потоковую передачу данных телеметрии Azure Digital двойников в клиенты с помощью Azure SignalR.
author: dejimarquis
ms.author: aymarqui
ms.date: 09/02/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: fd23cab844d40f75aa0034096be1bca897d54ff6
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/16/2020
ms.locfileid: "92108360"
---
# <a name="integrate-azure-digital-twins-with-azure-signalr-service"></a>Интеграция Azure Digital двойников со службой Azure SignalR

Из этой статьи вы узнаете, как интегрировать Azure Digital двойников со [службой Azure SignalR](../azure-signalr/signalr-overview.md).

Решение, описанное в этой статье, позволит отправлять данные телеметрии Digital двойника подключенным клиентам, таким как одна веб-страница или мобильное приложение. В результате клиенты обновляются с учетом метрик реального времени и состояния с устройств Интернета вещей без необходимости опрашивать сервер или отправлять новые HTTP-запросы на обновление.

## <a name="prerequisites"></a>Предварительные требования

Ниже приведены предварительные требования, которые необходимо выполнить перед продолжением.

* Перед интеграцией решения с помощью службы Azure SignalR в этой статье вы должны пройти руководство по работе с цифровыми Двойниковми Azure [_**: подключить комплексное решение**_](tutorial-end-to-end.md), так как оно строится поверх него. В этом руководстве описано, как настроить экземпляр Digital двойников для Azure, который работает с виртуальным устройством IoT, чтобы активировать цифровые двойника обновления. Это позволит подключить эти обновления к образцу веб-приложения с помощью службы Azure SignalR.
    - Вам потребуется имя **раздела "Сетка событий** ", созданного в этом руководстве.
* Установите на компьютере [**Node.js**](https://nodejs.org/) .

Вы также можете войти в [портал Azure](https://portal.azure.com/) с помощью учетной записи Azure.

## <a name="solution-architecture"></a>Архитектура решения

Вы будете присоединить службу Azure SignalR к Azure Digital двойников по указанному ниже пути. Разделы A, B и C на схеме основаны на схеме архитектуры [комплексного требования учебника](tutorial-end-to-end.md). в этом пошаговом окне вы создадите это, добавив раздел D.

:::image type="content" source="media/how-to-integrate-azure-signalr/signalr-integration-topology.png" alt-text="Обзор служб Azure в комплексном сценарии. Описание данных, передаваемых из устройства в центр Интернета вещей, с помощью функции Azure (стрелка б) в экземпляре Azure Digital двойников (раздел A), а затем из службы &quot;Сетка событий&quot; в другую функцию Azure для обработки (стрелка C). Раздел г показывает данные, передаваемые из одной и той же сетки событий с помощью стрелки C в функцию Azure с меткой &quot;вещание&quot;. &quot;Broadcast&quot; обменивается данными с другой функцией Azure, обозначенной как &quot;Negotiate&quot;, и &quot;Broadcast&quot; и &quot;Negotiate&quot; обмениваются данными с устройствами компьютера." lightbox="media/how-to-integrate-azure-signalr/signalr-integration-topology.png":::

## <a name="download-the-sample-applications"></a>Загрузка примеров приложений

Сначала скачайте необходимые примеры приложений. Потребуется следующее:
* [**Примеры цифровых двойников Azure**](/samples/azure-samples/digital-twins-samples/digital-twins-samples/). Этот пример содержит *адтсамплеапп* с двумя функциями Azure для перемещения данных по экземпляру цифрового двойников Azure (Дополнительные сведения об этом сценарии см. в [*руководстве по подключению комплексного решения*](tutorial-end-to-end.md)). Он также содержит пример приложения *девицесимулатор* , которое имитирует устройство IOT, создавая новое значение температуры каждую секунду. 
    - Перейдите к образцу ссылки и нажмите кнопку *скачать ZIP-файл* , чтобы загрузить копию примера на компьютер, как _**Azure_Digital_Twins_samples.zip**_. Распакуйте папку.
* [**Пример веб-приложения для интеграции SignalR**](/samples/azure-samples/digitaltwins-signalr-webapp-sample/digital-twins-samples/): это пример веб-приложения, которое будет использовать данные телеметрии Azure Digital двойников из службы Azure SignalR.
    -  Перейдите к образцу ссылки и нажмите кнопку *скачать ZIP-файл* , чтобы загрузить копию примера на компьютер, как _**Azure_Digital_Twins_SignalR_integration_web_app_sample.zip**_. Распакуйте папку.

[!INCLUDE [Create instance](../azure-signalr/includes/signalr-quickstart-create-instance.md)]

Оставьте окно браузера открытым для портал Azure, так как оно будет использоваться снова в следующем разделе.

## <a name="configure-and-run-the-azure-functions-app"></a>Настройка и запуск приложения "функции Azure"

В этом разделе вы настроите две функции Azure:
* **Negotiate** — функция триггера HTTP. Он использует входную привязку *сигналрконнектионинфо* для создания и возврата допустимых сведений о соединении.
* **Broadcast** — функция триггера [сетки событий](../event-grid/overview.md) . Он получает данные телеметрии Azure Digital двойников через сетку событий и использует выходную привязку экземпляра *SignalR* , созданного на предыдущем шаге, для передачи сообщения во все подключенные клиентские приложения.

Сначала перейдите в браузер, где открывается портал Azure, и выполните следующие действия, чтобы получить **строку подключения** для настроенного экземпляра SignalR. Он понадобится для настройки функций.
1. Подтвердите, что развернутый ранее экземпляр службы SignalR успешно создан. Это можно сделать, выполнив поиск по имени в поле поиска в верхней части портала. Выберите экземпляр, чтобы открыть его.

1. Выберите **ключи** в меню экземпляр, чтобы просмотреть строки подключения для экземпляра службы SignalR.

1. Щелкните значок, чтобы скопировать основную строку подключения.

    :::image type="content" source="media/how-to-integrate-azure-signalr/signalr-keys.png" alt-text="Обзор служб Azure в комплексном сценарии. Описание данных, передаваемых из устройства в центр Интернета вещей, с помощью функции Azure (стрелка б) в экземпляре Azure Digital двойников (раздел A), а затем из службы &quot;Сетка событий&quot; в другую функцию Azure для обработки (стрелка C). Раздел г показывает данные, передаваемые из одной и той же сетки событий с помощью стрелки C в функцию Azure с меткой &quot;вещание&quot;. &quot;Broadcast&quot; обменивается данными с другой функцией Azure, обозначенной как &quot;Negotiate&quot;, и &quot;Broadcast&quot; и &quot;Negotiate&quot; обмениваются данными с устройствами компьютера." lightbox="media/how-to-integrate-azure-signalr/signalr-keys.png":::

Затем запустите Visual Studio (или другой редактор кода по своему усмотрению) и откройте решение Code в папке *Azure_Digital_Twins_samples > адтсамплеапп* . Затем выполните следующие действия, чтобы создать функции.

1. Создайте новый яркий класс C# с именем **SignalRFunctions.CS** в проекте *самплефунктионсапп* .

1. Замените содержимое файла класса следующим кодом:

    ```C#
    using System;
    using System.Threading.Tasks;
    using Microsoft.AspNetCore.Http;
    using Microsoft.Azure.EventGrid.Models;
    using Microsoft.Azure.WebJobs;
    using Microsoft.Azure.WebJobs.Extensions.Http;
    using Microsoft.Azure.WebJobs.Extensions.EventGrid;
    using Microsoft.Azure.WebJobs.Extensions.SignalRService;
    using Microsoft.Extensions.Logging;
    using Newtonsoft.Json;
    using Newtonsoft.Json.Linq;
    using System.Collections.Generic;
    
    namespace SampleFunctionsApp
    {
        public static class SignalRFunctions
        {
            public static double temperature;
    
            [FunctionName("negotiate")]
            public static SignalRConnectionInfo GetSignalRInfo(
                [HttpTrigger(AuthorizationLevel.Anonymous, "post")] HttpRequest req,
                [SignalRConnectionInfo(HubName = "dttelemetry")] SignalRConnectionInfo connectionInfo)
            {
                return connectionInfo;
            }
    
            [FunctionName("broadcast")]
            public static Task SendMessage(
                [EventGridTrigger] EventGridEvent eventGridEvent,
                [SignalR(HubName = "dttelemetry")] IAsyncCollector<SignalRMessage> signalRMessages,
                ILogger log)
            {
                JObject eventGridData = (JObject)JsonConvert.DeserializeObject(eventGridEvent.Data.ToString());
    
                log.LogInformation($"Event grid message: {eventGridData}");
    
                var patch = (JObject)eventGridData["data"]["patch"][0];
                if (patch["path"].ToString().Contains("/Temperature"))
                {
                    temperature = Math.Round(patch["value"].ToObject<double>(), 2);
                }
    
                var message = new Dictionary<object, object>
                {
                    { "temperatureInFahrenheit", temperature},
                };
        
                return signalRMessages.AddAsync(
                    new SignalRMessage
                    {
                        Target = "newMessage",
                        Arguments = new[] { message }
                    });
            }
        }
    }
    ```

1. В окне *консоли диспетчера пакетов* Visual Studio или в любом окне командной строки на компьютере в папке *Azure_Digital_Twins_samples \адтсамплеапп\самплефунктионсапп* выполните следующую команду, чтобы установить `SignalRService` пакет NuGet в проект:
    ```cmd
    dotnet add package Microsoft.Azure.WebJobs.Extensions.SignalRService --version 1.2.0
    ```

    Это должно устранить все проблемы с зависимостями в классе.

Затем опубликуйте свою функцию в Azure, выполнив действия, описанные в [разделе " *Публикация приложения* ](tutorial-end-to-end.md#publish-the-app) " руководства *Подключение комплексного решения* . Вы можете опубликовать его в той же службе приложений или приложении-функции, которая использовалась в сквозном учебнике prereq, или создать новую, но вы можете использовать то же самое, чтобы избежать дублирования. Кроме того, завершите публикацию приложения, выполнив следующие действия:
1. Собирайте **URL-адрес конечной точки HTTP**функции *Negotiate* . Для этого перейдите на страницу [приложений-функций](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.Web%2Fsites/kind/functionapp) портал Azure и выберите приложение-функцию из списка. В меню приложение выберите *функции* и выберите функцию *согласовать* .

    :::image type="content" source="media/how-to-integrate-azure-signalr/functions-negotiate.png" alt-text="Обзор служб Azure в комплексном сценарии. Описание данных, передаваемых из устройства в центр Интернета вещей, с помощью функции Azure (стрелка б) в экземпляре Azure Digital двойников (раздел A), а затем из службы &quot;Сетка событий&quot; в другую функцию Azure для обработки (стрелка C). Раздел г показывает данные, передаваемые из одной и той же сетки событий с помощью стрелки C в функцию Azure с меткой &quot;вещание&quot;. &quot;Broadcast&quot; обменивается данными с другой функцией Azure, обозначенной как &quot;Negotiate&quot;, и &quot;Broadcast&quot; и &quot;Negotiate&quot; обмениваются данными с устройствами компьютера.":::

    Нажмите *получить URL-адрес функции* и скопируйте значение **до _/API_ (не включайте последние _/неготиате?_)**. Он будет использоваться позже.

    :::image type="content" source="media/how-to-integrate-azure-signalr/get-function-url.png" alt-text="Обзор служб Azure в комплексном сценарии. Описание данных, передаваемых из устройства в центр Интернета вещей, с помощью функции Azure (стрелка б) в экземпляре Azure Digital двойников (раздел A), а затем из службы &quot;Сетка событий&quot; в другую функцию Azure для обработки (стрелка C). Раздел г показывает данные, передаваемые из одной и той же сетки событий с помощью стрелки C в функцию Azure с меткой &quot;вещание&quot;. &quot;Broadcast&quot; обменивается данными с другой функцией Azure, обозначенной как &quot;Negotiate&quot;, и &quot;Broadcast&quot; и &quot;Negotiate&quot; обмениваются данными с устройствами компьютера.":::

1. Наконец, добавьте **строку подключения** Azure SignalR из ранее в параметры приложения функции, используя следующую команду Azure CLI. Команду можно выполнить в [Azure Cloud Shell](https://shell.azure.com)или локально, если [на компьютере установлен](/cli/azure/install-azure-cli?view=azure-cli-latest)Azure CLI.
 
    ```azurecli
    az functionapp config appsettings set -g <your-resource-group> -n <your-App-Service-(function-app)-name> --settings "AzureSignalRConnectionString=<your-Azure-SignalR-ConnectionString>"
    ```

    Выходные данные этой команды выводят все параметры приложения, настроенные для функции Azure. Найдите `AzureSignalRConnectionString` в нижней части списка, чтобы убедиться, что он был добавлен.

    :::image type="content" source="media/how-to-integrate-azure-signalr/output-app-setting.png" alt-text="Обзор служб Azure в комплексном сценарии. Описание данных, передаваемых из устройства в центр Интернета вещей, с помощью функции Azure (стрелка б) в экземпляре Azure Digital двойников (раздел A), а затем из службы &quot;Сетка событий&quot; в другую функцию Azure для обработки (стрелка C). Раздел г показывает данные, передаваемые из одной и той же сетки событий с помощью стрелки C в функцию Azure с меткой &quot;вещание&quot;. &quot;Broadcast&quot; обменивается данными с другой функцией Azure, обозначенной как &quot;Negotiate&quot;, и &quot;Broadcast&quot; и &quot;Negotiate&quot; обмениваются данными с устройствами компьютера.":::

#### <a name="connect-the-function-to-event-grid"></a>Подключение функции к Сетке событий

Затем подпишите функцию *широковещательной рассылки* Azure в службу " **Сетка событий** ", созданную во время работы с [*руководством: подключение комплексного решения*](tutorial-end-to-end.md) prereq. Это позволит передавать данные телеметрии из *thermostat67* двойника через раздел сетки событий в функцию, которая может транслировать ее на все клиенты.

Для этого вы создадите подписку на службу " **Сетка событий** " в разделе "Сетка событий", чтобы использовать функцию *широковещательной рассылки* Azure в качестве конечной точки.

На [портале Azure](https://portal.azure.com/) перейдите к своему разделу Сетки событий, выполнив поиск по его имени в верхней строке поиска. Выберите *+ Event Subscription* (+ Подписка на события).

:::image type="content" source="media/how-to-integrate-azure-signalr/event-subscription-1b.png" alt-text="Обзор служб Azure в комплексном сценарии. Описание данных, передаваемых из устройства в центр Интернета вещей, с помощью функции Azure (стрелка б) в экземпляре Azure Digital двойников (раздел A), а затем из службы &quot;Сетка событий&quot; в другую функцию Azure для обработки (стрелка C). Раздел г показывает данные, передаваемые из одной и той же сетки событий с помощью стрелки C в функцию Azure с меткой &quot;вещание&quot;. &quot;Broadcast&quot; обменивается данными с другой функцией Azure, обозначенной как &quot;Negotiate&quot;, и &quot;Broadcast&quot; и &quot;Negotiate&quot; обмениваются данными с устройствами компьютера.":::

На странице *Создание подписки на событие* заполните поля следующим образом (поля, заполненные по умолчанию, не указываются):
* *СВЕДЕНИЯ О ПОДПИСКЕ НА СОБЫТИЯ* > **Имя**: укажите имя для подписки на события.
* *СВЕДЕНИЯ О КОНЕЧНОЙ ТОЧКЕ* > **Тип конечной точки**: выберите в меню пункт *Функция Azure*.
* *СВЕДЕНИЯ О КОНЕЧНОЙ ТОЧКЕ* > **Конечная точка**: нажмите ссылку *Выбор конечной точки*. Откроется окно *Выбор функции Azure*:
    - Заполните **подписку**, **группу ресурсов**, **приложение-функцию** и **функцию** (*вещание*). После выбора подписки некоторые из этих полей могут быть заполнены автоматически.
    - Нажмите кнопку **Подтвердить выбор**.

:::image type="content" source="media/how-to-integrate-azure-signalr/create-event-subscription.png" alt-text="Обзор служб Azure в комплексном сценарии. Описание данных, передаваемых из устройства в центр Интернета вещей, с помощью функции Azure (стрелка б) в экземпляре Azure Digital двойников (раздел A), а затем из службы &quot;Сетка событий&quot; в другую функцию Azure для обработки (стрелка C). Раздел г показывает данные, передаваемые из одной и той же сетки событий с помощью стрелки C в функцию Azure с меткой &quot;вещание&quot;. &quot;Broadcast&quot; обменивается данными с другой функцией Azure, обозначенной как &quot;Negotiate&quot;, и &quot;Broadcast&quot; и &quot;Negotiate&quot; обмениваются данными с устройствами компьютера.":::

Вернитесь на страницу *Создание подписки на события* и нажмите кнопку **Создать**.

## <a name="configure-and-run-the-web-app"></a>Настройка и запуск веб-приложения

В этом разделе вы увидите результат в действии. Сначала вы запустите **пример приложения для имитации устройства** , которое отправляет данные телеметрии через ваш экземпляр Azure Digital двойников. Затем вы настроите **Пример клиентского веб-приложения** для подключения к настроенному потоку SignalR Azure. После этого вы сможете видеть данные, обновляющие пример веб-приложения в режиме реального времени.

### <a name="run-the-device-simulator"></a>Запуск симулятора устройства

Во время комплексного требования к учебнику вы [настроили симулятор устройств](tutorial-end-to-end.md#configure-and-run-the-simulation) для отправки данных через центр Интернета вещей и ваш экземпляр Azure Digital двойников.

Теперь все, что нужно сделать, — это запустить проект симулятора, расположенный в *Azure_Digital_Twins_samples > девицесимулатор > девицесимулатор. sln*. Если вы используете Visual Studio, вы можете открыть проект, а затем запустить его с помощью этой кнопки на панели инструментов:

:::image type="content" source="media/how-to-integrate-azure-signalr/start-button-simulator.png" alt-text="Обзор служб Azure в комплексном сценарии. Описание данных, передаваемых из устройства в центр Интернета вещей, с помощью функции Azure (стрелка б) в экземпляре Azure Digital двойников (раздел A), а затем из службы &quot;Сетка событий&quot; в другую функцию Azure для обработки (стрелка C). Раздел г показывает данные, передаваемые из одной и той же сетки событий с помощью стрелки C в функцию Azure с меткой &quot;вещание&quot;. &quot;Broadcast&quot; обменивается данными с другой функцией Azure, обозначенной как &quot;Negotiate&quot;, и &quot;Broadcast&quot; и &quot;Negotiate&quot; обмениваются данными с устройствами компьютера.":::

Откроется окно консоли, в котором будут отображаться сымитированные сообщения телеметрии температуры. Они отправляются через экземпляр Azure Digital двойников, где они затем выбираются функциями и SignalR Azure.

В этой консоли больше ничего делать не нужно, но оставьте ее работающей для выполнения следующих шагов.

### <a name="configure-the-sample-client-web-app"></a>Настройка примера клиентского веб-приложения

Затем настройте **Пример веб-приложения интеграции SignalR** , выполнив следующие действия:
1. Используя Visual Studio или любой другой редактор кода, откройте распакованную папку _**Azure_Digital_Twins_SignalR_integration_web_app_sample**_ , скачанную в разделе [*Загрузка примеров приложений*](#download-the-sample-applications) .

1. Откройте файл *src/App.js* и замените URL-адрес `HubConnectionBuilder` конечной точкой HTTP для функции **Negotiate** , сохраненной ранее:

    ```javascript
        const hubConnection = new HubConnectionBuilder()
            .withUrl('<URL>')
            .build();
    ```
1. В *командной строке разработчика* Visual Studio или любом командном окне на компьютере перейдите в папку *Azure_Digital_Twins_SignalR_integration_web_app_sample \срк* . Выполните следующую команду, чтобы установить пакеты зависимых узлов:

    ```cmd
    npm install
    ```

Затем задайте разрешения в приложении функции в портал Azure:
1. На странице приложения- [функции](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.Web%2Fsites/kind/functionapp) портал Azure выберите экземпляр приложения-функции.
1. Прокрутите вниз в меню экземпляр и выберите *CORS*. На странице CORS добавьте в `http://localhost:3000` качестве разрешенного источника, введя его в пустое поле. Установите флажок *включить доступ — управление-разрешить-учетные данные* и нажмите кнопку *сохранить*.

    :::image type="content" source="media/how-to-integrate-azure-signalr/cors-setting-azure-function.png" alt-text="Обзор служб Azure в комплексном сценарии. Описание данных, передаваемых из устройства в центр Интернета вещей, с помощью функции Azure (стрелка б) в экземпляре Azure Digital двойников (раздел A), а затем из службы &quot;Сетка событий&quot; в другую функцию Azure для обработки (стрелка C). Раздел г показывает данные, передаваемые из одной и той же сетки событий с помощью стрелки C в функцию Azure с меткой &quot;вещание&quot;. &quot;Broadcast&quot; обменивается данными с другой функцией Azure, обозначенной как &quot;Negotiate&quot;, и &quot;Broadcast&quot; и &quot;Negotiate&quot; обмениваются данными с устройствами компьютера.":::

### <a name="see-the-results"></a>Просмотр результатов

Чтобы увидеть результаты в действии, запустите **Пример веб-приложения "Интеграция SignalR**". Это можно сделать в любом окне консоли в расположении *Azure_Digital_Twins_SignalR_integration_web_app_sample \срк* , выполнив следующую команду:

```cmd
npm start
```

Откроется окно браузера с примером приложения, в котором отображается датчик температуры визуального элемента. После запуска приложения вы должны увидеть значения телеметрии температуры из симулятора устройств, распространяемого через Azure Digital двойников, которые веб-приложение отражает в режиме реального времени.

:::image type="content" source="media/how-to-integrate-azure-signalr/signalr-webapp-output.png" alt-text="Обзор служб Azure в комплексном сценарии. Описание данных, передаваемых из устройства в центр Интернета вещей, с помощью функции Azure (стрелка б) в экземпляре Azure Digital двойников (раздел A), а затем из службы &quot;Сетка событий&quot; в другую функцию Azure для обработки (стрелка C). Раздел г показывает данные, передаваемые из одной и той же сетки событий с помощью стрелки C в функцию Azure с меткой &quot;вещание&quot;. &quot;Broadcast&quot; обменивается данными с другой функцией Azure, обозначенной как &quot;Negotiate&quot;, и &quot;Broadcast&quot; и &quot;Negotiate&quot; обмениваются данными с устройствами компьютера.":::

## <a name="clean-up-resources"></a>Очистка ресурсов

Если ресурсы, созданные в этой статье, больше не нужны, выполните следующие действия, чтобы удалить их. 

С помощью Azure Cloud Shell или локального Azure CLI можно удалить все ресурсы Azure в группе ресурсов с помощью команды [AZ Group Delete](/cli/azure/group?view=azure-cli-latest#az-group-delete) . Удаление группы ресурсов также приведет к удалению...
* экземпляр Azure Digital двойников (из комплексного руководства)
* центр Интернета вещей и регистрация устройств концентратора (из комплексного руководства)
* раздел "Сетка событий" и связанные подписки
* приложение функций Azure, включая все три функции и связанные ресурсы, такие как хранилище.
* экземпляр Azure SignalR

> [!IMPORTANT]
> Удаление группы ресурсов — процесс необратимый. Группа ресурсов и все содержащиеся в ней ресурсы удаляются без возможности восстановления. Будьте внимательны, чтобы случайно не удалить не ту группу ресурсов или не те ресурсы. 

```azurecli
az group delete --name <your-resource-group>
```

Если вы удаляете экземпляр Azure Digital двойников, вы также можете удалить регистрацию приложения Azure AD, созданную для него, в рамках комплексного руководства, используя следующую команду:

```azurecli
az ad app delete --id <your-application-ID>
```

Наконец, удалите образцы папок проекта, скачанные на локальный компьютер (*Azure_Digital_Twins_samples.zip* и *Azure_Digital_Twins_SignalR_integration_web_app_sample.zip*).

## <a name="next-steps"></a>Дальнейшие действия

В этой статье вы настроите функции Azure с SignalR, чтобы транслировать события телеметрии Digital двойников в пример клиентского приложения.

Далее вы узнаете о службе Azure SignalR:
* [*Сведения о службе Azure SignalR*](../azure-signalr/signalr-overview.md)

Или Узнайте больше о проверке подлинности службы Azure SignalR с помощью функций Azure:
* [*Проверка подлинности службы Azure SignalR*](../azure-signalr/signalr-tutorial-authenticate-azure-functions.md)
