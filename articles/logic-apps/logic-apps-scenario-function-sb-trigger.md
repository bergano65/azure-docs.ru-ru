---
title: Вызов или запуск приложений логики с помощью функций Azure и служебной шины Azure
description: Создание функции Azure, которые вызов или запуск приложений логики с помощью служебной шины Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: jehollan, klam, LADocs
ms.topic: article
ms.assetid: 19cbd921-7071-4221-ab86-b44d0fc0ecef
ms.date: 06/04/2019
ms.openlocfilehash: 3d4f642ae25a179ea2c3241240996da774cd8c23
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/04/2019
ms.locfileid: "66494970"
---
# <a name="call-or-trigger-logic-apps-by-using-azure-functions-and-azure-service-bus"></a>Вызов или запуск приложений логики с помощью функций Azure и служебной шины Azure

Можно использовать ["функции Azure"](../azure-functions/functions-overview.md) для запуска приложения логики в том случае, когда требуется развернуть долго выполняющиеся прослушиватели или задачи. Например, можно создать функцию Azure, которая ожидает передачи данных [служебной шины Azure](../service-bus-messaging/service-bus-messaging-overview.md) ставить в очередь и немедленно запускает приложение логики с помощью извещающего триггера.

## <a name="prerequisites"></a>Технические условия

* Подписка Azure. Если у вас еще нет подписки Azure, [зарегистрируйтесь для получения бесплатной учетной записи Azure](https://azure.microsoft.com/free/).

* Пространство имен служебной шины Azure. Если у вас нет пространства имен, [сначала создать пространство имен](../service-bus-messaging/service-bus-create-namespace-portal.md).

* Приложения-функции Azure, который является контейнером для функций Azure. Если у вас нет приложения-функции, [сначала создать приложение-функцию](../azure-functions/functions-create-first-azure-function.md)и убедитесь, что .NET как стека времени выполнения.

* Базовые знания [создания приложений логики](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-logic-app"></a>Создание приложения логики

В этом сценарии у вас есть функции, выполняемой каждого приложения логики, которое вы хотите активировать. Во-первых создайте приложение логики, которое начинается с триггер HTTP-запроса. Функция Azure вызывает эту конечную точку при получении каждого сообщения из очереди.  

1. Войдите на [портал Azure](https://portal.azure.com) и создайте пустое приложение логики.

   Если вы знакомы с приложениями логики, просмотрите [краткое руководство: Создание первого автоматизированного рабочего процесса с помощью Azure Logic Apps на портале Azure](../logic-apps/quickstart-create-first-logic-app-workflow.md).

1. В поле поиска введите фильтр "HTTP-запрос". В списке триггеров выберите триггер: **При получении HTTP-запроса**

   ![Выбор триггера](./media/logic-apps-scenario-function-sb-trigger/when-http-request-received-trigger.png)

   В триггере запроса при необходимости можно ввести схему JSON для использования для очереди сообщений. JSON схемы помогают конструктору приложений логики понять структуру для входных данных и упростить выходные данные для использования в рабочем процессе.

1. Чтобы указать схему, введите ее в поле **Схема JSON текста запроса**, например:

   ![Указание схемы JSON](./media/logic-apps-scenario-function-sb-trigger/when-http-request-received-trigger-schema.png)

   Если у вас нет схемы, но у вас есть пример полезных данных в формате JSON, можно создать схему на основе полезных данных.

   1. В триггере запросов выберите **Использовать пример полезной нагрузки, чтобы создать схему**.

   1. В разделе **Введение или вставка примера полезных данных JSON** введите свой пример полезных данных, а затем выберите **Готово**.

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

1. Добавьте любые другие действия, которые вы хотите запустить после получения сообщения очереди.

   Например, можно отправить сообщение электронной почты с соединителем Office 365 Outlook.

1. Сохраните свое приложение логики, которое создает URL-адрес обратного вызова для триггера в этом приложении логики. Позже используйте этот URL-адрес обратного вызова в коде для триггера очереди служебной шины Azure.

   Отображается в URL-адрес обратного вызова **URL-адрес HTTP POST** свойство.

   ![Созданный URL-адрес обратного вызова для триггера](./media/logic-apps-scenario-function-sb-trigger/callback-URL-for-trigger.png)

## <a name="create-azure-function"></a>Создание функции Azure

Далее создайте функцию, которая выступает в качестве триггера и ожидает передачи данных из очереди.

1. На портале Azure откройте и разверните приложение-функцию, если оно еще не открыто. 

1. В разделе с именем приложения-функции разверните **Функции**. На панели **Функции** выберите **Новая функция**.

   ![Разверните узел «Функции» и выберите «Новая функция»](./media/logic-apps-scenario-function-sb-trigger/create-new-function.png)

1. Выберите этот шаблон, в зависимости от того, вы создали приложение-функцию, если вы выбрали .NET как стека времени выполнения, или вы используете существующее приложение-функцию.

   * Для нового приложения-функции выберите этот шаблон: **Триггер очередей служебной шины**

     ![Выберите шаблон для нового приложения-функции](./media/logic-apps-scenario-function-sb-trigger/current-add-queue-trigger-template.png)

   * Для имеющегося приложения-функции выберите этот шаблон: **Триггер очереди служебной шины —C#**

     ![Выберите шаблон для имеющегося приложения-функции](./media/logic-apps-scenario-function-sb-trigger/legacy-add-queue-trigger-template.png)

1. На **триггера очереди служебной шины Azure** области, укажите имя для своего триггера, а настройка **подключение к служебной шине** для очереди, с использованием пакета SDK служебной шины Azure `OnMessageReceive()` прослушиватель и выберите пункт **Создание**.

1. Напишите простую функцию для вызова конечной точки приложения логики, ранее созданного с помощью сообщения очереди в качестве триггера. В примере используется сообщение с типом содержимого `application/json`. При необходимости этот тип можно изменить. Если это возможно повторно использовать экземпляр HTTP-клиентов. Дополнительные сведения см. в разделе [Управление подключениями в функциях Azure](../azure-functions/manage-connections.md).

   ```CSharp
   using System;
   using System.Threading.Tasks;
   using System.Net.Http;
   using System.Text;

   // Callback URL for previously created Request trigger
   private static string logicAppUri = @"https://prod-05.westus.logic.azure.com:443/workflows/<remaining-callback-URL>";

   // Reuse the instance of HTTP clients if possible
   private static HttpClient httpClient = new HttpClient();

   public static void Run(string myQueueItem, ILogger log)
   {
       log.LogInformation($"C# ServiceBus queue trigger function processed message: {myQueueItem}");

       var response = httpClient.PostAsync(logicAppUri, new StringContent(myQueueItem, Encoding.UTF8, "application/json")).Result;
   }
   ```

1. Чтобы протестировать функцию, добавьте сообщение очереди с помощью такого средства, как [Обозреватель служебной шины](https://github.com/paolosalvatori/ServiceBusExplorer).

   Приложение логики должно активироваться сразу же, как только функция получит это сообщение.

## <a name="next-steps"></a>Дальнейшие действия

[Вызов, активация или создания вложенных рабочих процессов с помощью конечных точек HTTP](../logic-apps/logic-apps-http-endpoint.md)