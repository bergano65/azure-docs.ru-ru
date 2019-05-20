---
title: Примеры интеграции служебной шины Azure со службой "Сетка событий" | Документация Майкрософт
description: В статье представлены примеры интеграции обмена сообщениями через служебную шину с Сеткой событий.
services: service-bus-messaging
documentationcenter: .net
author: spelluru
manager: timlt
editor: ''
ms.assetid: f99766cb-8f4b-4baf-b061-4b1e2ae570e4
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: tutorial
ms.date: 05/14/2019
ms.author: spelluru
ms.openlocfilehash: b29798bb87b7c5c677e7d80e552e45e8d1290541
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/16/2019
ms.locfileid: "65786845"
---
# <a name="respond-to-azure-service-bus-events-received-via-azure-event-grid-by-using-azure-functions-and-azure-logic-apps"></a>Реагирование на события служебной шины Azure, получаемые через Сетку событий Azure с помощью Функций Azure и Azure Logic Apps
Узнайте, как отвечать на события служебной шины Azure, получаемые через Сетку событий Azure с помощью Функций Azure и Azure Logic Apps. Вы сделаете следующее:
 
- Создадите функцию проверки Azure для отладки и просмотра начального потока событий из Сетки событий.
- Создайте функцию Azure для приема и обработки сообщений служебной шины Azure на основе событий, зарегистрированных службой "Сетка событий".
- Создадите приложение логики для реагирования на события Сетки событий.

Создав артефакты служебной шины, Сетки событий, Функций Azure и Logic Apps, вы сделаете следующее: 

1. Отправите сообщения в раздел служебной шины. 
2. Убедитесь, что подписки на раздел получили эти сообщения.
3. Убедитесь, что функция или приложение логики с подпиской на событие получили это событие. 

## <a name="create-a-service-bus-namespace"></a>Создание пространства имен служебной шины
Выполните инструкции, описанные здесь: [Краткое руководство Создание раздела служебной шины и подписок на него с помощью портала Azure](service-bus-quickstart-topics-subscriptions-portal.md). Так вы сделаете следующее:

- Создадите пространство имен служебной шины уровня **Премиум**. 
- Получите строку подключения. 
- Создадите раздел служебной шины.
- Создадите две подписки на раздел. 

## <a name="prepare-a-sample-application-to-send-messages"></a>Подготовка примера приложения для отправки сообщений
Для отправки сообщения в раздел служебной шины можно использовать любой метод. Для использования примера кода в конце этой операции требуется Visual Studio 2017.

1. Клонируйте [репозиторий с azure-service-bus GitHub](https://github.com/Azure/azure-service-bus/).
2. В Visual Studio перейдите к папке *\samples\DotNet\Microsoft.ServiceBus.Messaging\ServiceBusEventGridIntegration*, а затем откройте файл *SBEventGridIntegration.sln*.
3. Перейдите к проекту **MessageSender**, а затем выберите **Program.cs**.
4. Введите имя раздела служебной шины и строку соединения, полученные на предыдущем шаге:

    ```CSharp
    const string ServiceBusConnectionString = "YOUR CONNECTION STRING";
    const string TopicName = "YOUR TOPIC NAME";
    ```
5. Создайте и запустите программу для отправки тестовых сообщений в раздел служебной шины. 

## <a name="set-up-a-test-function-on-azure"></a>Настройка проверочной функции в Azure 
Перед запуском скрипта создайте хотя бы небольшую проверочную функцию, которую можно использовать для отладки и отслеживания потока событий. Выполните инструкции, описанные здесь: [Создание первой функции на портале Azure](../azure-functions/functions-create-first-azure-function.md). Так вы сделаете следующее: 

1. Создадите приложение-функцию.
2. Создадите функцию, активируемую HTTP. 

Затем сделайте следующее: 


# <a name="azure-functions-v2tabv2"></a>[Функции Azure версии 2](#tab/v2)

1. Разверните **Функции** в представлении в виде дерева и выберите нужную функцию. Замените код в функции следующим кодом: 

    ```CSharp
    #r "Newtonsoft.Json"
    
    using System.Net;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Extensions.Primitives;
    using Newtonsoft.Json;
    
    public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
    {
        log.LogInformation("C# HTTP trigger function processed a request.");
        var content = req.Body;
        string jsonContent = await new StreamReader(content).ReadToEndAsync();
        log.LogInformation($"Received Event with payload: {jsonContent}");
    
        IEnumerable<string> headerValues;
        headerValues = req.Headers.GetCommaSeparatedValues("Aeg-Event-Type");
    
        if (headerValues.Count() != 0)
        {
            var validationHeaderValue = headerValues.FirstOrDefault();
            if(validationHeaderValue == "SubscriptionValidation")
            {
                var events = JsonConvert.DeserializeObject<GridEvent[]>(jsonContent);
                var code = events[0].Data["validationCode"];
                log.LogInformation("Validation code: {code}");
                return (ActionResult) new OkObjectResult(new { validationResponse = code });
            }
        }
    
        return jsonContent == null
            ? new BadRequestObjectResult("Please pass a name on the query string or in the request body")
            : (ActionResult)new OkObjectResult($"Hello, {jsonContent}");
    }
    
    public class GridEvent
    {
        public string Id { get; set; }
        public string EventType { get; set; }
        public string Subject { get; set; }
        public DateTime EventTime { get; set; }
        public Dictionary<string, string> Data { get; set; }
        public string Topic { get; set; }
    }
    
    ```
2. Выберите **Сохранить и запустить**.

    ![Выходные данные приложения-функции](./media/service-bus-to-event-grid-integration-example/function-run-output.png)
3. Щелкните **Получить URL-адрес функции** и скопируйте URL-адрес. 

    ![Получение URL-адреса функции](./media/service-bus-to-event-grid-integration-example/get-function-url.png)

# <a name="azure-functions-v1tabv1"></a>[Функции Azure версии 1](#tab/v1)

1. Настройте функцию для использования **версии 1**: 
    1. Разверните приложение-функцию в представлении в виде дерева и выберите **Параметры приложения-функции**. 

        ![Параметры приложения-функции]()./media/service-bus-to-event-grid-integration-example/function-app-settings.png)
    2. Выберите значение **~1** для параметра **Версия среды выполнения**. 
