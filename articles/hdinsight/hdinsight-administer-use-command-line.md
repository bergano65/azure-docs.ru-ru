---
title: Управление кластерами Azure HDInsight с помощью Azure CLI
description: Узнайте, как использовать Azure CLI для управления кластерами Azure HDInsight. К типам кластеров относятся Apache Hadoop, Spark, HBase, Kafka, Интерактивные запросы и службы ML.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 12/06/2019
ms.openlocfilehash: 81bc632f1061f0ee73d2295cafa5f7a8472d20ee
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/09/2019
ms.locfileid: "74951808"
---
# <a name="manage-azure-hdinsight-clusters-using-azure-cli"></a>Управление кластерами Azure HDInsight с помощью Azure CLI

[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Узнайте, как использовать [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) для управления кластерами Azure HDInsight. Azure CLI — это кроссплатформенный интерфейс командной строки от Майкрософт для управления ресурсами Azure.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

## <a name="prerequisites"></a>Технические условия

* Azure CLI. Если вы еще не установили Azure CLI, обратитесь к статье [Установка Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli).

* Кластер Apache Hadoop в HDInsight. Ознакомьтесь со статьей [Краткое руководство. Использование Apache Hadoop и Apache Hive в Azure HDInsight с шаблоном Resource Manager](hadoop/apache-hadoop-linux-tutorial-get-started.md).

## <a name="connect-to-azure"></a>Подключение к Azure

Войдите в свою подписку Azure. Если вы планируете использовать Azure Cloud Shell, выберите команду **попробовать** в правом верхнем углу блока кода. В противном случае введите следующую команду:

```azurecli-interactive
az login

# If you have multiple subscriptions, set the one to use
# az account set --subscription "SUBSCRIPTIONID"
```

## <a name="list-clusters"></a>Получение списка кластеров

Список кластеров можно получить с помощью команды [AZ hdinsight List](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-list) . Измените приведенные ниже команды, заменив `RESOURCE_GROUP_NAME` именем группы ресурсов, а затем введите команды:

```azurecli-interactive
# List all clusters in the current subscription
az hdinsight list

# List only cluster name and its resource group
az hdinsight list --query "[].{Cluster:name, ResourceGroup:resourceGroup}" --output table

# List all cluster for your resource group
az hdinsight list --resource-group RESOURCE_GROUP_NAME

# List all cluster names for your resource group
az hdinsight list --resource-group RESOURCE_GROUP_NAME --query "[].{clusterName:name}" --output table
```

## <a name="show-cluster"></a>Отображение кластеров

Чтобы отобразить сведения об указанном кластере, используйте команду [AZ hdinsight демонстрация](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-show) . Измените приведенную ниже команду, заменив `RESOURCE_GROUP_NAME`и `CLUSTER_NAME` соответствующими сведениями, а затем введите команду:

```azurecli-interactive
az hdinsight show --resource-group RESOURCE_GROUP_NAME --name CLUSTER_NAME
```

## <a name="delete-clusters"></a>Удаление кластеров

Чтобы удалить указанный кластер, используйте команду [AZ hdinsight Delete](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-delete) . Измените приведенную ниже команду, заменив `RESOURCE_GROUP_NAME`и `CLUSTER_NAME` соответствующими сведениями, а затем введите команду:

```azurecli-interactive
az hdinsight delete --resource-group RESOURCE_GROUP_NAME --name CLUSTER_NAME
```

Можно также удалить кластер, удалив группу ресурсов, которая содержит этот кластер. Обратите внимание, что это приведет к удалению всех ресурсов в группе, включая учетную запись хранения по умолчанию.

```azurecli-interactive
az group delete --name RESOURCE_GROUP_NAME
```

## <a name="scale-clusters"></a>Масштабирование кластеров

Чтобы изменить размер указанного кластера HDInsight до указанного размера, используйте команду [AZ hdinsight изменить](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-resize) размер. Измените приведенную ниже команду, заменив `RESOURCE_GROUP_NAME`и `CLUSTER_NAME` соответствующими данными. Замените `TARGET_INSTANCE_COUNT` требуемым количеством рабочих узлов для кластера. Дополнительные сведения о масштабировании кластеров см. в статье [масштабирование кластеров HDInsight](./hdinsight-scaling-best-practices.md). Введите команду:

```azurecli-interactive
az hdinsight resize --resource-group RESOURCE_GROUP_NAME --name CLUSTER_NAME --target-instance-count TARGET_INSTANCE_COUNT
```

## <a name="next-steps"></a>Дальнейшие действия

В этой статье вы узнали, как выполнять различные задачи администрирования кластера HDInsight. Для получения дополнительных сведений ознакомьтесь со следующими статьями:

* [Управление кластерами Apache Hadoop в HDInsight с помощью портала Azure](hdinsight-administer-use-portal-linux.md)
* [Администрирование HDInsight с помощью Azure PowerShell](hdinsight-administer-use-powershell.md)
* [Приступая к работе с Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Приступая к работе с Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest)
