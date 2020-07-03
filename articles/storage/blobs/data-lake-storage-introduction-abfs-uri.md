---
title: Использование URI в Azure Data Lake Storage Gen2
description: Использование URI в Azure Data Lake Storage Gen2
author: normesta
ms.topic: conceptual
ms.author: normesta
ms.date: 12/06/2018
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.reviewer: jamesbak
ms.openlocfilehash: fa0f67e0d72ee5710a42b6de744ddae98e20220a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80437128"
---
# <a name="use-the-azure-data-lake-storage-gen2-uri"></a>Использование URI в Azure Data Lake Storage Gen2

Драйвер [файловой системы Hadoop](https://www.aosabook.org/en/hdfs.html), совместимый с Azure Data Lake Storage 2-го поколения, обозначается идентификатором схемы `abfs` (файловая система BLOB-объектов Azure). Драйвер ABFS совместим с другими драйверами файловой системы Hadoop и использует формат URI для обращения к файлами и каталогам в учетных записях, поддерживающих Data Lake Storage Gen2.

## <a name="uri-syntax"></a>Синтаксис URI

Синтаксис URI для Data Lake Storage Gen2 зависит от того, настроена ли служба Data Lake Storage Gen2 в качестве файловой системы по умолчанию в вашей учетной записи хранения.

Если вы хотите обратиться к учетной записи, которая поддерживает Data Lake Storage 2-го поколения и при создании которой эта файловая система **не** была настроена как файловая система по умолчанию, используется сокращенный URI со следующим синтаксисом:

<pre>abfs[s]<sup>1</sup>://&lt;file_system&gt;<sup>2</sup>@&lt;account_name&gt;<sup>3</sup>.dfs.core.windows.net/&lt;path&gt;<sup>4</sup>/&lt;file_name&gt;<sup>5</sup></pre>

1. **Scheme identifier** (Идентификатор схемы) — указывает протокол `abfs`. Можно установить соединение с протоколом TLS или без него, ранее известное как SSL (SSL), Connection. Используйте `abfss` для подключения с помощью TLS-подключения.

2. **File system** (Файловая система) — указывает на родительское расположение, в котором хранятся все файлы и папки. Этот параметр аналогичен контейнерам в службе Azure Storage Blob.

3. **Account name** (Имя учетной записи) — содержит имя, которое вы присвоили учетной записи хранения при ее создании.

4. **Paths** (Пути) — содержит представление структуры каталогов, в котором сегменты разделяются косыми чертами (`/`).

5. **Имя файла**: имя отдельного файла. Этот параметр можно не указывать, если вы обращаетесь к каталогу.

Если при создании учетной записи, к которой нужно обратиться, эта файловая система была настроена как файловая система по умолчанию, используется сокращенный URI со следующим синтаксисом:

<pre>/&lt;path&gt;<sup>1</sup>/&lt;file_name&gt;<sup>2</sup></pre>

1. **Path** (Путь) — содержит представление структуры каталогов, в котором сегменты разделяются косыми чертами (`/`).

2. **File Name** (Имя файла) — содержит имя конкретного файла.


## <a name="next-steps"></a>Дальнейшие шаги

- [Использование Azure Data Lake Storage 2-го поколения с кластерами Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
