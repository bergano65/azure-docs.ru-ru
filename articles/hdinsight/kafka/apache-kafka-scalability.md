---
title: Повышение масштабируемости Apache Kafka в Azure HDInsight
description: Узнайте, как настроить управляемые диски для кластера Apache Kafka в Azure HDInsight, чтобы повысить масштабируемость.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/30/2018
ms.openlocfilehash: ab80dd86e544127fc3f40f5459ef9a587c7cd511
ms.sourcegitcommit: 7cd706612a2712e4dd11e8ca8d172e81d561e1db
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/18/2018
ms.locfileid: "53581510"
---
# <a name="configure-storage-and-scalability-for-apache-kafka-on-hdinsight"></a>Настройка объема хранилища и уровня масштабируемости для Apache Kafka в HDInsight

Узнайте, как настроить количество управляемых дисков, используемых [Apache Kafka](https://kafka.apache.org/) в HDInsight.

Kafka в HDInsight использует локальный диск для виртуальных машин в кластере HDInsight. Так как Kafka обрабатывает большое количество операций ввода-вывода, используются [управляемые диски Azure](../../virtual-machines/windows/managed-disks-overview.md), чтобы обеспечить высокую пропускную способность и предоставить дополнительное хранилище для каждого узла. Если для Kafka использовать стандартные виртуальные жесткие диски (VHD), на каждом узле будет доступен 1 ТБ памяти. Но благодаря управляемым дискам можно использовать несколько дисков и увеличить объем каждого узла кластера до 16 ТБ.

На схеме ниже сравниваются две версии Kafka в HDInsight: до использования управляемых дисков и с ними.

![Схема: Kafka в HDInsight с одним виртуальным жестким диском для каждой виртуальной машины и с несколькими управляемыми дисками для каждой виртуальной машины](./media/apache-kafka-scalability/kafka-with-managed-disks-architecture.png)

## <a name="configure-managed-disks-azure-portal"></a>Настройка управляемых дисков. Портал Azure

1. Следуйте указаниям в статье о [создании кластеров HDInsight](../hdinsight-hadoop-create-linux-clusters-portal.md), чтобы ознакомиться с основными действиями для создания кластера с помощью портала. Не завершайте создание кластера на портале.

2. В разделе __Размер кластера__ укажите число дисков в поле __Число дисков на рабочий узел__.

    > [!NOTE]  
    > Управляемый диск может быть двух типов: __Стандартный__ (HDD) или __Премиум__ (SSD). Диски категории "Премиум" используются с виртуальными машинами серий DS и GS. Для всех остальных виртуальных машин используются стандартные управляемые диски.

    ![Изображение раздела "Размер кластера" с выделенным полем "Число дисков на рабочий узел"](./media/apache-kafka-scalability/set-managed-disks-portal.png)

## <a name="configure-managed-disks-resource-manager-template"></a>Настройка управляемых дисков. Шаблон Resource Manager

Чтобы управлять количеством дисков, которое используется рабочими узлами в кластере Kafka, используйте следующий раздел шаблона:

```json
"dataDisksGroups": [
    {
        "disksPerNode": "[variables('disksPerWorkerNode')]"
    }
    ],
```

Полный шаблон, в котором показано, как настроить управляемые диски, можно найти на странице [https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-mirror-cluster-in-vnet-v2.1.json](https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-mirror-cluster-in-vnet-v2.1.json).

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о работе с Apache Kafka HDInsight см. в следующих документах:

* [Репликация разделов Apache Kafka с помощью Kafka в HDInsight и MirrorMaker](apache-kafka-mirroring.md)
* [Использование Apache Storm с Apache Kafka в HDInsight](../hdinsight-apache-storm-with-kafka.md)
* [Использование Apache Spark с Apache Kafka в HDInsight](../hdinsight-apache-spark-with-kafka.md)
* [Подключение к Apache Kafka с помощью виртуальной сети Azure](apache-kafka-connect-vpn-gateway.md)

* [Блог HDInsight об управляемых дисках в Apache Kafka](https://azure.microsoft.com/blog/announcing-public-preview-of-apache-kafka-on-hdinsight-with-azure-managed-disks/)
