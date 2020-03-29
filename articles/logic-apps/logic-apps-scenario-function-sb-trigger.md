---
title: Вызов приложений логики с помощью решения "Функции Azure"
description: Создавайте функции Azure, которые вызывают или запускают логические приложения, слушая шины службы Azure
services: logic-apps
ms.suite: integration
ms.reviewer: jehollan, klam, logicappspm
ms.topic: article
ms.date: 11/08/2019
ms.openlocfilehash: afd2735bae2a79ad942c347219019ef200b61070
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75428707"
---
# <a name="call-or-trigger-logic-apps-by-using-azure-functions-and-azure-service-bus"></a>Вызов или триггер логических приложений с помощью функций Azure и шины службAzи

Функции [Azure](../azure-functions/functions-overview.md) можно использовать для запуска приложения логики, когда необходимо развернуть длительного слушателя или задачи. Например, можно создать функцию Azure, которая сслушивается в очереди [Azure Service Bus](../service-bus-messaging/service-bus-messaging-overview.md) и немедленно запускает приложение логики в качестве триггера нажатия.

## <a name="prerequisites"></a>Предварительные требования

* Подписка Azure. Если у вас еще нет подписки Azure, [зарегистрируйтесь для получения бесплатной учетной записи Azure](https://azure.microsoft.com/free/).

* Пространство имен azure Service Bus. Если у вас нет пространства имен, [сначала создайте пространство имен.](../service-bus-messaging/service-bus-create-namespace-portal.md)

* Функциональное приложение Azure, которое является контейнером для функций Azure. Если у вас нет приложения функции, [сначала создайте приложение функции](../azure-functions/functions-create-first-azure-function.md)и убедитесь, что вы выберете .NET в качестве стека времени выполнения.

* Основные знания о [том, как создавать логические приложения](../logic-apps/quickstart-create-first-logic-app-workflow.md)

## <a name="create-logic-app"></a>Создание приложения логики

Для этого сценария у вас есть функция, запускаемый каждым логическим приложением, которое вы хотите вызвать. Во-первых, создайте логическое приложение, которое начинается с триггера запроса HTTP. Функция Azure вызывает эту конечную точку при получении каждого сообщения из очереди.

1. Войдите на [портал Azure](https://portal.azure.com) и создайте пустое приложение логики.

   Если вы пока не знакомы с приложениями логики, ознакомьтесь со статьей [Краткое руководство. Создание первого автоматизированного рабочего процесса с помощью Azure Logic Apps на портале Azure](../logic-apps/quickstart-create-first-logic-app-workflow.md).

1. В поле поиска введите `http request`. Из списка триггеров выберите триггер **ный запрос HTTP.**

   ![Выбор триггера](./media/logic-apps-scenario-function-sb-trigger/when-http-request-received-trigger.png)

   С помощью триггера Запроса можно дополнительно ввести схему JSON для использования с сообщением очереди. Схемы JSON помогают конструктору logic App понять структуру входных данных и упрощают использование выходных данных в рабочем процессе.

1. Чтобы указать схему, введите ее в поле **Схема JSON текста запроса**, например:

   ![Указание схемы JSON](./media/logic-apps-scenario-function-sb-trigger/when-http-request-received-trigger-schema.png)

   Если у вас нет схемы, но у вас есть пример полезных данных в формате JSON, можно создать схему на основе полезных данных.

   1. В триггере запросов выберите **Использовать пример полезной нагрузки, чтобы создать схему**.

   1. Под **Введите или вставьте образец JSON полезной нагрузки,** введите образец полезной нагрузки, а затем выберите **Done**.

      ![Ввод примера полезных данных](./media/logic-apps-scenario-function-sb-trigger/enter-sample-payload.png)

   Этот пример полезных данных создает схему, которая появляется в триггере:

   ```json
   {
      "type": "object",
      "properties": {
         "address": {
            "type": "object",
            "properties": {
               "number": {
                  "type": "integer"
               },
               "street": {
                  "type": "string"
               },
               "city": {
                  "type": "string"
               },
               "postalCode": {
                  "type": "integer"
               },
               "country": {
                  "type": "string"
               }
            }
         }
      }
   }
   ```

1. Добавьте все другие действия, которые вы хотите выполнить после получения сообщения о очереди.

   Например, можно отправить сообщение электронной почты с соединителем Office 365 Outlook.

1. Сохраните свое приложение логики, которое создает URL-адрес обратного вызова для триггера в этом приложении логики. Позже этот URL-адрес обратного вызова используется в коде для триггера Очереди автобусов Azure.

   URL-адрес обратного вызова отображается в свойстве **HTTP POST URL.**

   ![Созданный URL-адрес обратного вызова для триггера](./media/logic-apps-scenario-function-sb-trigger/callback-URL-for-trigger.png)

## <a name="create-azure-function"></a>Создание функции Azure

Далее создайте функцию, которая выступает в качестве триггера и ожидает передачи данных из очереди.

1. На портале Azure откройте и разверните приложение-функцию, если оно еще не открыто. 

1. В разделе с именем приложения-функции разверните **Функции**. На панели **функций** выберите **новую функцию.**

   ![Расширить "Функции" и выбрать "Новая функция"](./media/logic-apps-scenario-function-sb-trigger/add-new-function-to-function-app.png)

1. Выберите этот шаблон на основе того, создали ли вы новое приложение функции, где вы выбрали .NET в качестве стека времени выполнения, или вы используете существующее приложение функции.

   * Для новых функциональных приложений выберите этот шаблон: **триггер очереди служебных автобусов**

     ![Выберите шаблон для нового приложения функции](./media/logic-apps-scenario-function-sb-trigger/current-add-queue-trigger-template.png)

   * Для существующего приложения функции выберите этот шаблон: **триггер очереди автобуса обслуживания - C**

     ![Выберите шаблон для существующего приложения функции](./media/logic-apps-scenario-function-sb-trigger/legacy-add-queue-trigger-template.png)

1. На **триггерном** панели Azure Service Bus Queue укажите имя триггера и настройте соединение шины **обслуживания** для очереди, которое использует слушатель Azure Service Bus SDK, `OnMessageReceive()` и выберите **«Создать».**

1. Напишите основную функцию для вызова ранее созданной точки логического приложения, используя сообщение очереди в качестве триггера. Прежде чем написать функцию, просмотрите следующие соображения:

   * В примере используется сообщение с типом содержимого `application/json`. При необходимости этот тип можно изменить.
   
   * Из-за возможных одновременно работающих функций, больших объемов или больших нагрузок, избегайте мгновенного воспроизведения [класса HTTPClient](https://docs.microsoft.com/dotnet/api/system.net.http.httpclient) с `using` выпиской и непосредственно создавая экземпляры HTTPClient за запрос. Для получения дополнительной информации [см.](https://docs.microsoft.com/dotnet/architecture/microservices/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests#issues-with-the-original-httpclient-class-available-in-net-core)
   
   * Если возможно, повторно использовать экземпляр клиентов HTTP. Для получения дополнительной информации [см. Управление соединениями в функциях Azure.](../azure-functions/manage-connections.md)

   Этот пример использует [ `Task.Run` метод](https://docs.microsoft.com/dotnet/api/system.threading.tasks.task.run) в [асинхронном режиме.](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/async) Для получения дополнительной информации, [см Асинхронное программирование с async и ждать](https://docs.microsoft.com/dotnet/csharp/programming-guide/concepts/async/).

   ```csharp
   using System;
   using System.Threading.Tasks;
   using System.Net.Http;
   using System.Text;

   // Can also fetch from App Settings or environment variable
   private static string logicAppUri = @"https://prod-05.westus.logic.azure.com:443/workflows/<remaining-callback-URL>";

   // Reuse the instance of HTTP clients if possible: https://docs.microsoft.com/azure/azure-functions/manage-connections
   private static HttpClient httpClient = new HttpClient();

   public static async Task Run(string myQueueItem, TraceWriter log) 
   {
      log.Info($"C# ServiceBus queue trigger function processed message: {myQueueItem}");
      var response = await httpClient.PostAsync(logicAppUri, new StringContent(myQueueItem, Encoding.UTF8, "application/json")); 
   }
   ```

1. Чтобы протестировать функцию, добавьте сообщение очереди с помощью такого средства, как [Обозреватель служебной шины](https://github.com/paolosalvatori/ServiceBusExplorer).

   Приложение логики должно активироваться сразу же, как только функция получит это сообщение.

## <a name="next-steps"></a>Дальнейшие действия

* [Вызов, триггер или рабочие процессы гнезда с помощью конечных точек HTTP](../logic-apps/logic-apps-http-endpoint.md)
