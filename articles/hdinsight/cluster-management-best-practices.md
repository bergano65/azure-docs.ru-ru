---
title: Рекомендации по управлению кластером — Azure HDInsight
description: Ознакомьтесь с рекомендациями по управлению кластерами HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 3010c1d597907e7d5c7f82c8b42721dc1f934f6f
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74782025"
---
# <a name="hdinsight-cluster-management-best-practices"></a>Рекомендации по управлению кластерами HDInsight

Ознакомьтесь с рекомендациями по управлению кластерами HDInsight.

## <a name="how-do-i-create-hdinsight-clusters"></a>Разделы справки создать кластеры HDInsight?

| Вариант | Документы |
|---|---|
| Фабрика данных Azure | [Создание кластеров Apache Hadoop в HDInsight по запросу с помощью Фабрики данных Azure](./hdinsight-hadoop-create-linux-clusters-adf.md) |
| Пользовательский шаблон диспетчер ресурсов | [Создание кластеров Apache Hadoop в HDInsight с помощью шаблонов диспетчер ресурсов](./hdinsight-hadoop-create-linux-clusters-arm-templates.md) |
| Шаблоны быстрого запуска | [Шаблоны быстрого запуска HDInsight](https://azure.microsoft.com/resources/templates/?term=hdinsight) |
| Примеры Azure | [Примеры Azure HDInsight](https://docs.microsoft.com/samples/browse/?products=azure-hdinsight) |
| портала Azure | [Создание кластеров под управлением Linux в HDInsight с помощью портал Azure](./spark/apache-spark-intellij-tool-plugin.md) |
| Azure CLI | [Создание кластеров HDInsight с помощью Azure CLI](./hdinsight-hadoop-create-linux-clusters-azure-cli.md) |
| Azure PowerShell | [Создание кластеров под управлением Linux в HDInsight с помощью Azure PowerShell](./hdinsight-hadoop-create-linux-clusters-azure-powershell.md) |
| cURL | [Создание кластеров Apache Hadoop с помощью REST API Azure](./hdinsight-hadoop-create-linux-clusters-curl-rest.md) |
| Пакеты SDK (.NET, Python, Java) | [.NET](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight?view=azure-dotnet), [Python](https://docs.microsoft.com/python/api/overview/azure/hdinsight?view=azure-python), [Java](https://docs.microsoft.com/java/api/overview/azure/hdinsight?view=azure-java-stable), [Go](https://docs.microsoft.com/azure/hdinsight/hdinsight-go-sdk-overview) |

> [!Note]
> Если вы создаете кластер и повторно используете имя кластера из ранее созданного кластера, перед созданием кластера дождитесь завершения предыдущего удаления кластера.

## <a name="how-do-i-customize-hdinsight-clusters"></a>Разделы справки настроить кластеры HDInsight?

| Вариант | Документы |
|---|---|
| Действия сценария | [Настройка кластеров Azure HDInsight с помощью действий сценария](./hdinsight-hadoop-customize-cluster-linux.md) |
| Загрузке | [Настройка кластеров HDInsight с помощью начальной загрузки](./hdinsight-hadoop-customize-cluster-bootstrap.md) |
| Внешние метахранилища | [Использование внешних хранилищ метаданных в Azure HDInsight](./hdinsight-use-external-metadata-stores.md) |
| Пользовательская база данных Ambari DB | [Настройка кластеров HDInsight с помощью настраиваемой базы данных Ambari DB](./hdinsight-custom-ambari-db.md) |

## <a name="what-are-some-errors-i-might-face-when-creating-clusters"></a>Какие ошибки я могу столкнуться при создании кластеров?

| Ошибка | Дополнительные сведения |
|---|---|
| Нет квоты | Существуют квоты на количество квот, которые можно создать для подписки в каждом регионе. Дополнительные сведения см. в разделе [планирование емкости: квоты](./hdinsight-capacity-planning.md). |
| Больше нет доступных IP-адресов | Каждая виртуальная сеть имеет ограниченное количество IP-адресов. При создании кластера HDInsight каждый узел (включая узлы Zookeeper и шлюза) использует некоторые из этих выделенных IP-адресов. Если все IP-адреса используются, возникнет эта ошибка.  |
| Правила группы безопасности сети (NSG) не разрешают взаимодействие с поставщиками ресурсов HDInsight | Если вы используете группы безопасности сети или определяемые пользователем маршруты (определяемые пользователем маршруты) для управления входящим трафиком к кластеру HDInsight, необходимо убедиться, что кластер может взаимодействовать с критически важными службами работоспособности и управления Azure. Дополнительные сведения см. в статье [теги службы группы безопасности сети (NSG) для Azure HDInsight](./hdinsight-service-tags.md) . |
| Повторное использование имени кластера | При использовании имени кластера, которое использовалось ранее, необходимо подождать X минут перед повторной созданием кластера. В противном случае вы увидите сообщение о том, что ресурс уже существует. |

## <a name="how-do-i-manage-running-hdinsight-clusters"></a>Разделы справки управлять работающими кластерами HDInsight?

| Вариант | Документы |
|---|---|
| Автоматическое масштабирование | [Автоматическое масштабирование кластеров Azure HDInsight](./hdinsight-autoscale-clusters.md) |
| Ручное масштабирование | [Масштабирование кластеров Azure HDInsight](./hdinsight-scaling-best-practices.md) |
| Мониторинг с помощью Ambari| [Мониторинг производительности кластера в Azure HDInsight](./hdinsight-key-scenarios-to-monitor.md) |
| Мониторинг с помощью журналов Azure Monitor | [Use Azure Monitor logs to monitor HDInsight clusters](./hdinsight-hadoop-oms-log-analytics-tutorial.md) (Мониторинг кластеров HDInsight с помощью журналов Azure Monitor) |

## <a name="how-do-i-check-on-deleted-hdinsight-clusters"></a>Разделы справки проверить удаленные кластеры HDInsight?

### <a name="azure-monitor-logs"></a>Журналы Azure Monitor

Для мониторинга удаленных кластеров можно использовать следующий запрос с журналами Azure Monitor.

```loganalytics
AzureActivity
| where ResourceProvider == "Microsoft.HDInsight" and (OperationName == "Create or Update Cluster" or OperationName == "Delete Cluster") and ActivityStatus == "Succeeded"
```

## <a name="next-steps"></a>Дальнейшие действия

* [Capacity planning for HDInsight clusters](./hdinsight-capacity-planning.md) (Планирование загрузки кластеров HDInsight)
* [Каковы рекомендуемые конфигурации узла по умолчанию для Azure HDInsight?](./hdinsight-supported-node-configuration.md)