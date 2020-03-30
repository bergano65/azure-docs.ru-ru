---
title: Повышение масштабируемости Apache Kafka в Azure HDInsight
description: Узнайте, как настроить управляемые диски для кластера Apache Kafka в Azure HDInsight, чтобы повысить масштабируемость.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/09/2019
ms.openlocfilehash: 56c25b7c77809a5cb7f4e539cff8e1815cd9976f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77031717"
---
# <a name="configure-storage-and-scalability-for-apache-kafka-on-hdinsight"></a>Настройка объема хранилища и уровня масштабируемости для Apache Kafka в HDInsight

Узнайте, как настроить количество управляемых дисков, используемых [Apache Kafka](https://kafka.apache.org/) на HDInsight.

Kafka в HDInsight использует локальный диск для виртуальных машин в кластере HDInsight. Так как Kafka обрабатывает большое количество операций ввода-вывода, используются [управляемые диски Azure](../../virtual-machines/windows/managed-disks-overview.md), чтобы обеспечить высокую пропускную способность и предоставить дополнительное хранилище для каждого узла. Если для Kafka использовать стандартные виртуальные жесткие диски (VHD), на каждом узле будет доступен 1 ТБ памяти. Но благодаря управляемым дискам можно использовать несколько дисков и увеличить объем каждого узла кластера до 16 ТБ.

На схеме ниже сравниваются две версии Kafka в HDInsight: до использования управляемых дисков и с ними.

![kafka с архитектурой управляемых дисков](./media/apache-kafka-scalability/kafka-with-managed-disks-architecture.png)

## <a name="configure-managed-disks-azure-portal"></a>Настройка управляемых дисков на портале Azure

1. Следуйте указаниям в статье о [создании кластеров HDInsight](../hdinsight-hadoop-create-linux-clusters-portal.md), чтобы ознакомиться с основными действиями для создания кластера с помощью портала. Не завершайте процесс создания портала.

2. Из раздела **«Конфигурация & цен»** используйте поле __«Число узлов»__ для настройки количества дисков.

    > [!NOTE]  
    > Управляемый диск может быть двух типов: __Стандартный__ (HDD) или __Премиум__ (SSD). Диски категории "Премиум" используются с виртуальными машинами серий DS и GS. Для всех остальных виртуальных машин используются стандартные управляемые диски.

    ![раздел размера кластера с дисками на одном узлах рабочего](./media/apache-kafka-scalability/azure-portal-cluster-configuration-pricing-kafka-disks.png)

## <a name="configure-managed-disks-resource-manager-template"></a>Настройка управляемых дисков с использованием шаблона Resource Manager

Чтобы управлять количеством дисков, которое используется рабочими узлами в кластере Kafka, используйте следующий раздел шаблона:

```json
"dataDisksGroups": [
    {
        "disksPerNode": "[variables('disksPerWorkerNode')]"
    }
    ],
```

Вы можете найти полный шаблон, который демонстрирует, [https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-mirror-cluster-in-vnet-v2.1.json](https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-mirror-cluster-in-vnet-v2.1.json)как настроить управляемые диски на .

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о работе с Apache Kafka HDInsight см. в следующих документах:

* [Репликация разделов Apache Kafka с помощью Kafka в HDInsight и MirrorMaker](apache-kafka-mirroring.md)
* [Использование Apache Storm с Apache Kafka в HDInsight](../hdinsight-apache-storm-with-kafka.md)
* [Используйте Apache Spark с Apache Kafka на HDInsight](../hdinsight-apache-spark-with-kafka.md)
* [Подключение к Apache Kafka с помощью виртуальной сети Azure](apache-kafka-connect-vpn-gateway.md)

* [Блог HDInsight об управляемых дисках в Apache Kafka](https://azure.microsoft.com/blog/announcing-public-preview-of-apache-kafka-on-hdinsight-with-azure-managed-disks/)
