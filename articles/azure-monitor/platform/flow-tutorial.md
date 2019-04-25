---
title: Автоматизация процессов журнала Azure Monitor с помощью Microsoft Flow
description: Узнайте, как можно использовать Microsoft Flow для быстрой автоматизации повторяющихся процессов с помощью соединителя Azure Log Analytics.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
ms.service: log-analytics
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/29/2017
ms.author: bwren
ms.openlocfilehash: c3732dd2fa87b00eec38f88ab828605b33567235
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60396602"
---
# <a name="automate-azure-monitor-log-processes-with-the-connector-for-microsoft-flow"></a>Автоматизация процессов журнала Azure Monitor с помощью соединителя для Microsoft Flow
[Microsoft Flow](https://ms.flow.microsoft.com) позволяет создавать автоматизированные рабочие процессы, используя сотни действий для различных служб. Выходные данные одного действия могут использоваться в качестве входных данных другого действия, что позволяет интегрировать различные службы.  Соединитель Azure Log Analytics для Microsoft Flow позволяет создавать рабочие процессы, которые включают данные, полученные запросами журнала из рабочих областей Log Analytics в Azure Monitor.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

Например, с помощью Microsoft Flow вы можете использовать данные журнала Azure Monitor в уведомлении по электронной почте от Office 365, создавать ошибку в Azure DevOps или отправлять сообщение Slack.  Вы можете запускать рабочие процессы с помощью простого расписания или какого-либо действия в подключенной службе, например при получении почты или твита.  

В этой статье содержится руководство о том, как создать поток, который автоматически отправляет по электронной почте результаты запроса журнала Azure Monitor. Это лишь один из примеров того, как можно использовать соединитель Log Analytics в Microsoft Flow. 


## <a name="step-1-create-a-flow"></a>Шаг 1. Создание потока
1. Войдите в [Microsoft Flow](https://flow.microsoft.com) и выберите **Мои потоки**.
2. Щелкните **Создать с нуля**.

## <a name="step-2-create-a-trigger-for-your-flow"></a>Шаг 2. Создание триггера для потока
1. Щелкните **Search hundreds of connectors and triggers** (Поиск по сотням соединителей и триггеров).
2. В поле поиска введите **Расписание**.
3. Выберите **Расписание**, а затем **Расписание — повторение**.
4. В поле **Частота** выберите **День**, а в поле **Интервал** введите **1**.<br><br>![Диалоговое окно триггера Microsoft Flow](media/flow-tutorial/flow01.png)


## <a name="step-3-add-a-log-analytics-action"></a>Шаг 3. Добавление действия Log Analytics
1. Выберите поле **+ Новый шаг**, а затем щелкните **Добавить действие**.
2. Найдите **Log Analytics**.
3. Щелкните **Azure Log Analytics – Run query and visualize results** (Azure Log Analytics – выполнить запрос и отобразить результаты).<br><br>![Окно выполнения запроса Log Analytics](media/flow-tutorial/flow02.png)

## <a name="step-4-configure-the-log-analytics-action"></a>Шаг 4. Настройка действия Log Analytics

1. Укажите сведения для своей рабочей области, включая идентификатор подписки, группу ресурсов и имя рабочей области.
2. В окне **Запрос** добавьте следующий запрос журнала.  Это лишь пример запроса, поэтому вы можете заменить его любым другим запросом, возвращающим данные.
   ```
    Event
    | where EventLevelName == "Error" 
    | where TimeGenerated > ago(1day)
    | summarize count() by Computer
    | sort by Computer
   ```

2. В поле **Тип диаграммы** выберите **Таблица HTML**.<br><br>![Действие Log Analytics](media/flow-tutorial/flow03.png)

## <a name="step-5-configure-the-flow-to-send-email"></a>Шаг 5. Настройка потока для отправки электронной почты

1. Выберите поле **Новый шаг**, а затем щелкните **Добавить действие**.
2. Выполните поиск по запросу **Office 365 Outlook**.
3. Щелкните **Office 365 Outlook – Send an email** (Office 365 Outlook — отправка сообщения электронной почты).<br><br>![Окно выбора Office 365 Outlook](media/flow-tutorial/flow04.png)

4. В окне **Кому** укажите адрес электронной почты получателя, а в окне **Тема** — тему сообщения.
5. Щелкните в любом месте поля **Текст**.  В открывшемся окне **Динамическое содержимое** отобразятся значения из предыдущих действий.  
6. Выберите **Текст**.  Это результаты запроса в действии Log Analytics.
6. Щелкните **Показать дополнительные параметры**.
7. В поле **Является HTML** выберите значение **Да**.<br><br>![Окно настройки сообщения электронной почты Office 365](media/flow-tutorial/flow05.png)

## <a name="step-6-save-and-test-your-flow"></a>Шаг 6. Сохранение и тестирование потока
1. В поле **Имя потока** добавьте имя потока и нажмите кнопку **Создать поток**.<br><br>![Сохранение потока](media/flow-tutorial/flow06.png)
2. Теперь поток создан и будет выполняться через день, как задано в расписании. 
3. Чтобы проверить поток, щелкните **Запустить сейчас**, а затем — **Запустить поток**.<br><br>![Запуск потока](media/flow-tutorial/flow07.png)
3. По завершении потока проверьте почту получателя, которую вы указали.  Вы должны получить сообщение с текстом, подобным этому:<br><br>![Пример электронного сообщения](media/flow-tutorial/flow08.png)


## <a name="next-steps"></a>Дальнейшие действия

- Дополнительные сведения о запросах журнала в Azure Monitor см. в [этой статье](../log-query/log-query-overview.md).
- Дополнительные сведения о [Microsoft Flow](https://ms.flow.microsoft.com).



