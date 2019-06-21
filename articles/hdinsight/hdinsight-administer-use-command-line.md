---
title: Управление кластерами Azure HDInsight, с помощью интерфейса командной строки Azure
description: Узнайте, как использовать Azure CLI для управления кластерами Azure HDInsight. Типы кластеров включают Apache Hadoop, Spark, HBase, Storm, Kafka, Interactive Query и службами машинного Обучения.
ms.reviewer: jasonh
author: tylerfox
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 05/13/2019
ms.author: tyfox
ms.openlocfilehash: 5ae97b17d06fa0a9934a58ac662ef12116cce4f6
ms.sourcegitcommit: e5dcf12763af358f24e73b9f89ff4088ac63c6cb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/14/2019
ms.locfileid: "67137403"
---
# <a name="manage-azure-hdinsight-clusters-using-azure-cli"></a>Управление кластерами Azure HDInsight, с помощью интерфейса командной строки Azure

[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Сведения об использовании [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) для управления кластерами Azure HDInsight. Azure CLI — это кроссплатформенный интерфейс командной строки от Майкрософт для управления ресурсами Azure.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

## <a name="prerequisites"></a>Технические условия

* Azure CLI. Если вы еще не установили Azure CLI, обратитесь к статье [Установка Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli).

* Кластер Apache Hadoop в HDInsight. Ознакомьтесь со статьей [Краткое руководство. Использование Apache Hadoop и Apache Hive в Azure HDInsight с шаблоном Resource Manager](hadoop/apache-hadoop-linux-tutorial-get-started.md).

## <a name="connect-to-azure"></a>Подключение к Azure

Войдите в свою подписку Azure. Если вы планируете использовать Azure Cloud Shell, просто выберите **Попробовать** в правом верхнем углу блока кода. В противном случае введите следующую команду:

```azurecli-interactive
az login

# If you have multiple subscriptions, set the one to use
# az account set --subscription "SUBSCRIPTIONID"
```

## <a name="list-clusters"></a>Получение списка кластеров

Используйте [список hdinsight az](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-list) на получение списка кластеров. Измените приведенные ниже команды, заменив `RESOURCE_GROUP_NAME` с именем группы ресурсов, затем введите команды:

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

Используйте [az hdinsight show](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-show) для отображения сведений для указанного кластера. Измените указанную ниже команду, заменив `RESOURCE_GROUP_NAME`, и `CLUSTER_NAME` актуальной информацией, введите команду:

```azurecli-interactive
az hdinsight show --resource-group RESOURCE_GROUP_NAME --name CLUSTER_NAME
```

## <a name="delete-clusters"></a>Удаление кластеров

Используйте [удалении az hdinsight](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-delete) для удаления указанного кластера. Измените указанную ниже команду, заменив `RESOURCE_GROUP_NAME`, и `CLUSTER_NAME` актуальной информацией, введите команду:

```azurecli-interactive
az hdinsight delete --resource-group RESOURCE_GROUP_NAME --name CLUSTER_NAME
```

Можно также удалить кластер, удалив группу ресурсов, которая содержит этот кластер. Обратите внимание, что это приведет к удалению всех ресурсов в группе, включая учетную запись хранения по умолчанию.

```azurecli-interactive
az group delete --name RESOURCE_GROUP_NAME
```

## <a name="scale-clusters"></a>Масштабирование кластеров

Используйте [az hdinsight изменение размера](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-resize) изменить размер указанного кластера HDInsight до заданного размера. Измените указанную ниже команду, заменив `RESOURCE_GROUP_NAME`, и `CLUSTER_NAME` соответствующей информацией. Замените `TARGET_INSTANCE_COUNT` с требуемое число рабочих узлов для кластера. Дополнительные сведения о масштабировании кластеров см. в разделе [кластеры HDInsight масштабирования](./hdinsight-scaling-best-practices.md). Введите команду:

```azurecli-interactive
az hdinsight resize --resource-group RESOURCE_GROUP_NAME --name CLUSTER_NAME --target-instance-count TARGET_INSTANCE_COUNT
```

## <a name="next-steps"></a>Дальнейшие действия

В этой статье вы узнали, как выполнять различные административные задачи в кластере HDInsight. Для получения дополнительных сведений ознакомьтесь со следующими статьями:

* [Управление кластерами Apache Hadoop в HDInsight с помощью портала Azure](hdinsight-administer-use-portal-linux.md)
* [Администрирование HDInsight с помощью Azure PowerShell](hdinsight-administer-use-powershell.md)
* [Приступая к работе с Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Приступая к работе с Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest)
