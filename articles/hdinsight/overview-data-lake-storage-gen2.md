---
title: Обзор данных Azure Data Lake Gen2 в HDInsight
description: Обзор данных хранения озера Gen2 в HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/21/2020
ms.openlocfilehash: 677d8348cc230e4f64915ac5fc1e86b9b50ab5c3
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81873346"
---
# <a name="azure-data-lake-storage-gen2-overview-in-hdinsight"></a>Обзор данных Azure Data Lake Gen2 в HDInsight

Azure Data Lake Storage 2-го поколения принимает основные возможности Azure Data Lake Storage 1-го поколения и интегрирует их в хранилище BLOB-объектов Azure. К этим функциям относится файловая система, которая совместима с Hadoop, Azure Active Directory (Azure AD) и списки управления доступом (ACL) на основе POSIX. Эта комбинация позволяет воспользоваться преимуществами хранилища Azure Data Lake Storage Gen1. При использовании многоуровневого и управления жизненным циклом данных хранилища Blob.

Дополнительные сведения об Azure Data Lake Storage 2-го поколения см. в [этой статье](../storage/blobs/data-lake-storage-introduction.md).

## <a name="core-functionality-of-azure-data-lake-storage-gen2"></a>Основные функциональные возможности Azure Data Lake Storage 2-го поколения

* **Доступ, совместимый с Hadoop:** В Azure Data Lake Storage Gen2 вы можете управлять и получать доступ к данным так же, как и с помощью распределенной файловой системы Hadoop (HDFS). Во всех средах Apache Hadoop, в том числе Azure HDInsight и Azure Databricks, доступен драйвер файловой системы больших двоичных объектов Azure (ABFS). Используйте ABFS для получения доступа к данным, хранящимся в Data Lake Storage 2-го поколения.

* **Супернабор разрешений POSIX:** Модель безопасности Data Lake Gen2 поддерживает разрешения ACL и POSIX наряду с дополнительной гранулированностью, характерной для Data Lake Storage Gen2. Параметры можно настроить с помощью средств администрирования или платформ, таких как Apache Hive и Apache Spark.

* **Эффективность затрат:** Data Lake Storage Gen2 предлагает недорогие емкости и транзакции. Жизненные циклы хранения Azure Blob помогают снизить затраты, регулируя тарифы выставления счетов по мере прохождения данных по жизненному циклу.

* **Совместимость с инструментами хранения, инфраструктурами и приложениями Blob:** Data Lake Storage Gen2 продолжает работать с широким спектром инструментов, инфраструктур и приложений для хранения Blob.

* **Оптимизированный драйвер:** Драйвер ABFS оптимизирован специально для анализа больших данных. Соответствующие интерфейсы REST API подключены через конечную точку распределенной файловой системы (DFS) — dfs.core.windows.net.

## <a name="whats-new-for-azure-data-lake-storage-gen-2"></a>Новые возможности Azure Data Lake Storage 2-го поколения

### <a name="managed-identities-for-secure-file-access"></a>Управляемые удостоверения для безопасного доступа к файлам

Azure HDInsight использует управляемые удостоверения, чтобы защитить доступ к кластеру файлов в Azure Data Lake Storage 2-го поколения. Управляемые удостоверения — это функция Azure Active Directory, которая предоставляет службы Azure с набором автоматически управляемых учетных данных. Эти учетные данные можно использовать для аутентификации в любой службе, которая поддерживает аутентификацию Active Directory. Для управляемых удостоверений не требуется хранить учетные данные в коде или файлах конфигурации.

Дополнительные сведения см. в статье [Что такое управляемые удостоверения для ресурсов Azure?](../active-directory/managed-identities-azure-resources/overview.md)

### <a name="azure-blob-file-system-driver"></a>Драйвер файловой системы больших двоичных объектов Azure

Приложения Apache Hadoop изначально рассчитаны на чтение и запись данных из локального дискового хранилища. Драйвер файловой системы Hadoop, такой как ABFS, позволяет приложениям Hadoop работать с облачным хранилищем. Работает, имитируя регулярные операции файловой системы Hadoop. Затем драйвер преобразует полученные из приложения команды в операции, чтобы их поддерживала фактическая платформа облачного хранения.

Ранее драйвер файловой системы Hadoop преобразовал все операции файловой системы в API API хранения данных Azure Storage на стороне клиента. А затем сослался на REST API. Это преобразование на стороне клиента привело к нескольким вызовам REST API на одну операцию файловой системы, например переименование файла. ABFS перевела логику файловой системы Hadoop со стороны клиента на серверную. API Azure Data Lake Storage 2-го поколения теперь выполняется параллельно с API больших двоичных объектов. Такая миграция повышает производительность, так как теперь общие операции файловой системы Hadoop выполняются с помощью одного вызова REST API.

Для получения дополнительной информации [см. Драйвер файловОй системы Azure Blob (ABFS): специальный драйвер хранения Azure для Hadoop](../storage/blobs/data-lake-storage-abfs-driver.md).

### <a name="uri-scheme-for-azure-data-lake-storage-gen-2"></a>Схема URI для Azure Data Lake Storage 2-го поколения

Azure Data Lake Storage 2-го поколения использует схему URI для доступа к файлам в службе хранилища Azure из HDInsight:

`abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/<PATH>`

Схема URI обеспечивает SSL-зашифрованный доступ.

`<FILE_SYSTEM_NAME>` идентифицирует путь к файловой системе Data Lake Storage 2-го поколения.

`<ACCOUNT_NAME>` определяет имя учетной записи службы хранилища Azure. Обязательно использовать полное доменное имя (FQDN).

`<PATH>` — это имя пути к файлу или каталогу HDFS.

Если значения для `<FILE_SYSTEM_NAME>` и `<ACCOUNT_NAME>` не указаны, используется файловая система по умолчанию. Для файлов в файловой системе по умолчанию можно использовать относительный или абсолютный путь. Например, для ссылки на файл `hadoop-mapreduce-examples.jar`, который поставляется с кластерами HDInsight, можно использовать один из приведенных ниже вариантов:

```
abfs://myfilesystempath@myaccount.dfs.core.windows.net/example/jars/hadoop-mapreduce-examples.jar
abfs:///example/jars/hadoop-mapreduce-examples.jar /example/jars/hadoop-mapreduce-examples.jar
```

> [!NOTE]
> В кластерах HDInsight версий 2.1 и 1.6 файл называется `hadoop-examples.jar`. При работе с файлами вне HDInsight вы увидите, что большинство программ не распознают формат ABFS, но вместо этого ожидают формат базового пути, например `example/jars/hadoop-mapreduce-examples.jar`.

Дополнительные сведения см. в статье [Use the Azure Data Lake Storage Gen2 URI](../storage/blobs/data-lake-storage-introduction-abfs-uri.md) (Использование универсального кода ресурса в Azure Data Lake Storage 2-го поколения).

## <a name="next-steps"></a>Дальнейшие действия

* [Общие сведения о хранилище Azure Data Lake Storage Gen2 (предварительная версия)](../storage/blobs/data-lake-storage-introduction.md)
* [Введение в хранилище Azure](../storage/common/storage-introduction.md)
* [Обзор данных Azure Data Lake Data Gen1](./overview-data-lake-storage-gen1.md)