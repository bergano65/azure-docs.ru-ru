---
title: Автоматизация процессов Azure Application Insights с помощью Microsoft Flow
description: Узнайте, как можно использовать Microsoft Flow для быстрой автоматизации повторяющихся процессов с помощью соединителя Application Insights.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 03/12/2019
ms.author: mbullwin
ms.openlocfilehash: 15299be83758c157bf3bc7d9fb27b50763b9148e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60903657"
---
# <a name="automate-azure-application-insights-processes-with-the-connector-for-microsoft-flow"></a>Автоматизация процессов Azure Application Insights с помощью соединителя для Microsoft Flow

Постоянно выполняете одинаковые запросы к данным телеметрии, чтобы проверить, что ваша служба работает правильно? Хотите автоматизировать эти запросы для поиска тенденций и аномалий и создавать на их основе собственные рабочие процессы? Соединитель Application Insights для Microsoft Flow предоставляет оптимальные средства для этих целей.

Благодаря этой интеграции можно автоматизировать множество процессов, не написав ни строчки кода. После создания потока с помощью действия Application Insights будет автоматически запущен запрос Application Insights Analytics. 

Вы также можете добавить дополнительные действия. Служба Flow предоставляет сотни действий. Например, вы можете использовать Microsoft Flow, чтобы автоматически отправить уведомление по электронной почте или создать ошибку в Azure DevOps. Вы также можете воспользоваться одним из нескольких [шаблонов](https://ms.flow.microsoft.com/en-us/connectors/shared_applicationinsights/?slug=azure-application-insights), доступных для соединителя для Microsoft Flow. С помощью этих шаблонов можно ускорить создание потока. 

<!--The Application Insights connector also works with [Azure Power Apps](https://powerapps.microsoft.com/en-us/) and [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/?v=17.23h). --> 

## <a name="create-a-flow-for-application-insights"></a>Создание потока для Application Insights

В этом руководстве вы узнаете, как создать поток, использующий алгоритм автоматической кластеризации Analytics, для группирования атрибутов данных для веб-приложения. Отправка результатов по электронной почте — это лишь один из примеров того, как можно совместно использовать Microsoft Flow и Application Insights Analytics. 

### <a name="step-1-create-a-flow"></a>Шаг 1. Создание потока
1. Войдите в службу [Microsoft Flow](https://flow.microsoft.com) и выберите **Мои последовательности**.
2. Нажмите кнопку **New** затем **создание из пустого**.

    ![Создать новый поток с нуля](./media/automate-with-flow/1createflow.png)

### <a name="step-2-create-a-trigger-for-your-flow"></a>Шаг 2. Создание триггера для потока
1. В сборки, на вкладке выберите **расписание**, а затем выберите **расписание — повторение**.

    ![Выберите расписание в разделе сборки](./media/automate-with-flow/2schedule.png)

1. В **интервал** введите **1**, а затем в **частота** выберите **день**.
2. Нажмите кнопку **новый шаг**

    ![Настройка с помощью ввода частоту и интервал повторения в расписании](./media/automate-with-flow/3schedulerecurrence.png)


### <a name="step-3-add-an-application-insights-action"></a>Шаг 3. Добавление действия Application Insights
1. Выполните поиск по запросу **Azure Application Insights**.
2. Нажмите кнопку **Azure Application Insights — визуализация аналитического запроса**.
 
    ![Выберите действие: Запрос Azure визуализация аналитики в Application Insights](./media/automate-with-flow/4visualize.png)

### <a name="step-4-connect-to-an-application-insights-resource"></a>Шаг 4. Подключение к ресурсу Application Insights

Чтобы выполнить этот шаг, необходим идентификатор приложения и ключ API для ресурса. Их можно получить на портале Azure, как показано ниже.

![Идентификатор приложения на портале Azure](./media/automate-with-flow/5apiaccess.png)

![Ключ API на портале Azure](./media/automate-with-flow/6apikey.png)

- Укажите имя подключения, а также идентификатор приложения и ключ API.

    ![Окно подключения Microsoft Flow](./media/automate-with-flow/7connection.png)

### <a name="step-5-specify-the-analytics-query-and-chart-type"></a>Шаг 5. Указание запроса Analytics и типа диаграммы
В этом примере выбираются невыполненные запросы за последний день. Они сопоставляются с исключениями, которые возникли в рамках операции. Analytics сопоставляет их на основе идентификатора operation_Id. Затем запрос разделяет результаты с помощью алгоритма автоматической кластеризации. 

При создании собственных запросов убедитесь, что они работают должным образом в Analytics, прежде чем добавить их в поток.

- Добавьте следующий запрос Analytics и выберите тип диаграммы таблицы HTML. Затем выберите **новый шаг**.

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
    
    ![Окно настройки запроса Analytics](./media/automate-with-flow/8query.png)

### <a name="step-6-configure-the-flow-to-send-email"></a>Шаг 6. Настройка потока для отправки электронной почты

1. Выполните поиск по запросу **Office 365 Outlook**.
2. Щелкните **Office 365 Outlook – Send an email** (Office 365 Outlook — отправка сообщения электронной почты).

    ![Окно выбора Office 365 Outlook](./media/automate-with-flow/9outlookaction.png)

1. В окне **Отправка сообщения электронной почты** выполните следующие действия:

   a. Введите адрес электронной почты получателя.

   2. Введите тему сообщения.

   c. Щелкните в любом месте в поле **Текст**, затем в открывшемся справа меню динамического содержимого выберите **Текст**.

   d. Щелкните **Показать дополнительные параметры**.

    ![Конфигурация Office 365 Outlook](./media/automate-with-flow/10sendemailbody.png)

1. В меню динамического содержимого выполните следующее.

    a. Выберите **Имя вложения**.

    2. Выберите **Содержимое вложения**.
    
    c. В поле **Является HTML** выберите значение **Да**.

    ![Экран настройки сообщения электронной почты Office 365](./media/automate-with-flow/11emailattachment.png)

### <a name="step-7-save-and-test-your-flow"></a>Шаг 7. Сохранение и тестирование потока
- В **имя потока** , добавьте имя последовательности и нажмите кнопку **Сохранить**.

    ![Имя потока и сохраните](./media/automate-with-flow/12nameflow.png)

Вы можете подождать, пока триггер запустит действие, или немедленно запустить поток, [запустив триггер по запросу](https://flow.microsoft.com/blog/run-now-and-six-more-services/).

При выполнении потока получатели, указанные в списке рассылки, получат сообщение электронной почты, которое выглядит следующим образом:

![Пример электронного сообщения](./media/automate-with-flow/flow9.png)


## <a name="next-steps"></a>Дальнейшие действия

- Узнайте больше о создании [запросов Analytics](../../azure-monitor/log-query/get-started-queries.md).
- Дополнительные сведения о [Microsoft Flow](https://ms.flow.microsoft.com).



<!--Link references-->





