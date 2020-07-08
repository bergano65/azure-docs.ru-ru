---
title: Apache Hadoop & безопасное хранение данных в Azure HDInsight
description: Узнайте, как создать кластеры HDInsight с помощью учетных записей хранения Azure с включенной безопасной передачей.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 02/18/2020
ms.openlocfilehash: 43abe6a827af668f2b756e5f00497fbfe1698413
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/08/2020
ms.locfileid: "86083242"
---
# <a name="apache-hadoop-clusters-with-secure-transfer-storage-accounts-in-azure-hdinsight"></a>Apache Hadoop кластеры с учетными записями хранения с безопасной переносю в Azure HDInsight

Функция [Secure transfer required](../storage/common/storage-require-secure-transfer.md) (Необходима безопасная передача) усиливает безопасность учетной записи хранения Azure путем принудительной передачи всех запросов к вашей учетной записи через безопасное подключение. Эта функция и схема wasbs поддерживаются только кластером HDInsight версии 3.6 или более новым.

> [!IMPORTANT]
> Включение безопасного обмена хранилищами после создания кластера может привести к ошибкам с помощью учетной записи хранения и не рекомендуется. Лучше создать новый кластер с использованием учетной записи хранения с уже включенной безопасной защитой.

## <a name="storage-accounts"></a>Учетные записи хранения

### <a name="azure-portal"></a>Портал Azure

По умолчанию при создании учетной записи хранения в портал Azure включается свойство Обязательное безопасное перемещение.

Сведения об обновлении существующей учетной записи хранения с портал Azure см. в разделе [Обязательное безопасное перемещение с помощью портал Azure](../storage/common/storage-require-secure-transfer.md#require-secure-transfer-for-an-existing-storage-account).

### <a name="powershell"></a>PowerShell

Для командлета PowerShell [New-азсторажеаккаунт](https://docs.microsoft.com/powershell/module/az.storage/new-azstorageaccount)убедитесь, что параметр `-EnableHttpsTrafficOnly` имеет значение `1` .

Сведения об обновлении существующей учетной записи хранения с помощью PowerShell см. в статье [требование безопасной пересылки с помощью PowerShell](../storage/common/storage-require-secure-transfer.md#require-secure-transfer-with-powershell).

### <a name="azure-cli"></a>Azure CLI

Для команды Azure CLI [AZ Storage Account Create](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create), убедитесь, что параметр `--https-only` имеет значение `true` .

Сведения об обновлении существующей учетной записи хранения с Azure CLI см. в разделе [Обязательное безопасное перемещение с помощью Azure CLI](../storage/common/storage-require-secure-transfer.md#require-secure-transfer-with-azure-cli).

## <a name="add-additional-storage-accounts"></a>Добавление дополнительных учетных записей хранения

Добавить дополнительные учетные записи хранения с включенной безопасной передачей можно несколькими способами:

* Измените шаблон Azure Resource Manager в предыдущем разделе.
* Создайте кластер с помощью [портала Azure](https://portal.azure.com) и укажите связанную учетную запись хранения.
* Используйте действия скриптов для добавления дополнительных учетных записей хранения с включенной безопасной передачей в имеющийся кластер HDInsight. Дополнительные сведения см. в статье [Добавление дополнительных учетных записей хранения в HDInsight](hdinsight-hadoop-add-storage.md).

## <a name="next-steps"></a>Следующие шаги

* использование службы хранилища Azure (WASB) вместо [Apache Hadoop HDFS](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsUserGuide.html) в качестве хранилища данных по умолчанию.
* Сведения о том, как HDInsight использует службу хранилища Azure, см. в статье [Использование службы хранилища Azure в HDInsight](hdinsight-hadoop-use-blob-storage.md).
* Сведения об отправке данных в HDInsight см. в статье [Отправка данных в HDInsight](hdinsight-upload-data.md).
