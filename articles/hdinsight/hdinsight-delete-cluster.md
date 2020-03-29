---
title: Как удалить кластер HDInsight в Azure
description: Информация о различных способах удаления кластера Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.date: 11/29/2019
ms.openlocfilehash: 2912777c001a36ec913e2cfd618091ada5aa107a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74807142"
---
# <a name="delete-an-hdinsight-cluster-using-your-browser-powershell-or-the-azure-cli"></a>Удаление кластера HDInsight с помощью браузера, PowerShell или Azure CLI

Начисление оплаты начинается после создания кластера HDInsight и прекращается только после его удаления. Биллинг является прооцененным в минуту, поэтому всегда следует удалять кластер, когда он больше не используется. В этом документе вы узнаете, как удалить кластер с помощью [портала Azure,](https://portal.azure.com) [модуля Azure PowerShell Az](https://docs.microsoft.com/powershell/azure/overview)и [Azure CLI.](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)

> [!IMPORTANT]  
> При удалении кластера HDInsight не происходит удаления связанных с ним учетных записей хранения Azure или Data Lake Storage. Данные, хранящиеся в этих службах, можно повторно использовать в будущем.

## <a name="azure-portal"></a>Портал Azure

1. Войдите на [портал Azure](https://portal.azure.com).

2. Из левого меню перейдите на **все службы** > **Analytics** > **HDInsight кластеров** и выберите кластер.

3. Из представления по умолчанию выберите значок **«Удалить».** Следуйте запросу, чтобы удалить кластер.

    ![HDInsight удалить кластеркнопку](./media/hdinsight-delete-cluster/hdinsight-delete-cluster.png)

## <a name="azure-powershell"></a>Azure PowerShell

Замените `CLUSTERNAME` имя кластера HDInsight в приведенном ниже коде. Из запроса PowerShell введите следующую команду, чтобы удалить кластер:

```powershell
Remove-AzHDInsightCluster -ClusterName CLUSTERNAME
```

## <a name="azure-cli"></a>Azure CLI

Замените `CLUSTERNAME` имя кластера HDInsight `RESOURCEGROUP` и имя группы ресурсов в приведенном ниже коде.  Из запроса команды введите следующее, чтобы удалить кластер:

```azurecli
az hdinsight delete --name CLUSTERNAME --resource-group RESOURCEGROUP
```
