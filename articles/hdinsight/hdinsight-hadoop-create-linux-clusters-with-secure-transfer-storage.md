---
title: Apache Hadoop & безопасное хранилище передачи - Azure HDInsight
description: Узнайте, как создать кластеры HDInsight с помощью учетных записей хранения Azure с включенной безопасной передачей.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/18/2020
ms.openlocfilehash: c1e5ca8b0bb828e5e8ce896bba6a5278266b118e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77560088"
---
# <a name="apache-hadoop-clusters-with-secure-transfer-storage-accounts-in-azure-hdinsight"></a>Кластеры Apache Hadoop с защищенными учетными записями хранения данных о передаче в Azure HDInsight

Функция [Secure transfer required](../storage/common/storage-require-secure-transfer.md) (Необходима безопасная передача) усиливает безопасность учетной записи хранения Azure путем принудительной передачи всех запросов к вашей учетной записи через безопасное подключение. Эта функция и схема wasbs поддерживаются только кластером HDInsight версии 3.6 или более новым.

> [!IMPORTANT]
> Включение безопасной передачи хранилища после создания кластера может привести к ошибкам с помощью учетной записи хранилища и не рекомендуется. Лучше создать новый кластер, используя учетную запись хранилища с уже включенной безопасной передачей.

## <a name="storage-accounts"></a>Учетные записи хранения

### <a name="azure-portal"></a>Портал Azure

По умолчанию требуемое свойство передачи включено при создании учетной записи хранилища на портале Azure.

Чтобы обновить существующую учетную запись хранения на портале Azure, [см.](../storage/common/storage-require-secure-transfer.md#require-secure-transfer-for-an-existing-storage-account)

### <a name="powershell"></a>PowerShell

Для PowerShell cmdlet [New-AzStorageAccount](https://docs.microsoft.com/powershell/module/az.storage/new-azstorageaccount) `-EnableHttpsTrafficOnly` , убедитесь, что параметр установлен на `1`.

Чтобы обновить существующую учетную запись хранения с PowerShell, [см. Требуют безопасной передачи с PowerShell.](../storage/common/storage-require-secure-transfer.md#require-secure-transfer-with-powershell)

### <a name="azure-cli"></a>Azure CLI

Для создания [учетной записи](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create)хранилища Az, `--https-only` обеспечивающей `true`настройку параметра, убедитесь, что параметр установлен.

Чтобы обновить существующую учетную запись хранения с помощью Azure CLI, [см.](../storage/common/storage-require-secure-transfer.md#require-secure-transfer-with-azure-cli)

## <a name="add-additional-storage-accounts"></a>Добавление дополнительных учетных записей хранения

Добавить дополнительные учетные записи хранения с включенной безопасной передачей можно несколькими способами:

* Измените шаблон Azure Resource Manager в предыдущем разделе.
* Создайте кластер с помощью [портала Azure](https://portal.azure.com) и укажите связанную учетную запись хранения.
* Используйте действия скриптов для добавления дополнительных учетных записей хранения с включенной безопасной передачей в имеющийся кластер HDInsight. Дополнительные сведения см. в статье [Добавление дополнительных учетных записей хранения в HDInsight](hdinsight-hadoop-add-storage.md).

## <a name="next-steps"></a>Дальнейшие действия

* использование службы хранилища Azure (WASB) вместо [Apache Hadoop HDFS](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsUserGuide.html) в качестве хранилища данных по умолчанию.
* Сведения о том, как HDInsight использует службу хранилища Azure, см. в статье [Использование службы хранилища Azure в HDInsight](hdinsight-hadoop-use-blob-storage.md).
* Сведения об отправке данных в HDInsight см. в статье [Отправка данных в HDInsight](hdinsight-upload-data.md).