2. Разверните **Функции** в представлении в виде дерева и выберите нужную функцию. Замените код в функции следующим кодом: 

    ```CSharp
    #r "Newtonsoft.Json"
    using System.Net;
    using Newtonsoft.Json;
    using Newtonsoft.Json.Linq;
    
    public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
    {
        log.Info("C# HTTP trigger function processed a request.");
        // parse query parameter
        var content = req.Content;
    
        string jsonContent = await content.ReadAsStringAsync(); 
        log.Info($"Received Event with payload: {jsonContent}");
    
        IEnumerable<string> headerValues;
        if (req.Headers.TryGetValues("Aeg-Event-Type", out headerValues))
        {
            var validationHeaderValue = headerValues.FirstOrDefault();
            if(validationHeaderValue == "SubscriptionValidation")
            {
            var events = JsonConvert.DeserializeObject<GridEvent[]>(jsonContent);
                 var code = events[0].Data["validationCode"];
                 return req.CreateResponse(HttpStatusCode.OK,
                 new { validationResponse = code });
            }
        }
    
        return jsonContent == null
        ? req.CreateResponse(HttpStatusCode.BadRequest, "Pass a name on the query string or in the request body")
        : req.CreateResponse(HttpStatusCode.OK, "Hello " + jsonContent);
    }
    
    public class GridEvent
    {
        public string Id { get; set; }
        public string EventType { get; set; }
        public string Subject { get; set; }
        public DateTime EventTime { get; set; }
        public Dictionary<string, string> Data { get; set; }
        public string Topic { get; set; }
    }
    ```
4. Выберите **Сохранить и запустить**.

    ![Выходные данные приложения-функции](./media/service-bus-to-event-grid-integration-example/function-run-output.png)
4. Щелкните **Получить URL-адрес функции** и скопируйте URL-адрес. 

    ![Получение URL-адреса функции](./media/service-bus-to-event-grid-integration-example/get-function-url.png)

---

## <a name="connect-the-function-and-namespace-via-event-grid"></a>Подключение функции и пространства имен с помощью службы "Сетка событий"
В этом разделе вы свяжете функцию и пространство имен служебной шины с помощью портала Azure. 

Чтобы создать подписку на Сетку событий Azure, сделайте следующее:

1. На портале Azure перейдите к пространству имен и выберите в области слева **События**. На правой панели откроется окно пространства имен с двумя подписками на Сетку событий. 
    
    ![Служебная шина — страница событий](./media/service-bus-to-event-grid-integration-example/service-bus-events-page.png)
2. Выберите **+ Подписка на события** на панели инструментов. 
3. На странице **Создание подписки на события** сделайте следующее:
    1. Укажите **имя** подписки. 
    2. Выберите значение **Веб-перехватчик** для параметра **Тип конечной точки**. 

        ![Служебная шина — подписка на Сетку событий](./media/service-bus-to-event-grid-integration-example/event-grid-subscription-page.png)
    3. Щелкните **Выбрать конечную точку**, вставьте URL-адрес функции, а затем щелкните **Подтвердить выбор**. 

        ![Функция — выбор конечной точки](./media/service-bus-to-event-grid-integration-example/function-select-endpoint.png)
    4. Откройте вкладку **Фильтры**, укажите имя **первой подписки** раздела служебной шины, созданной ранее, а затем щелкните **Создать**. 

        ![Фильтр событий подписки](./media/service-bus-to-event-grid-integration-example/event-subscription-filter.png)
