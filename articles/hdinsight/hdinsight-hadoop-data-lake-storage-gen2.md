---
title: Использование Azure Data Lake Storage (ADLS) 2-го поколения с Apache Hadoop в Azure HDInsight
description: В этой статье приведен обзор Azure Data Lake Storage 2-го поколения и его работы с Azure HDInsight.
services: hdinsight,storage
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 11/21/2018
ms.openlocfilehash: 2ae11afe1ecbe500a4851aab6d56e612fbe79ee6
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/06/2018
ms.locfileid: "52976750"
---
# <a name="use-azure-data-lake-storage-gen2-with-apache-hadoop-in-azure-hdinsight"></a>Использование Azure Data Lake Storage 2-го поколения с Apache Hadoop в Azure HDInsight

В Azure Data Lake Store (ADLS) 2-го поколения реализованы основные возможности Azure Data Lake Storage 1-го поколения, такие как совместимая с Hadoop файловая система, Azure Active Directory и списки управления доступом на основе POSIX, и интегрированы в хранилище BLOB-объектов. Эта комбинация обеспечивает производительность Azure Data Lake Storage 1-го поколения в сочетании с распределением по уровням хранилища BLOB-объектов и возможностью управлять жизненным циклом данных.

## <a name="core-functionality-of-azure-data-lake-storage-gen2"></a>Основные функциональные возможности Azure Data Lake Storage 2-го поколения

- Доступ, совместимый с Hadoop. Хранилище Azure Data Lake Storage 2-го поколения позволяет получать доступ к данным и управлять ими так же, как и в распределенной файловой системе Hadoop (HDFS). Во всех средах Apache Hadoop, в том числе Azure HDInsight и Azure Databricks, доступен драйвер ABFS, который позволяет получать доступ к хранимым данным в Data Lake Storage 2-го поколения.

- Супермножество разрешений POSIX. Модель безопасности Data Lake 2-го поколения поддерживает разрешения ACL и POSIX, а также некоторую дополнительную детализацию, относящуюся к Data Lake Storage 2-го поколения. Параметры можно настроить с помощью средств администрирования или платформ, таких как Apache Hive и Apache Spark.

- Экономичность. Data Lake Storage 2-го поколения обеспечивает недорогостоящие транзакции и емкость хранилища. Такие функции, как жизненный цикл хранилища BLOB-объектов Azure, помогают снизить затраты, регулируя тарифные ставки при перемещении данных в течение жизненного цикла.

- Поддержка средств, платформ и приложений хранилища BLOB-объектов. Хранилище Data Lake Storage 2-го поколения поддерживает множество средств, платформ и приложений хранилища BLOB-объектов.

- Оптимизированный драйвер. Драйвер ABFS оптимизирован специально для аналитики больших данных. Соответствующие интерфейсы REST API подключены через конечную точку dfs — dfs.core.windows.net.

## <a name="whats-new-about-azure-data-lake-storage-gen-2"></a>Новые возможности Azure Data Lake Storage 2-го поколения

### <a name="managed-identities-for-secure-file-access"></a>Управляемые удостоверения для безопасного доступа к файлам

Azure HDInsight использует управляемые удостоверения, чтобы защитить доступ к кластеру файлов в Azure Data Lake Storage 2-го поколения. Управляемые удостоверения — это функция Azure Active Directory, которая предоставляет службы Azure с набором автоматически управляемых учетных данных. Эти учетные данные можно использовать для аутентификации в любой службе, которая поддерживает аутентификацию AD. Для управляемых удостоверений не требуется хранить учетные данные в коде или файлах конфигурации.

Дополнительные сведения см. в разделе [Что такое управляемые удостоверения для ресурсов Azure](../active-directory/managed-identities-azure-resources/overview.md).

### <a name="azure-blob-filesystem-abfs-driver"></a>Драйвер файловой системы BLOB-объектов (ABFS)

