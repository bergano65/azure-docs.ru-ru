---
title: Перезагрузка виртуальных машин для кластера Azure HDInsight
description: Узнайте, как перезагружать неотвечающие виртуальные машины для кластера HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: how-to
ms.date: 06/22/2020
ms.openlocfilehash: c0f0bd9eb423b3de6a602647dff93fd9fce6e13e
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/08/2020
ms.locfileid: "86077020"
---
# <a name="reboot-vms-for-hdinsight-cluster"></a>Перезагрузка виртуальных машин для кластера HDInsight

Кластеры HDInsight содержат группы виртуальных машин в качестве узлов кластера. Для длительных кластеров эти узлы могут перестать отвечать по различным причинам. В этой статье описывается, как перезагружать неотвечающие виртуальные машины в кластере HDInsight.

## <a name="when-to-reboot"></a>Время перезагрузки

> [!WARNING]  
> Перезагрузка виртуальных машин в кластере приводит к простою узла и перезапуску служб на узле. 

При перезагрузке узла кластер может стать неработоспособным, а задания могут замедлить работу или завершиться сбоем. Если вы пытаетесь перезагрузить активный головной узел, все выполняющиеся задания будут уничтожены, и вы не сможете отправлять задания в кластер, пока службы не будут запущены и еще раз не запустится. Рекомендуется перезагружать виртуальные машины только при необходимости. Ниже приведены некоторые рекомендации, которые необходимо учитывать при перезагрузке виртуальных машин.

- Вы не можете получить доступ к узлу по протоколу SSH, но он отвечает на запросы проверки связи.
- Рабочий узел не работает без пульса в пользовательском интерфейсе Ambari.
- Диск TEMP заполнен на узле.
- В таблице процесса на виртуальной машине содержится много записей, где процесс был завершен, но он указан с «завершенным состоянием».

> [!WARNING]  
> При перезагрузке виртуальных машин для **HBase** и **Kafka** клустес следует соблюдать осторожность, так как это может привести к потере данных.

## <a name="use-powershell-to-reboot-vms"></a>Перезагрузка виртуальных машин с помощью PowerShell

Для использования операции перезагрузки узла необходимо выполнить два действия: список узлов и перезагрузка узлов.

1. Список узлов. Список узлов кластера можно получить с помощью [Get-аздинсигхсост](https://docs.microsoft.com/powershell/module/az.hdinsight/get-azhdinsighthost). 

  ```
  Get-AzHDInsightHost -ClusterName myclustername
  ```

2. Перезапустите узлы. После получения имен узлов, которые требуется перезагрузить, перезапустите узлы с помощью [restart-аздинсигхсост](https://docs.microsoft.com/powershell/module/az.hdinsight/restart-azhdinsighthost).

  ```
  Restart-AzHDInsightHost -ClusterName myclustername -Name wn0-myclus, wn1-myclus
  ```

## <a name="use-rest-api-to-reboot-vms"></a>Использование REST API для перезагрузки виртуальных машин

Для отправки запросов в HDInsight можно использовать функцию **попробовать** в документации по API. Для использования операции перезагрузки узла необходимо выполнить два действия: список узлов и перезагрузка узлов.

1. Список узлов. Список узлов кластера можно получить из REST API или в Ambari. Дополнительные сведения можно найти в [списке HDInsight hosts REST API Operation](https://docs.microsoft.com/rest/api/hdinsight/virtualmachines/listhosts).

    ```
    POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.HDInsight/clusters/{clusterName}/listHosts?api-version=2018-06-01-preview
    ```

2. Перезапустите узлы. После получения имен узлов, которые требуется перезапустить, используйте перезагрузку узлов REST API для перезагрузки узлов. Имя узла соответствует шаблону **"NodeType (WN/HN/ZK/GW)" + "x" + "первые 6 символов имени кластера"**. Дополнительные сведения можно найти в [HDInsight Restart hosts REST API Operation](https://docs.microsoft.com/rest/api/hdinsight/virtualmachines/restarthosts).

    ```
    POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.HDInsight/clusters/{clusterName}/restartHosts?api-version=2018-06-01-preview
    ```

Фактические имена узлов, которые требуется перезагружать, задаются в массиве JSON в тексте запроса.

```json
[
  "wn0-abcdef",
  "zk1-abcdef"
]
```

## <a name="next-steps"></a>Следующие шаги

* [Restart-Аздинсигхсост](https://docs.microsoft.com/powershell/module/az.hdinsight/restart-azhdinsighthost)
* [Виртуальные машины HDInsight REST API](https://docs.microsoft.com/rest/api/hdinsight/virtualmachines)
* [REST API HDInsight](https://docs.microsoft.com/rest/api/hdinsight/)
