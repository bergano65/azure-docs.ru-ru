---
title: Журналы запросов Azure Monitor для мониторинга кластеров Azure HDInsight
description: Узнайте, как выполнять запросы в журналы Azure Monitor для мониторинга заданий, выполняемых в кластере HDInsight.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/05/2018
ms.author: hrasheed
ms.openlocfilehash: cbaaed3fff99778bfab1feeacdab02bf8245a85a
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2019
ms.locfileid: "64714701"
---
# <a name="query-azure-monitor-logs-to-monitor-hdinsight-clusters"></a>Журналы запросов Azure Monitor для мониторинга кластеров HDInsight

Узнайте, некоторые базовые сценарии, о том, как использовать журналы Azure Monitor для мониторинга кластеров Azure HDInsight:

* [Анализ метрик кластера HDInsight](#analyze-hdinsight-cluster-metrics).
* [Поиск определенных сообщений журнала](#search-for-specific-log-messages).
* [Создание оповещений о событиях](#create-alerts-for-tracking-events).

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Технические условия

* Вы должны настроить кластер HDInsight и использовать журналы Azure Monitor и добавили журналы Azure Monitor связанные с кластером HDInsight, решения в рабочую область для мониторинга. Инструкции см. в разделе [журналов использования Azure Monitor с кластерами HDInsight](hdinsight-hadoop-oms-log-analytics-tutorial.md).

## <a name="analyze-hdinsight-cluster-metrics"></a>Анализ метрик кластера HDInsight

Узнайте, как выполнить поиск определенных метрик для кластера HDInsight.

1. Откройте рабочую область Log Analytics, которая связана с вашим кластером HDInsight на портале Azure.
2. Выберите плитку **Поиск по журналам**.
3. Введите следующий запрос в поле поиска для поиска всех метрик для всех доступных метрик для всех кластеров HDInsight, настроенных для использования Azure Monitor журналы, а затем выберите **ЗАПУСКА**.

        search *

    ![Поиск всех метрик](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-metrics.png "Поиск всех метрик")

    Выходные данные должны выглядеть так:

    ![Выходные данные поиска всех метрик](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-metrics-output.png "Выходные данные поиска всех метрик")

5. В левой области в категории **Тип** выберите метрику, которую необходимо изучить подробнее, и нажмите кнопку **Применить**. На следующем снимке экрана показано, что выбран тип `metrics_resourcemanager_queue_root_default_CL`.

    > [!NOTE]  
    > Чтобы найти необходимую метрику, может потребоваться нажать кнопку **[+] Дополнительно**. Кроме того, кнопка **Применить** находится в нижней части списка, поэтому нужно прокрутить вниз, чтобы увидеть ее.

    Обратите внимание, что запрос в текстовом поле изменяется на тот, который показан в выделенном поле на следующем снимке экрана:

    ![Поиск конкретных метрик](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-metrics.png "Поиск конкретных метрик")

6. Можно получить более подробную информацию о конкретной метрике. Например, можно уточнить существующие выходные данные по среднему количеству ресурсов, использованных за 10-минутный интервал, классифицировав их по имени кластера с помощью следующего запроса:

        search in (metrics_resourcemanager_queue_root_default_CL) * | summarize AggregatedValue = avg(UsedAMResourceMB_d) by ClusterName_s, bin(TimeGenerated, 10m)

7. Вместо уточнения по среднему количеству используемых ресурсов можно использовать следующий запрос, чтобы уточнить результаты на основе максимального использования ресурсов в 10-минутном интервале (а также 90-го и 95-го процентиля):

        search in (metrics_resourcemanager_queue_root_default_CL) * | summarize ["max(UsedAMResourceMB_d)"] = max(UsedAMResourceMB_d), ["pct95(UsedAMResourceMB_d)"] = percentile(UsedAMResourceMB_d, 95), ["pct90(UsedAMResourceMB_d)"] = percentile(UsedAMResourceMB_d, 90) by ClusterName_s, bin(TimeGenerated, 10m)

## <a name="search-for-specific-log-messages"></a>Поиск определенных сообщений журнала

Узнайте, как искать сообщения об ошибках за определенный интервал времени. Эти шаги являются всего лишь одним примером того, как можно получить сообщение об ошибке, которое вас интересует. Для поиска нужных ошибок можно использовать любое доступное свойство.

1. Откройте рабочую область Log Analytics, которая связана с вашим кластером HDInsight на портале Azure.
2. Выберите плитку **Поиск по журналам**.
3. Введите следующий запрос для поиска всех сообщений об ошибках для всех кластеров HDInsight, настроенных для использования Azure Monitor журналы, а затем выберите **ЗАПУСКА**. 

         search "Error"

    Должны отобразиться выходные данные, аналогичные указанным ниже.

    ![Выходные данные поиска всех ошибок](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-errors-output.png "Выходные данные поиска всех ошибок")

4. В левой области в категории **Тип** найдите тип ошибки, который необходимо изучить подробнее, и нажмите кнопку **Применить**.  Обратите внимание, что результаты уточнены и показывают только ошибки выбранного типа.
5. Можно получить более подробную информацию о конкретном списке ошибок, используя параметры, доступные на панели слева. Например: 

    - чтобы просмотреть сообщения об ошибках из определенного рабочего узла, сделайте следующее:

        ![Выходные данные поиска конкретных ошибок](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error-refined.png "Выходные данные поиска конкретных ошибок")

    - Чтобы просмотреть ошибку, которая возникла в определенное время, сделайте следующее:

        ![Выходные данные поиска конкретных ошибок](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error-time.png "Выходные данные поиска конкретных ошибок")

6. Чтобы просмотреть определенную ошибку, сделайте следующее. Щелкните **[+]show more** ([+] больше), чтобы просмотреть фактическое сообщение об ошибке.

    ![Выходные данные поиска конкретных ошибок](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error-arrived.png "Выходные данные поиска конкретных ошибок")

## <a name="create-alerts-for-tracking-events"></a>Создание оповещений для отслеживания событий

Первым шагом создания оповещения является получение запроса, на основе которого активируется оповещение. Для создания оповещения можно использовать любой запрос.

1. Откройте рабочую область Log Analytics, которая связана с вашим кластером HDInsight на портале Azure.
2. Выберите плитку **Поиск по журналам**.
3. Выполните следующий запрос, на основе которого требуется создать оповещение, а затем нажмите клавишу **ЗАПУСК**.

        metrics_resourcemanager_queue_root_default_CL | where AppsFailed_d > 0

    Запрос вернет список запущенных на кластерах HDInsight приложений, в которых возник сбой.

4. Выберите **Новое правило генерации оповещений** в верхней части страницы.

    ![Ввод запроса для создания оповещения](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-create-alert-query.png "Ввод запроса для создания оповещения")

5. В окне **Создать правило** введите запрос и другие сведения, чтобы создать оповещение, а затем щелкните **Создать правило генерации оповещений**.

    ![Ввод запроса для создания оповещения](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-create-alert.png "Ввод запроса для создания оповещения")

Чтобы изменить или удалить оповещение:

1. Откройте рабочую область Log Analytics на портале Azure.
2. В меню слева выберите **Оповещение**.
3. Выберите оповещение, которое требуется изменить или удалить.
4. Доступны следующие варианты: **Сохранить**, **Отменить**, **Отключить** и **Удалить**.

    ![Изменить Удаление оповещения журналов HDInsight Azure Monitor](media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-edit-alert.png)

Дополнительные сведения см. в статье [Создание и просмотр оповещений метрик, а также управление ими с помощью Azure Monitor](../azure-monitor/platform/alerts-metric.md).

## <a name="see-also"></a>См. также

* [Создавать пользовательские представления с помощью конструктора представлений в Azure Monitor](../azure-monitor/platform/view-designer.md)
* [Создание, просмотр и управление оповещения метрик с помощью Azure Monitor](../azure-monitor/platform/alerts-metric.md)