Приложения Apache Hadoop изначально рассчитаны на чтение и запись данных из локального дискового хранилища. Драйвер файловой системы Hadoop, такой как ABFS, позволяет приложениям Hadoop работать с облачным хранилищем за счет эмуляции регулярных операций файловой системы Hadoop для приложения. Затем драйвер преобразует эти операции таким образом, чтобы их поддерживала фактическая платформа облачного хранения.

Ранее драйвер файловой системы Hadoop преобразовывал все операции файловой системы в вызовы REST API службы хранилища Azure на стороне клиента, а затем вызывал REST API. Это преобразование на стороне клиента привело к нескольким вызовам REST API на одну операцию файловой системы, например переименование файла. В ABFS определенная логика файловой системы Hadoop перемещена со стороны клиента на сторону сервера. Теперь API ADLS 2-го поколения выполняется параллельно с API BLOB-объектов. Такая миграция повышает производительность, так как теперь общие операции файловой системы Hadoop выполняются с помощью одного вызова REST API.

Дополнительные сведения см. в [The Azure Blob Filesystem driver (ABFS): A dedicated Azure Storage driver for Hadoop](../storage/data-lake-storage/abfs-driver.md) (Драйвер файловой системы BLOB-объектов Azure (ABFS) — выделенный драйвер службы хранилища Azure Storage для Hadoop).

### <a name="adls-gen-2-uri-scheme"></a>Схема универсального кода ресурса ADLS 2-го поколения

ADLS 2-го поколения использует схему URI для доступа к файлам в службе хранилища Azure из HDInsight:

`abfs[s]://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/<PATH>`

Эта схема URI предоставляет как доступ с использованием SSL-шифрования с префиксом `abfss://`, так и незашифрованный доступ с префиксом `abfs://`. Мы рекомендуем использовать `abfss` всегда, когда это возможно, даже при обращении к данным, которые хранятся в том же регионе Azure.

`<FILE_SYSTEM_NAME>` идентифицирует путь к файловой системе Data Lake Storage 2-го поколения.

`<ACCOUNT_NAME>` определяет имя учетной записи службы хранилища Azure. Обязательно использовать полное доменное имя (FQDN).

`<PATH>` — это имя пути к файлу или каталогу HDFS.

Если значения для `<FILE_SYSTEM_NAME>` и `<ACCOUNT_NAME>` не указаны, используется файловая система по умолчанию. Для файлов в файловой системе по умолчанию можно использовать относительный или абсолютный путь. Например, для ссылки на файл `hadoop-mapreduce-examples.jar`, который поставляется с кластерами HDInsight, можно использовать один из приведенных ниже вариантов:

```
abfss://myfilesystempath@myaccount.dfs.core.windows.net/example/jars/hadoop-mapreduce-examples.jar
abfss:///example/jars/hadoop-mapreduce-examples.jar /example/jars/hadoop-mapreduce-examples.jar
```

> [!Note]
> В кластерах HDInsight версий 2.1 и 1.6 файл называется `hadoop-examples.jar`. При работе с файлами вне HDInsight большинство программ не распознают формат ABFS и вместо этого ожидают формат базового пути, например `example/jars/hadoop-mapreduce-examples.jar`.

Дополнительные сведения см. в статье [Use the Azure Data Lake Storage Gen2 URI](../storage/data-lake-storage/introduction-abfs-uri.md) (Использование универсального кода ресурса в Azure Data Lake Storage 2-го поколения).

## <a name="next-steps"></a>Дополнительная информация
- [Общие сведения о хранилище Azure Data Lake Storage Gen2 (предварительная версия)](../storage/data-lake-storage/introduction.md)
- [Use Azure Data Lake Storage Gen2 Preview with Azure HDInsight clusters](../storage/data-lake-storage/use-hdi-cluster.md) (Использование хранилища Azure Data Lake Storage 2-го поколения (предварительная версия) с кластерами Azure HDInsight)