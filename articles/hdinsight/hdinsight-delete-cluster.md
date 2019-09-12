---
title: Как удалить кластер HDInsight в Azure
description: Сведения о различных способах удаления кластера Azure HDInsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: hrasheed
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: 08bfdcab0b7fbb8e533cb8d2d6a74d315ad4074c
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/11/2019
ms.locfileid: "70885222"
---
# <a name="delete-an-hdinsight-cluster-using-your-browser-powershell-or-the-azure-cli"></a>Удаление кластера HDInsight с помощью браузера, PowerShell или Azure CLI

Начисление оплаты начинается после создания кластера HDInsight и прекращается только после его удаления. Кластеры оплачиваются поминутно, поэтому всегда следует удалять кластер, когда он больше не нужен. В этом документе вы узнаете, как удалить кластер с помощью [портал Azure](https://portal.azure.com), [Azure PowerShell AZ](https://docs.microsoft.com/powershell/azure/overview)и [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).

> [!IMPORTANT]  
> При удалении кластера HDInsight не происходит удаления связанных с ним учетных записей хранения Azure или Data Lake Storage. Данные, хранящиеся в этих службах, можно повторно использовать в будущем.

## <a name="azure-portal"></a>портала Azure

1. Войдите на [портале Azure](https://portal.azure.com).

2. В меню слева перейдите к пункту **все службы** > **аналитика** > **кластеров HDInsight** и выберите свой кластер.

3. В представлении по умолчанию выберите значок **Удалить** . Выполните запрос, чтобы удалить кластер.
   
    ![значок удаления](./media/hdinsight-delete-cluster/hdinsight-delete-cluster.png)

## <a name="azure-powershell-az-module"></a>Azure PowerShell AZ Module

Замените `CLUSTERNAME` именем кластера HDInsight в приведенном ниже коде. В командной строке PowerShell введите следующую команду, чтобы удалить кластер:

```powershell
Remove-AzHDInsightCluster -ClusterName CLUSTERNAME
```

## <a name="azure-cli"></a>Azure CLI

Замените `CLUSTERNAME` именем кластера HDInsight и `RESOURCEGROUP` именем группы ресурсов в приведенном ниже коде.  В командной строке введите следующую команду, чтобы удалить кластер:

```azurecli
az hdinsight delete --name CLUSTERNAME --resource-group RESOURCEGROUP
```
