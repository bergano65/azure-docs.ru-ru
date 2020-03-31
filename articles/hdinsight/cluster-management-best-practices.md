---
title: Лучшие практики управления кластерами - Azure HDInsight
description: Изучите лучшие практики управления кластерами HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 3010c1d597907e7d5c7f82c8b42721dc1f934f6f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74782025"
---
# <a name="hdinsight-cluster-management-best-practices"></a>Лучшие практики управления кластерами HDInsight

Изучите лучшие практики управления кластерами HDInsight.

## <a name="how-do-i-create-hdinsight-clusters"></a>Как создать кластеры HDInsight?

| Параметр | Документы |
|---|---|
| Фабрика данных Azure | [Создание кластеров Apache Hadoop в HDInsight по запросу с помощью Фабрики данных Azure](./hdinsight-hadoop-create-linux-clusters-adf.md) |
| Шаблон пользовательского менеджера ресурсов | [Создание кластеров Apache Hadoop в HDInsight с помощью шаблонов Resource Manager](./hdinsight-hadoop-create-linux-clusters-arm-templates.md) |
| Шаблоны быстрого запуска | [Шаблоны HDInsight Квикстарт](https://azure.microsoft.com/resources/templates/?term=hdinsight) |
| Примеры Azure | [Образцы HDInsight Azure](https://docs.microsoft.com/samples/browse/?products=azure-hdinsight) |
| Портал Azure | [Создание кластеров под управлением Linux в HDInsight с помощью портала Azure](./spark/apache-spark-intellij-tool-plugin.md) |
| Azure CLI | [Создание кластеров HDInsight с помощью интерфейса командной строки Azure](./hdinsight-hadoop-create-linux-clusters-azure-cli.md) |
| Azure PowerShell | [Создание кластеров под управлением Linux в HDInsight с помощью Azure PowerShell](./hdinsight-hadoop-create-linux-clusters-azure-powershell.md) |
| cURL | [Создание кластеров Apache Hadoop с помощью REST API Azure](./hdinsight-hadoop-create-linux-clusters-curl-rest.md) |
| SDKs (.NET, Python, Java) | [.NET](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight?view=azure-dotnet), [Python](https://docs.microsoft.com/python/api/overview/azure/hdinsight?view=azure-python), [Java](https://docs.microsoft.com/java/api/overview/azure/hdinsight?view=azure-java-stable), [Go](https://docs.microsoft.com/azure/hdinsight/hdinsight-go-sdk-overview) |

> [!Note]
> Если вы создаете кластер и повторно используете имя кластера из ранее созданного кластера, подождите, пока предыдущее удаление кластера будет завершено, прежде чем создать кластер.

## <a name="how-do-i-customize-hdinsight-clusters"></a>Как настроить кластеры HDInsight?

| Параметр | Документы |
|---|---|
| Действия сценария | [Настройка кластеров Azure HDInsight с помощью действий скриптов](./hdinsight-hadoop-customize-cluster-linux.md) |
| Начальная загрузка | [Настройка кластеров HDInsight с помощью начальной загрузки](./hdinsight-hadoop-customize-cluster-bootstrap.md) |
| Внешние метамагазины | [Использование внешних хранилищ метаданных в Azure HDInsight](./hdinsight-use-external-metadata-stores.md) |
| Пользовательская база данных Ambari DB | [Настройка кластеров HDInsight с помощью пользовательского Ambari DB](./hdinsight-custom-ambari-db.md) |

## <a name="what-are-some-errors-i-might-face-when-creating-clusters"></a>С какими ошибками я могу столкнуться при создании кластеров?

| Error | Дополнительные сведения |
|---|---|
| Без квот | Существуют квоты на количество квот, которые можно создать по подписке в каждом регионе. Для получения дополнительной информации [см.](./hdinsight-capacity-planning.md) |
| Больше нет IP-адресов | Каждый VNet имеет ограниченное количество IP-адресов. При создании кластера HDInsight каждый узлы (включая зоозащитник и узлы шлюза) использует некоторые из этих выделенных IP-адресов. Когда все IP-адреса используются, вы столкнетесь с этой ошибкой.  |
| Правила группы сетевой безопасности (NSG) не позволяют общаться с поставщиками ресурсов HDInsight | Если для управления входящим трафиком в кластер HDInsight используются НСГ или маршруты с определенными пользователями (UDR), необходимо убедиться, что кластер может общаться с критически важными службами работоспособности и управления Azure. Для получения дополнительной информации смотрите [теги службы группы сетевой безопасности (NSG) для Azure HDInsight](./hdinsight-service-tags.md) |
| Повторное использование названия кластера | При использовании имени кластера, которое использовалось ранее, необходимо подождать X-число минут, прежде чем воссоздать кластер. В противном случае вы увидите сообщение о том, что ресурс уже существует. |

## <a name="how-do-i-manage-running-hdinsight-clusters"></a>Как управлять запущенными кластерами HDInsight?

| Параметр | Документы |
|---|---|
| Автомасштабирование | [Автоматическое масштабирование кластеров Azure HDInsight](./hdinsight-autoscale-clusters.md) |
| Масштабирование вручную | [Масштабируйте кластеры Azure HDInsight](./hdinsight-scaling-best-practices.md) |
| Мониторинг с Амбариа| [Мониторинг производительности кластера в Azure HDInsight](./hdinsight-key-scenarios-to-monitor.md) |
| Мониторинг с помощью журналов Azure Monitor | [Use Azure Monitor logs to monitor HDInsight clusters](./hdinsight-hadoop-oms-log-analytics-tutorial.md) (Мониторинг кластеров HDInsight с помощью журналов Azure Monitor) |

## <a name="how-do-i-check-on-deleted-hdinsight-clusters"></a>Как проверить удаленные кластеры HDInsight?

### <a name="azure-monitor-logs"></a>Журналы Azure Monitor

Для мониторинга удаленных кластеров можно использовать следующий запрос с журналами Azure Monitor.

```loganalytics
AzureActivity
| where ResourceProvider == "Microsoft.HDInsight" and (OperationName == "Create or Update Cluster" or OperationName == "Delete Cluster") and ActivityStatus == "Succeeded"
```

## <a name="next-steps"></a>Дальнейшие действия

* [Capacity planning for HDInsight clusters](./hdinsight-capacity-planning.md) (Планирование загрузки кластеров HDInsight)
* [Что такое конфигурация узлов по умолчанию и рекомендуемых узлов для Azure HDInsight?](./hdinsight-supported-node-configuration.md)