4. Убедитесь, что подписка на события отображается в списке.

    ![Подписка на события в списке](./media/service-bus-to-event-grid-integration-example/event-subscription-in-list.png)

## <a name="send-messages-to-the-service-bus-topic"></a>Отправка сообщений в раздел служебной шины
1. Запустите приложение C# .NET, которое отправляет сообщения в раздел служебной шины. 

    ![Выходные данные приложения в консоли](./media/service-bus-to-event-grid-integration-example/console-app-output.png)
1. На странице приложения-функции Azure в разделе **Функции** выберите свою **функцию** и щелкните **Монитор**. 

    ![Функция — монитор](./media/service-bus-to-event-grid-integration-example/function-monitor.png)

## <a name="receive-messages-by-using-azure-functions"></a>Получение сообщений с помощью службы "Функции Azure"
В предыдущем разделе мы рассмотрели простой сценарий проверки и отладки и убедились, что события отправляются. 

В этом разделе вы узнаете, как получать и обрабатывать сообщения после получения события.

### <a name="publish-a-function-from-visual-studio"></a>Публикация функции из Visual Studio
1. В том же решении Visual Studio, которое вы уже открывали (**SBEventGridIntegration**), выберите файл **ReceiveMessagesOnEvent.cs** в проекте **SBEventGridIntegration**. 
2. Укажите строку подключения служебной шины в следующем коде:

    ```Csharp
    const string ServiceBusConnectionString = "YOUR CONNECTION STRING";
    ```
3. Скачайте **профиль публикации** для функции:
    1. Выберите свое приложение-функцию. 
    2. При необходимости откройте вкладку **Обзор**. 
    3. Щелкните **Получить профиль публикации** на панели инструментов. 

        ![Получение профиля публикации для функции](./media/service-bus-to-event-grid-integration-example/function-download-publish-profile.png)
    4. Сохраните файл в папку проекта. 
4. Затем в Visual Studio щелкните правой кнопкой мыши **SBEventGridIntegration** и выберите **Опубликовать**. 
5. Щелкните *Запустить** на странице **Публикация**. 
6. На странице **Выбор целевого объекта публикации** щелкните **Импорт профиля**. 

    ![Visual Studio — кнопка импорта профиля](./media/service-bus-to-event-grid-integration-example/visual-studio-import-profile-button.png)
7. Выберите **файл профиля публикации**, который вы скачали ранее. 
8. На странице **Публикация** щелкните **Опубликовать**. 

    ![Visual Studio — публикация](./media/service-bus-to-event-grid-integration-example/select-publish.png)
9. Подтвердите, что вы видите новую функцию Azure **ReceiveMessagesOnEvent**. При необходимости обновите страницу. 

    ![Подтверждение создания функции](./media/service-bus-to-event-grid-integration-example/function-receive-messages.png)
10. Получите URL-адрес для новой функции и запишите его. 

### <a name="event-grid-subscription"></a>Подписка на Сетку событий

1. Удалите существующую подписку на Сетку событий:
    1. На странице **Пространство имен служебной шины** выберите пункт **События** в меню слева. 
    2. Выберите существующую подписку на событие. 
    3. На странице **Подписка на события** щелкните **Удалить**.
