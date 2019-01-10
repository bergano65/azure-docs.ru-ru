---
title: Как удалить кластер HDInsight в Azure
description: Сведения о различных способах удаления кластера HDInsight.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 03/22/2018
ms.author: hrasheed
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: f5c2f6104a765c0e598e41234891c492686019dd
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/21/2018
ms.locfileid: "53718985"
---
# <a name="delete-an-hdinsight-cluster-using-your-browser-powershell-or-the-azure-classic-cli"></a>Удаление кластера HDInsight с помощью браузера, PowerShell или классического интерфейса Azure CLI

Начисление оплаты начинается после создания кластера HDInsight и прекращается только после его удаления. Кластеры оплачиваются поминутно, поэтому всегда следует удалять кластер, когда он больше не нужен. Из этого документа вы узнаете, как удалить кластер с помощью [портала Azure](https://portal.azure.com), [Azure PowerShell](https://docs.microsoft.com/powershell/azure/) и классического интерфейс командной строки Azure.

> [!IMPORTANT]  
> При удалении кластера HDInsight не происходит удаления связанных с ним учетных записей хранения Azure или Data Lake Storage. Данные, хранящиеся в этих службах, можно повторно использовать в будущем.

## <a name="azure-portal"></a>Портал Azure

1. Войдите на [портал Azure](https://portal.azure.com) и выберите свой кластер HDInsight. Если кластер HDInsight не закреплен на панели мониторинга, его можно найти по имени с помощью поля поиска.
   
    ![поиск по порталу](./media/hdinsight-delete-cluster/navbar.png)

2. В параметрах кластера выберите значок **Удалить**. При появлении запроса выберите **Да**, чтобы удалить кластер.
   
    ![значок удаления](./media/hdinsight-delete-cluster/deletecluster.png)

## <a name="azure-powershell"></a>Azure PowerShell

Введите следующую команду в командной строке PowerShell, чтобы удалить кластер.

    Remove-AzureRmHDInsightCluster -ClusterName CLUSTERNAME

Замените **CLUSTERNAME** именем кластера HDInsight.

## <a name="azure-classic-cli"></a>Классический Azure CLI

[!INCLUDE [classic-cli-warning](../../includes/requires-classic-cli.md)]

Введите следующую команду в командной строке, чтобы удалить кластер.

    azure hdinsight cluster delete CLUSTERNAME

Замените **CLUSTERNAME** именем кластера HDInsight.
