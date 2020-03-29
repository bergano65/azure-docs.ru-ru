---
title: Журналы мониторинга запросов Azure Monitor для мониторинга кластеров Azure HDInsight
description: Узнайте, как запускать запросы в журналах Azure Monitor для мониторинга заданий, работающих в кластере HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/02/2019
ms.openlocfilehash: 65e85548420116bdfcab87fe9f81a20e66226beb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74803843"
---
# <a name="query-azure-monitor-logs-to-monitor-hdinsight-clusters"></a>Журналы мониторинга запросов Azure Monitor для мониторинга кластеров HDInsight

Узнайте несколько основных сценариев использования журналов Azure Monitor для мониторинга кластеров Azure HDInsight:

* [Анализ метрик кластера HDInsight](#analyze-hdinsight-cluster-metrics)
* [Создание оповещений о событиях](#create-alerts-for-tracking-events).

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Предварительные требования

Необходимо настроить кластер HDInsight для использования журналов Azure Monitor и добавить в рабочее пространство решения мониторинга журналов мониторинга кластеров HdInsight Azure Monitor. Для получения инструкций смотрите [журналы Azure Monitor с кластерами HDInsight.](hdinsight-hadoop-oms-log-analytics-tutorial.md)

## <a name="analyze-hdinsight-cluster-metrics"></a>Анализ метрик кластера HDInsight

Узнайте, как выполнить поиск определенных метрик для кластера HDInsight.

1. Откройте рабочую область Log Analytics, которая связана с вашим кластером HDInsight на портале Azure.
1. Под **общим**, выберите **журналы**.
1. Введите следующий запрос в поле поиска для поиска всех метрик для всех доступных метрик для всех кластеров HDInsight, настроенных для использования журналов Azure Monitor, а затем выберите **Run.** Просмотрите результаты операции.

    ```kusto
    search *
    ```

    ![Аналитики Apache Ambari ищут все метрики](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-metrics.png "Поиск по всем метрикам")

1. Из левого меню выберите вкладку **Фильтр.**

1. Под **типом**, выберите **Сердцебиение**. Затем выберите **Применить & выполнить**.

    ![поиск аналитики журналов](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-metrics.png "Поиск конкретных метрик")

1. Обратите внимание, что запрос в текстовом поле изменяется на:

    ```kusto
    search *
    | where Type == "Heartbeat"
    ```

1. Вы можете копать глубже, используя варианты, доступные в левом меню. Пример:

    - Чтобы увидеть журналы из определенного узла:

        ![Поиск конкретных ошибок output1](./media/hdinsight-hadoop-oms-log-analytics-use-queries/log-analytics-specific-node.png "Поиск конкретных ошибок output1")

    - Чтобы просмотреть журналы в определенное время:

        ![Поиск конкретных ошибок output2](./media/hdinsight-hadoop-oms-log-analytics-use-queries/log-analytics-specific-time.png "Поиск конкретных ошибок output2")

1. Выберите **Применить & выполнить** и просмотреть результаты. Также обратите внимание, что запрос был обновлен до:

    ```kusto
    search *
    | where Type == "Heartbeat"
    | where (Computer == "zk2-myhado") and (TimeGenerated == "2019-12-02T23:15:02.69Z" or TimeGenerated == "2019-12-02T23:15:08.07Z" or TimeGenerated == "2019-12-02T21:09:34.787Z")
    ```

### <a name="additional-sample-queries"></a>Дополнительные выборочные запросы

Запрос выборки, основанный на среднем уровне ресурсов, используемых в 10-минутном интервале, классифицируется по имени кластера:

```kusto
search in (metrics_resourcemanager_queue_root_default_CL) * 
| summarize AggregatedValue = avg(UsedAMResourceMB_d) by ClusterName_s, bin(TimeGenerated, 10m)
```

Вместо уточнения по среднему количеству используемых ресурсов можно использовать следующий запрос, чтобы уточнить результаты на основе максимального использования ресурсов в 10-минутном интервале (а также 90-го и 95-го процентиля):

```kusto
search in (metrics_resourcemanager_queue_root_default_CL) * 
| summarize ["max(UsedAMResourceMB_d)"] = max(UsedAMResourceMB_d), ["pct95(UsedAMResourceMB_d)"] = percentile(UsedAMResourceMB_d, 95), ["pct90(UsedAMResourceMB_d)"] = percentile(UsedAMResourceMB_d, 90) by ClusterName_s, bin(TimeGenerated, 10m)
```

## <a name="create-alerts-for-tracking-events"></a>Создание оповещений для отслеживания событий

Первым шагом создания оповещения является получение запроса, на основе которого активируется оповещение. Для создания оповещения можно использовать любой запрос.

1. Откройте рабочую область Log Analytics, которая связана с вашим кластером HDInsight на портале Azure.
1. Под **общим**, выберите **журналы**.
1. Выполнить следующий запрос, на котором требуется создать оповещение, а затем выберите **Выполнить.**

    ```kusto
    metrics_resourcemanager_queue_root_default_CL | where AppsFailed_d > 0
    ```

    Запрос вернет список запущенных на кластерах HDInsight приложений, в которых возник сбой.

1. Выберите **новое правило оповещения** в верхней части страницы.

    ![Введите запрос для создания оповещения1](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-create-alert-query.png "Введите запрос для создания оповещения1")

1. В окне **Создать правило** введите запрос и другие сведения, чтобы создать оповещение, а затем щелкните **Создать правило генерации оповещений**.

    ![Введите запрос для создания оповещения2](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-create-alert.png "Введите запрос для создания оповещения2")

### <a name="edit-or-delete-an-existing-alert"></a>Отсечение или удаление существующего оповещения

1. Откройте рабочую область Log Analytics на портале Azure.

1. Из левого меню, под **мониторингом,** выберите **оповещения.**

1. Вверху выберите **правила оповещения Управления.**

1. Выберите оповещение, которое требуется изменить или удалить.

1. Доступны следующие варианты: **Сохранить**, **Отменить**, **Отключить** и **Удалить**.

    ![HDInsight Azure Monitor журналы оповещения удалить](media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-edit-alert.png)

Дополнительные сведения см. в статье [Создание и просмотр оповещений метрик, а также управление ими с помощью Azure Monitor](../azure-monitor/platform/alerts-metric.md).

## <a name="see-also"></a>См. также

* [Начало работы с запросами к журналам Azure Monitor](../azure-monitor/log-query/get-started-queries.md)
* [Создавайте пользовательские представления с помощью «Дизайнера представлений» в Azure Monitor](../azure-monitor/platform/view-designer.md)
