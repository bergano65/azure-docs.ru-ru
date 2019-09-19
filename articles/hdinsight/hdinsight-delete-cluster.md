---
title: Как удалить кластер HDInsight в Azure
description: Сведения о различных способах удаления кластера Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/17/2019
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: a28d59cb35004fac6b069f2aa41042b4d46e443f
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/18/2019
ms.locfileid: "71091538"
---
# <a name="delete-an-hdinsight-cluster-using-your-browser-powershell-or-the-azure-cli"></a>Удаление кластера HDInsight с помощью браузера, PowerShell или Azure CLI

Начисление оплаты начинается после создания кластера HDInsight и прекращается только после его удаления. Кластеры оплачиваются поминутно, поэтому всегда следует удалять кластер, когда он больше не нужен. В этом документе вы узнаете, как удалить кластер с помощью [портал Azure](https://portal.azure.com), [Azure PowerShell AZ](https://docs.microsoft.com/powershell/azure/overview)и [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).

> [!IMPORTANT]  
> При удалении кластера HDInsight не происходит удаления связанных с ним учетных записей хранения Azure или Data Lake Storage. Данные, хранящиеся в этих службах, можно повторно использовать в будущем.

## <a name="azure-portal"></a>портала Azure

1. Войдите на [портале Azure](https://portal.azure.com).

2. В меню слева перейдите к пункту **все службы** > **аналитика** > **кластеров HDInsight** и выберите свой кластер.

3. В представлении по умолчанию выберите значок **Удалить** . Выполните запрос, чтобы удалить кластер.

    ![Кнопка удаления кластера HDInsight](./media/hdinsight-delete-cluster/hdinsight-delete-cluster.png)

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
