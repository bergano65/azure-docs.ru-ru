---
title: Автоматизация процессов Azure Application Insights с помощью Microsoft Flow
description: Узнайте, как можно использовать Microsoft Flow для быстрой автоматизации повторяющихся процессов с помощью соединителя Application Insights.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 08/29/2019
ms.openlocfilehash: ff0896498c0270b8eb43b762228916985f924def
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/21/2019
ms.locfileid: "72678325"
---
# <a name="automate-azure-application-insights-processes-with-the-connector-for-microsoft-flow"></a>Автоматизация процессов Azure Application Insights с помощью соединителя для Microsoft Flow

Постоянно выполняете одинаковые запросы к данным телеметрии, чтобы проверить, что ваша служба работает правильно? Хотите автоматизировать эти запросы для поиска тенденций и аномалий и создавать на их основе собственные рабочие процессы? Соединитель Azure Application Insights для Microsoft Flow — это правильный инструмент для этих целей.

Благодаря этой интеграции можно автоматизировать множество процессов, не написав ни строчки кода. После создания потока с помощью действия Application Insights будет автоматически запущен запрос Application Insights Analytics.

Вы также можете добавить дополнительные действия. Служба Flow предоставляет сотни действий. Например, вы можете использовать Microsoft Flow, чтобы автоматически отправить уведомление по электронной почте или создать ошибку в Azure DevOps. Вы также можете воспользоваться одним из нескольких [шаблонов](https://ms.flow.microsoft.com/connectors/shared_applicationinsights/?slug=azure-application-insights), доступных для соединителя для Microsoft Flow. С помощью этих шаблонов можно ускорить создание потока.

<!--The Application Insights connector also works with [Azure Power Apps](https://powerapps.microsoft.com/) and [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/?v=17.23h). -->

## <a name="create-a-flow-for-application-insights"></a>Создание потока для Application Insights

В этом руководстве вы узнаете, как создать последовательность, использующую алгоритм автокластера аналитики для группирования атрибутов в данных для веб-приложения. Отправка результатов по электронной почте — это лишь один из примеров того, как можно совместно использовать Microsoft Flow и Application Insights Analytics.

### <a name="step-1-create-a-flow"></a>Шаг 1. Создание потока

1. Войдите в службу [Microsoft Flow](https://flow.microsoft.com) и выберите **Мои потоки**.
2. Щелкните **создать** **, затем — по расписанию — с пустого**поля.

    ![Создать новый поток из пустого расписания](./media/automate-with-flow/1-create.png)

### <a name="step-2-create-a-trigger-for-your-flow"></a>Шаг 2. Создание триггера для потока

1. Во всплывающем окне **Создайте запланированный поток**, заполните Имя последовательности и частоту выполнения последовательности.

    ![Настройка повторения расписания с вводом частоты и интервала](./media/automate-with-flow/2-schedule.png)

1. Щелкните **Create**(Создать).

### <a name="step-3-add-an-application-insights-action"></a>Шаг 3. Добавление действия Application Insights

1. Выполните поиск по запросу **Application Insights**.
2. Щелкните **Azure Application Insights — визуализировать аналитический запрос**.

    ![Выберите действие: Azure Application Insights визуализировать аналитический запрос](./media/automate-with-flow/3-visualize.png)

3. Выберите **Новый шаг**.

### <a name="step-4-connect-to-an-application-insights-resource"></a>Шаг 4. Подключение к ресурсу Application Insights

Чтобы выполнить этот шаг, необходим идентификатор приложения и ключ API для ресурса. Их можно получить на портале Azure, как показано ниже.

![Идентификатор приложения на портале Azure](./media/automate-with-flow/5apiaccess.png)

![Ключ API в портал Azure](./media/automate-with-flow/6apikey.png)

Укажите имя подключения, а также идентификатор приложения и ключ API.

   ![Окно подключения Microsoft Flow](./media/automate-with-flow/4-connection.png)

Если окно подключения не отображается сразу же, а вместо ввода запроса, нажмите кнопку с многоточием в правом верхнем углу окна. Затем выберите "Мои подключения" или используйте существующее.

Щелкните **Create**(Создать).

### <a name="step-5-specify-the-analytics-query-and-chart-type"></a>Шаг 5. Указание запроса Analytics и типа диаграммы
В этом примере выбираются невыполненные запросы за последний день. Они сопоставляются с исключениями, которые возникли в рамках операции. Analytics сопоставляет их на основе идентификатора operation_Id. Затем запрос разделяет результаты с помощью алгоритма автоматической кластеризации.

При создании собственных запросов убедитесь, что они работают должным образом в Analytics, прежде чем добавить их в поток.

- Добавьте следующий аналитический запрос и выберите тип диаграммы Таблица HTML. Затем выберите **новый шаг**.

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
    
    ![Окно настройки запроса Analytics](./media/automate-with-flow/5-query.png)

### <a name="step-6-configure-the-flow-to-send-email"></a>Шаг 6. Настройка потока для отправки электронной почты

1. Выполните поиск по запросу **Office 365 Outlook**.
2. Щелкните **Office 365 Outlook – Send an email** (Office 365 Outlook — отправка сообщения электронной почты).

    ![Окно выбора Office 365 Outlook](./media/automate-with-flow/6-outlook.png)

1. В окне **Отправить сообщение электронной почты** :

   а) Введите адрес электронной почты получателя.

   б) Введите тему сообщения.

   в) Щелкните в любом месте в поле **Текст**, затем в открывшемся справа меню динамического содержимого выберите **Текст**.

   д) Выберите пункт " **отобразить дополнительные параметры** ".

1. В меню динамического содержимого:

    а) Выберите **Имя вложения**.

    б) Выберите **Содержимое вложения**.
    
    в) В поле **Является HTML** выберите значение **Да**.

    ![Конфигурация Office 365 Outlook](./media/automate-with-flow/7-email.png)

### <a name="step-7-save-and-test-your-flow"></a>Шаг 7. Сохранение и тестирование потока

В нижней части страницы нажмите кнопку **Save**.

Можно подождать, пока триггер запустит это действие, или щелкнуть значок теста ![beaker ](./media/automate-with-flow/testicon.png) **тест** в верхней части.

После выбора **теста**:

1. Выберите **действие триггера**.
2. Выберите **выполнить последовательность**.

При выполнении последовательности получатели, указанные в списке рассылки, получают сообщение электронной почты, подобное приведенному ниже.

![Пример электронного сообщения](./media/automate-with-flow/flow9.png)

## <a name="next-steps"></a>Дальнейшие действия

- Узнайте больше о создании [запросов Analytics](../../azure-monitor/log-query/get-started-queries.md).
- Дополнительные сведения о [Microsoft Flow](https://ms.flow.microsoft.com).

<!--Link references-->