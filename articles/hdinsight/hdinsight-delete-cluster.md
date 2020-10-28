---
title: Как удалить кластер HDInsight в Azure
description: Сведения о различных способах удаления кластера Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: H1Hack27Feb2017,hdinsightactive, devx-track-azurecli
ms.date: 11/29/2019
ms.openlocfilehash: 49a43f821a159af6944abb9509c24a8dd081be43
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/27/2020
ms.locfileid: "92748800"
---
# <a name="delete-an-hdinsight-cluster-using-your-browser-powershell-or-the-azure-cli"></a>Удаление кластера HDInsight с помощью браузера, PowerShell или Azure CLI

Начисление оплаты начинается после создания кластера HDInsight и прекращается только после его удаления. Для выставления счетов учитывается уровень Pro в минуту, поэтому кластер следует всегда удалять, когда он больше не используется. В этом документе вы узнаете, как удалить кластер с помощью [портал Azure](https://portal.azure.com), [Azure PowerShell AZ](/powershell/azure/)и [Azure CLI](/cli/azure/).

> [!IMPORTANT]  
> При удалении кластера HDInsight не происходит удаления связанных с ним учетных записей хранения Azure или Data Lake Storage. Данные, хранящиеся в этих службах, можно повторно использовать в будущем.

## <a name="azure-portal"></a>Портал Azure

1. Войдите на [портал Azure](https://portal.azure.com).

2. В меню слева перейдите к пункту **все службы**  >  **аналитика**  >  **кластеров HDInsight** и выберите свой кластер.

3. В представлении по умолчанию выберите значок **Удалить** . Выполните запрос, чтобы удалить кластер.

    ![Кнопка удаления кластера HDInsight](./media/hdinsight-delete-cluster/hdinsight-delete-cluster.png)

## <a name="azure-powershell"></a>Azure PowerShell

Замените `CLUSTERNAME` именем кластера HDInsight в приведенном ниже коде. В командной строке PowerShell введите следующую команду, чтобы удалить кластер:

```powershell
Remove-AzHDInsightCluster -ClusterName CLUSTERNAME
```

## <a name="azure-cli"></a>Azure CLI

Замените `CLUSTERNAME` именем кластера HDInsight и `RESOURCEGROUP` именем группы ресурсов в приведенном ниже коде.  В командной строке введите следующую команду, чтобы удалить кластер:

```azurecli
az hdinsight delete --name CLUSTERNAME --resource-group RESOURCEGROUP
```