2. Выполните инструкции по [подключению функции и пространства имен, используя Сетку событий](#connect-the-function-and-namespace-via-event-grid), чтобы создать подписку на Сетку событий с помощью URL-адреса новой функции.
3. Выполните инструкции по [отправке сообщений в раздел служебной шины](#send-messages-to-the-service-bus-topic), чтобы отправить сообщения в раздел и начать мониторинг функции. 

## <a name="receive-messages-by-using-logic-apps"></a>Получение сообщений с помощью Logic Apps
Подключите приложение логики к служебной шине Azure и Сетке событий Azure, сделав следующее:

1. Создайте приложение логики на портале Azure.
    1. Щелкните **+ Создать ресурс**, выберите **Интеграция**, а затем — **Logic App**. 
    2. На странице **Приложение логики — создание** укажите **имя** приложения логики.
    3. Выберите свою **подписку Azure**. 
    4. Щелкните **Использовать существующую** для **группы ресурсов** и выберите группу ресурсов, которая использовалась для других ресурсов (например, Функции Azure или пространство имен служебной шины), созданных ранее. 
    5. Укажите **расположение** приложения логики. 
    6. Выберите **Создать**, чтобы создать приложение логики. 
2. На странице **Конструктор Logic Apps** щелкните **Пустое приложение логики** в разделе **Шаблоны**. 
3. В конструкторе сделайте следующее:
    1. Введите **Сетка событий** для поиска. 
    2. Щелкните **При возникновении события ресурса (предварительная версия) — Сетка событий Azure**. 

        ![Конструктор Logic Apps — выбор триггера Сетки событий](./media/service-bus-to-event-grid-integration-example/logic-apps-event-grid-trigger.png)
4. Щелкните **Войти**, введите свои учетные данные Azure и выберите **Разрешить доступ**. 
5. На странице **При возникновении события ресурса** сделайте следующее:
    1. Выберите подписку Azure. 
    2. Для параметра **Тип ресурса** выберите значение **Microsoft.ServiceBus.Namespaces**. 
    3. Для параметра **Имя ресурса** выберите пространство имен служебной шины. 
    4. Щелкните **Добавить параметр** и выберите **Фильтр суффиксов**. 
    5. В разделе **Фильтр суффиксов** укажите имя вашей второй подписки раздела служебной шины. 

        ![Конструктор Logic Apps — настройка события](./media/service-bus-to-event-grid-integration-example/logic-app-configure-event.png)
6. В конструкторе щелкните **+ Новый шаг** и сделайте следующее:
    1. Введите **Служебная шина** для поиска.
    2. Выберите **Служебная шина** в списке. 
    3. Выберите **Получить сообщения** в списке **Действия**. 
    4. Выберите **Получение сообщений из подписки раздела (с блокировкой при извлечении)**. 

        ![Конструктор Logic Apps — действие получения сообщений](./media/service-bus-to-event-grid-integration-example/service-bus-get-messages-step.png)
    5. Укажите **имя подключения**. Например:  **Получение сообщений из подписки раздела**. Затем выберите пространство имен служебной шины. 

        ![Конструктор Logic Apps — выбор пространства имен служебной шины](./media/service-bus-to-event-grid-integration-example/logic-apps-select-namespace.png) 
    6. Выберите **RootManageSharedAccessKey**.

        ![Конструктор Logic Apps — выбор общего ключа доступа](./media/service-bus-to-event-grid-integration-example/logic-app-shared-access-key.png) 
    7. Нажмите кнопку **Создать**. 
    8. Выберите раздел и подписку. 
    
        ![Конструктор Logic Apps — выбор подписки и раздела служебной шины](./media/service-bus-to-event-grid-integration-example/logic-app-select-topic-subscription.png)
7. Щелкните **+ Новый шаг** и сделайте следующее: 
    1. Выберите **служебную шину**.
    2. Выберите **Завершить сообщение в подписке раздела** в списке действий. 
    3. Выберите **раздел** служебной шины.
    4. Выберите вторую **подписку** на раздел.
    5. Для параметра **Маркер блокировки сообщения** выберите значение **Маркер блокировки** в списке **Динамическое содержимое**. 

        ![Конструктор Logic Apps — выбор подписки и раздела служебной шины](./media/service-bus-to-event-grid-integration-example/logic-app-complete-message.png)
8. Выберите **Сохранить** на панели инструментов в конструкторе Logic Apps, чтобы сохранить приложение логики. 
9. Выполните инструкции по [отправке сообщений в раздел служебной шины](#send-messages-to-the-service-bus-topic), чтобы отправить сообщения в раздел. 
10. Перейдите на страницу **Обзор** приложения-логики. Выполнение приложения логики можно отследить в **журнале выполнения** для отправленных сообщений.

    ![Конструктор Logic Apps — выполнение приложения логики](./media/service-bus-to-event-grid-integration-example/logic-app-runs.png)

## <a name="next-steps"></a>Дополнительная информация

* Дополнительные сведения о службе "Сетка событий" Azure см. [здесь](https://docs.microsoft.com/azure/event-grid/).
* Дополнительные сведения о решении "Функции Azure" см. [здесь](https://docs.microsoft.com/azure/azure-functions/).
* Дополнительные сведения о компоненте Logic Apps службы приложений Azure см. [здесь](https://docs.microsoft.com/azure/logic-apps/).
* Дополнительные сведения о [служебной шине Azure](https://docs.microsoft.com/azure/service-bus/).


[2]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid2.png
[3]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid3.png
[7]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid7.png
[8]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid8.png
[9]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid9.png
[10]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid10.png
[11]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid11.png
[12]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid12.png
[12-1]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid12-1.png
[12-2]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid12-2.png
[13]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid13.png
[14]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid14.png
[15]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid15.png
[16]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid16.png
[17]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid17.png
[18]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid18.png
[20]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgridportal.png
[21]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgridportal2.png
