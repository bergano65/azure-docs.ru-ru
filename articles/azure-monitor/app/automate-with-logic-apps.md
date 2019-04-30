---
title: Автоматизация процессов Azure Application Insights с помощью Logic Apps.
description: Узнайте, как можно быстро автоматизировать повторяющиеся процессы, добавив соединитель Application Insights в приложение логики.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: mbullwin
ms.openlocfilehash: 61215adc2aee5cef3693d119bf0efb36526d748b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60904836"
---
# <a name="automate-application-insights-processes-by-using-logic-apps"></a>Автоматизация процессов Application Insights с помощью Logic Apps

Постоянно выполняете одинаковые запросы к данным телеметрии, чтобы проверить, что ваша служба работает правильно? Хотите автоматизировать эти запросы для поиска тенденций и аномалий и создавать на их основе собственные рабочие процессы? Соединитель Application Insights для Logic Apps предоставляет оптимальные средства для этой цели.

Благодаря этой интеграции можно автоматизировать множество процессов, не написав ни строчки кода. Можно создать приложение логики с соединителем Application Insights, чтобы быстро автоматизировать любой процесс Application Insights. 

Вы также можете добавить дополнительные действия. Компонент Logic Apps службы приложений Azure предоставляет сотни действий. Например, используя приложение логики, вы можете автоматически отправить уведомление по электронной почте или создать ошибку в Azure DevOps. Вы можете также воспользоваться одним из множества доступных [шаблонов](https://docs.microsoft.com/azure/logic-apps/logic-apps-use-logic-app-templates). С их помощью можно ускорить процесс создания приложения логики. 

## <a name="create-a-logic-app-for-application-insights"></a>Создание приложения логики для Application Insights

В этом руководстве вы узнаете, как создать приложение логики, использующее алгоритм автоматической кластеризации Analytics, для группирования атрибутов в данных для веб-приложения. Отправка результатов по электронной почте — это лишь один из примеров того, как можно совместно использовать Logic Apps и Application Insights Analytics. 

### <a name="step-1-create-a-logic-app"></a>Шаг 1. Создайте приложение логики
1. Войдите на [портале Azure](https://portal.azure.com).
1. Щелкните **Создать ресурс**, выберите **Интернет и мобильные устройства**, а затем — **Logic App**.

    ![Окно создания приложения логики](./media/automate-with-logic-apps/1createlogicapp.png)

### <a name="step-2-create-a-trigger-for-your-logic-app"></a>Шаг 2. Создание триггера для приложения логики
1. В окне **конструктора приложений логики** в разделе **Начинаем с часто используемого триггера** выберите **Повторение**.

    ![Окно конструктора приложений логики](./media/automate-with-logic-apps/2logicappdesigner.png)

1. В **интервал** введите **1** и затем**частота** выберите **день**.

    ![Окно "Повторение" в конструкторе приложений логики](./media/automate-with-logic-apps/3recurrence.png)

### <a name="step-3-add-an-application-insights-action"></a>Шаг 3. Добавление действия Application Insights
1. Нажмите кнопку **новый шаг**.

1. В поле поиска **Выберите действие** введите **Azure Application Insights**.

1. В разделе **действия**, нажмите кнопку **Azure Application Insights — визуализация аналитического запроса**.

    ![Окно "Выберите действие" в конструкторе приложений логики](./media/automate-with-logic-apps/4visualize.png)

### <a name="step-4-connect-to-an-application-insights-resource"></a>Шаг 4. Подключение к ресурсу Application Insights

Чтобы выполнить этот шаг, необходим идентификатор приложения и ключ API для ресурса. Их можно получить на портале Azure, как показано ниже.

![Идентификатор приложения на портале Azure](./media/automate-with-logic-apps/5apiaccess.png)

![Идентификатор приложения на портале Azure](./media/automate-with-logic-apps/6apikey.png)

Укажите имя подключения, а также идентификатор приложения и ключ API.

![Окно подключения потока в конструкторе приложений логики](./media/automate-with-logic-apps/7connection.png)

### <a name="step-5-specify-the-analytics-query-and-chart-type"></a>Шаг 5. Указание запроса Analytics и типа диаграммы
В следующем примере выбираются невыполненные запросы за последний день. Они сопоставляются с исключениями, которые возникли в рамках операции. Analytics сопоставляет такие запросы на основе идентификатора operation_Id. Затем запрос разделяет результаты с помощью алгоритма автоматической кластеризации. 

При создании собственных запросов убедитесь, что они работают должным образом в Analytics, прежде чем добавить их в поток.

1. В поле **Запрос** добавьте следующий запрос Analytics:

    ```
    requests
    | where timestamp > ago(1d)
    | where success == "False"
    | project name, operation_Id
    | join ( exceptions
        | project problemId, outerMessage, operation_Id
    ) on operation_Id
    | evaluate autocluster()
    ```

1. В поле **Тип диаграммы** выберите **Таблица HTML**.

    ![Окно настройки запроса Analytics](./media/automate-with-logic-apps/8query.png)

### <a name="step-6-configure-the-logic-app-to-send-email"></a>Шаг 6. Настройка приложения логики для отправки сообщения электронной почты

1. Нажмите кнопку **новый шаг**.

1. В поле поиска введите **Office 365 Outlook**.

1. Щелкните **Office 365 Outlook – Send an email** (Office 365 Outlook — отправка сообщения электронной почты).

    ![Выбор Office 365 Outlook](./media/automate-with-logic-apps/9sendemail.png)

1. В окне **Отправка сообщения электронной почты** выполните следующие действия:

   a. Введите адрес электронной почты получателя.

   2. Введите тему сообщения.

   c. Щелкните в любом месте в поле **Текст**, затем в открывшемся справа меню динамического содержимого выберите **Текст**.
    
   d. Нажмите кнопку **добавьте параметр** раскрывающийся список и выберите вложений и является HTML.

      ![Конфигурация Office 365 Outlook](./media/automate-with-logic-apps/10emailbody.png)

      ![Конфигурация Office 365 Outlook](./media/automate-with-logic-apps/11emailparameter.png)

1. В меню динамического содержимого выполните следующее.

    a. Выберите **Имя вложения**.

    2. Выберите **Содержимое вложения**.
    
    c. В поле **Является HTML** выберите значение **Да**.

      ![Экран настройки электронного сообщения Office 365](./media/automate-with-logic-apps/12emailattachment.png)

### <a name="step-7-save-and-test-your-logic-app"></a>Шаг 7. Сохранение и тестирование приложения логики
* Нажмите кнопку **Сохранить**, чтобы сохранить изменения.

Можно либо подождать, пока триггер не запустит приложение логики, либо запустить его немедленно, щелкнув **Запуск**.

![Экран создания приложения логики](./media/automate-with-logic-apps/13save.png)

При выполнении приложения логики получатели, указанные в списке рассылки, получат сообщение электронной почты, которое выглядит следующим образом:

![Сообщение электронной почты приложения логики](./media/automate-with-logic-apps/flow9.png)

## <a name="next-steps"></a>Дальнейшие действия

- Узнайте больше о создании [запросов Analytics](../../azure-monitor/log-query/get-started-queries.md).
- Дополнительные сведения о [Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-what-are-logic-apps).



<!--Link references-->





