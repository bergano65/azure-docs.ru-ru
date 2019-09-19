---
title: Запрос журналов Azure Monitor для мониторинга кластеров Azure HDInsight
description: Узнайте, как выполнять запросы к журналам Azure Monitor для мониторинга заданий, выполняемых в кластере HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/05/2018
ms.openlocfilehash: 51344ff7381b6392870b1fd0e331eed38a33915d
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/18/2019
ms.locfileid: "71103519"
---
# <a name="query-azure-monitor-logs-to-monitor-hdinsight-clusters"></a>Запрос журналов Azure Monitor для мониторинга кластеров HDInsight

Ознакомьтесь с некоторыми базовыми сценариями использования журналов Azure Monitor для мониторинга кластеров Azure HDInsight.

* [Анализ метрик кластера HDInsight](#analyze-hdinsight-cluster-metrics).
* [Поиск определенных сообщений журнала](#search-for-specific-log-messages).
* [Создание оповещений о событиях](#create-alerts-for-tracking-events).

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Предварительные требования

Вы должны настроить кластер HDInsight для использования журналов Azure Monitor и добавить в нее решения для мониторинга Azure Monitor журналов, относящиеся к кластеру HDInsight. Инструкции см. в статье [Использование журналов Azure Monitor с кластерами HDInsight](hdinsight-hadoop-oms-log-analytics-tutorial.md).

## <a name="analyze-hdinsight-cluster-metrics"></a>Анализ метрик кластера HDInsight

Узнайте, как выполнить поиск определенных метрик для кластера HDInsight.

1. Откройте рабочую область Log Analytics, которая связана с вашим кластером HDInsight на портале Azure.
1. Выберите плитку **Поиск по журналам**.
1. Введите следующий запрос в поле поиска, чтобы найти все метрики всех доступных метрик для всех кластеров HDInsight, настроенных для использования журналов Azure Monitor, а затем выберите **выполнить**.

        search *

    ![Apache Ambari Analytics Поиск всех метрик](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-metrics.png "Поиск по всем метрикам")

    Выходные данные должны выглядеть так:

    ![Поиск в log Analytics всех метрик](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-metrics-output.png "Поиск всех выходных данных метрик")

1. В левой области в категории **Тип** выберите метрику, которую необходимо изучить подробнее, и нажмите кнопку **Применить**. На следующем снимке экрана показано, что выбран тип `metrics_resourcemanager_queue_root_default_CL`.

    > [!NOTE]  
    > Чтобы найти необходимую метрику, может потребоваться нажать кнопку **[+] Дополнительно**. Кроме того, кнопка **Применить** находится в нижней части списка, поэтому нужно прокрутить вниз, чтобы увидеть ее.

    Обратите внимание, что запрос в текстовом поле изменяется на тот, который показан в выделенном поле на следующем снимке экрана:

    ![Поиск конкретных метрик в log Analytics](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-metrics.png "Поиск конкретных метрик")

1. Можно получить более подробную информацию о конкретной метрике. Например, можно уточнить существующие выходные данные по среднему количеству ресурсов, использованных за 10-минутный интервал, классифицировав их по имени кластера с помощью следующего запроса:

        search in (metrics_resourcemanager_queue_root_default_CL) * | summarize AggregatedValue = avg(UsedAMResourceMB_d) by ClusterName_s, bin(TimeGenerated, 10m)

1. Вместо уточнения по среднему количеству используемых ресурсов можно использовать следующий запрос, чтобы уточнить результаты на основе максимального использования ресурсов в 10-минутном интервале (а также 90-го и 95-го процентиля):

        search in (metrics_resourcemanager_queue_root_default_CL) * | summarize ["max(UsedAMResourceMB_d)"] = max(UsedAMResourceMB_d), ["pct95(UsedAMResourceMB_d)"] = percentile(UsedAMResourceMB_d, 95), ["pct90(UsedAMResourceMB_d)"] = percentile(UsedAMResourceMB_d, 90) by ClusterName_s, bin(TimeGenerated, 10m)

## <a name="search-for-specific-log-messages"></a>Поиск определенных сообщений журнала

Узнайте, как искать сообщения об ошибках за определенный интервал времени. Эти шаги являются всего лишь одним примером того, как можно получить сообщение об ошибке, которое вас интересует. Для поиска нужных ошибок можно использовать любое доступное свойство.

1. Откройте рабочую область Log Analytics, которая связана с вашим кластером HDInsight на портале Azure.
2. Выберите плитку **Поиск по журналам**.
3. Введите следующий запрос, чтобы найти все сообщения об ошибках для всех кластеров HDInsight, настроенных для использования журналов Azure Monitor, а затем выберите **выполнить**.

         search "Error"

    Должны отобразиться выходные данные, аналогичные указанным ниже.

    ![Портал Azure ошибок поиска по журналам](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-errors-output.png "Поиск всех ошибок в выходных данных")

4. В левой области в категории **Тип** найдите тип ошибки, который необходимо изучить подробнее, и нажмите кнопку **Применить**.  Обратите внимание, что результаты уточнены и показывают только ошибки выбранного типа.

5. Можно получить более подробную информацию о конкретном списке ошибок, используя параметры, доступные на панели слева. Пример:

    - чтобы просмотреть сообщения об ошибках из определенного рабочего узла, сделайте следующее:

        ![Поиск конкретных ошибок output1](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error-refined.png "Поиск конкретных ошибок output1")

    - Чтобы просмотреть ошибку, которая возникла в определенное время, сделайте следующее:

        ![Поиск конкретных ошибок Output2](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error-time.png "Поиск конкретных ошибок Output2")

6. Чтобы просмотреть определенную ошибку, сделайте следующее. Щелкните **[+]show more** ([+] больше), чтобы просмотреть фактическое сообщение об ошибке.

    ![Поиск конкретных ошибок output3](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error-arrived.png "Поиск конкретных ошибок output3")

## <a name="create-alerts-for-tracking-events"></a>Создание оповещений для отслеживания событий

Первым шагом создания оповещения является получение запроса, на основе которого активируется оповещение. Для создания оповещения можно использовать любой запрос.

1. Откройте рабочую область Log Analytics, которая связана с вашим кластером HDInsight на портале Azure.
2. Выберите плитку **Поиск по журналам**.
3. Выполните следующий запрос, на основе которого требуется создать оповещение, а затем нажмите клавишу **ЗАПУСК**.

        metrics_resourcemanager_queue_root_default_CL | where AppsFailed_d > 0

    Запрос вернет список запущенных на кластерах HDInsight приложений, в которых возник сбой.

4. Выберите **Новое правило генерации оповещений** в верхней части страницы.

    ![Введите запрос для создания alert1](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-create-alert-query.png "Введите запрос для создания alert1")

5. В окне **Создать правило** введите запрос и другие сведения, чтобы создать оповещение, а затем щелкните **Создать правило генерации оповещений**.

    ![Введите запрос для создания alert2](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-create-alert.png "Введите запрос для создания alert2")

Чтобы изменить или удалить оповещение:

1. Откройте рабочую область Log Analytics на портале Azure.
2. В меню слева выберите **Оповещение**.
3. Выберите оповещение, которое требуется изменить или удалить.
4. Можно выбрать один из следующих параметров. **Сохранить**, **Отменить**, **Отключить** и **Удалить**.

    ![Предупреждение об удалении журнала Azure Monitor HDInsight](media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-edit-alert.png)

Дополнительные сведения см. в статье [Создание и просмотр оповещений метрик, а также управление ими с помощью Azure Monitor](../azure-monitor/platform/alerts-metric.md).

## <a name="see-also"></a>См. также

* [Создание пользовательских представлений с помощью конструктора представлений в Azure Monitor](../azure-monitor/platform/view-designer.md)
* [Создание, Просмотр оповещений метрик и управление ими с помощью Azure Monitor](../azure-monitor/platform/alerts-metric.md)
