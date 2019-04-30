---
title: Как удалить кластер HDInsight в Azure
description: Сведения о различных способах удаления кластера HDInsight.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: hrasheed
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: eca7b4f8bd7e91bc8dcb9bcc49ed3b981010aaee
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "62097194"
---
# <a name="delete-an-hdinsight-cluster-using-your-browser-powershell-or-the-azure-cli"></a>Удаление кластера HDInsight с помощью браузера, PowerShell или Azure CLI

Начисление оплаты начинается после создания кластера HDInsight и прекращается только после его удаления. Счета выставляются пропорционально по минутам, поэтому вы всегда можете удалить кластер, если он больше не используется. В этом документе вы узнаете, как удалить кластер с помощью [портала Azure](https://portal.azure.com), [модуля Azure PowerShell Az](https://docs.microsoft.com/powershell/azure/overview)и [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).

> [!IMPORTANT]  
> При удалении кластера HDInsight не происходит удаления связанных с ним учетных записей хранения Azure или Data Lake Storage. Данные, хранящиеся в этих службах, можно повторно использовать в будущем.

## <a name="azure-portal"></a>Портал Azure

1. Войдите на [портале Azure](https://portal.azure.com).

2. В меню слева, перейдите к **все службы** > **Analytics** > **кластеры HDInsight** и выберите свой кластер.

3. Представление по умолчанию, выберите **удалить** значок. Следуйте указаниям, чтобы удалить кластер.
   
    ![значок удаления](./media/hdinsight-delete-cluster/deletecluster.png)

## <a name="azure-powershell-az-module"></a>Модуль Azure PowerShell Az

Замените `CLUSTERNAME` с именем кластера HDInsight в приведенном ниже коде. В командной строке PowerShell введите следующую команду, чтобы удалить кластер:

```powershell
Remove-AzHDInsightCluster -ClusterName CLUSTERNAME
```

## <a name="azure-cli"></a>Инфраструктура CLI Azure

Замените `CLUSTERNAME` с именем кластера HDInsight, и `RESOURCEGROUP` с именем группы ресурсов в приведенном ниже коде.  В командной строке введите следующую команду, чтобы удалить кластер:

```azurecli
az hdinsight delete --name CLUSTERNAME --resource-group RESOURCEGROUP
```
