---
title: Настройка мониторинга и оповещений для заданий Azure Stream Analytics
description: В этой статье объясняется, как с помощью портала Azure настроить мониторинг и оповещения для заданий Azure Stream Analytics.
services: stream-analytics
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/19/2019
ms.custom: seodec18
ms.openlocfilehash: 4c0d32a201da5befbc8b68148f0b051e283ec289
ms.sourcegitcommit: 82cdc26615829df3c57ee230d99eecfa1c4ba459
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/19/2019
ms.locfileid: "54412397"
---
# <a name="set-up-alerts-for-azure-stream-analytics-jobs"></a>Настройка оповещений для заданий Azure Stream Analytics

Очень важно отслеживать задание Azure Stream Analytics, чтобы убедиться, что оно выполняется непрерывно и без каких-либо проблем. В этой статье описывается, как настроить оповещения для распространенных сценариев, которые необходимо отслеживать. 

Правила на основе метрик можно настраивать через портал и [программным путем](https://code.msdn.microsoft.com/windowsazure/Receive-Email-Notifications-199e2c9a), используя данные в журналах операций.

## <a name="set-up-alerts-in-the-azure-portal"></a>Настройка оповещений на портале Azure

Ниже приведен пример того, как настроить оповещения, когда задание переходит в состояние сбоя. Рекомендуется настроить это оповещение для всех заданий.

1. На портале Azure откройте задание Stream Analytics, для которого требуется создать оповещение.

2. На странице **Задание** перейдите к разделу **Мониторинг**.  

3. Выберите пункт **Метрики**, а затем щелкните **Новое правило генерации оповещений**.

   ![Настройка оповещений Stream Analytics на портале Azure](./media/stream-analytics-set-up-alerts/stream-analytics-set-up-alerts.png)  

4. Имя задания Stream Analytics должно автоматически появиться в разделе **Ресурс**. Щелкните **Добавить условие** и выберите **All Administrative operations** (Все административные операции) в разделе **Настроить логику сигналов**.

   ![Выбор названия сигнала для оповещения Stream Analytics](./media/stream-analytics-set-up-alerts/stream-analytics-condition-signal.png)  

5. В разделе **Настроить логику сигналов** задайте для параметра **Уровень события** значение **Все**, а для параметра **Состояние** — значение **Сбой**. Оставьте поле **Кем инициировано событие** пустым и нажмите кнопку **Готово**.

   ![Настройка логики сигналов для оповещения Stream Analytics](./media/stream-analytics-set-up-alerts/stream-analytics-configure-signal-logic.png) 

6. Создайте группу действий или выберите имеющуюся. В этом примере новая группа действий **TIDashboardGroupActions** была создана с действием **Сообщения электронной почты**, которое отправляет сообщение пользователям с ролью **Владелец** Azure Resource Manager.

   ![Настройка оповещений для заданий Azure Streaming Analytics](./media/stream-analytics-set-up-alerts/stream-analytics-add-group-email-action.png)

7. В полях **Ресурс**, **Условие** и **Группы действий** должны быть записи.

   ![Создание правила генерации оповещений Stream Analytics](./media/stream-analytics-set-up-alerts/stream-analytics-create-alert-rule-2.png)

   В разделе **Сведения об оповещении** введите значения в поля **Имя правила генерации оповещений** и **Описание**, укажите свою **группу ресурсов** и щелкните **Создать правило генерации оповещений**, чтобы создать правило для задания Stream Analytics.

   ![Создание правила генерации оповещений Stream Analytics](./media/stream-analytics-set-up-alerts/stream-analytics-create-alert-rule.png)

## <a name="scenarios-to-monitor"></a>Сценарии для мониторинга

Для мониторинга производительности задания Stream Analytics рекомендуется использовать следующие оповещения. Эти метрики должны оцениваться каждую минуту за последний 5-минутный период. Если во время выполнения задания наблюдаются проблемы с производительностью, оптимизируйте его, используя параллелизацию запросов, и попробуйте увеличить число единиц потоковой передачи.

|Метрика|Условие|Агрегат времени|Threshold (Пороговое значение)|Корректирующие действия|
|-|-|-|-|-|
|Использование единиц потоковой передачи (%)|Больше|Максимальная|80|Существуют несколько факторов, повышающих процент использования единиц потоковой передачи. Вы можете масштабировать с применением параллелизации запросов или увеличить число единиц потоковой передачи. Дополнительные сведения см. в статье [Использование параллелизации запросов в Azure Stream Analytics](stream-analytics-parallelization.md).|
|Ошибки среды выполнения|Больше|Всего|0|Изучите журналы действий или диагностики и внесите соответствующие изменения во входные данные, запросы или выходные данные.|
|Предельная задержка|Больше|Максимальная|Если среднее значение метрики за последние 15 минут больше допустимого интервала поступления с задержкой (в секундах). Если вы не изменили допустимый интервал поступления с задержкой, значение по умолчанию устанавливается на 5 секунд.|Попробуйте увеличить число единиц потоковой передачи или выполнить параллелизацию запроса. Дополнительные сведения о единицах потоковой передачи см. в статье [Обзор и настройка единиц потоковой передачи](stream-analytics-streaming-unit-consumption.md#how-many-sus-are-required-for-a-job). Дополнительные сведения см. в статье [Использование параллелизации запросов в Azure Stream Analytics](stream-analytics-parallelization.md).|
|Ошибки десериализации входных данных|Больше|Всего|0|Изучите журналы действий или диагностики и внесите соответствующие изменения во входные данные. Дополнительные сведения о журналах диагностики см. в статье [Устранение неполадок Azure Stream Analytics с помощью журналов диагностики](stream-analytics-job-diagnostic-logs.md).|

## <a name="get-help"></a>Получение справки

Дополнительные сведения о настройке оповещений на портале Azure см. в статье [Получение уведомлений](../monitoring-and-diagnostics/insights-receive-alert-notifications.md).  

За дополнительной помощью обращайтесь на наш [форум Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Дальнейшие действия
* [Введение в Azure Stream Analytics](stream-analytics-introduction.md)
* [Приступая к работе с Azure Stream Analytics](stream-analytics-get-started.md)
* [Масштабирование заданий в службе Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Справочник по языку запросов Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Справочник по API-интерфейсу REST управления Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

