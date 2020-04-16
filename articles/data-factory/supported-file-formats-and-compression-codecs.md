---
title: Поддерживаемые форматы файлов на фабрике данных Azure
description: В этой статье описаны форматы файлов и коды сжатия, поддерживаемые соединителями на основе файлов в фабрике данных Azure.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: jingwang
ms.openlocfilehash: dbcfad3dd3db9f5c9431e07d85d77a77a10283c4
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81419040"
---
# <a name="supported-file-formats-and-compression-codecs-in-azure-data-factory"></a>Поддерживаемые форматы файлов и кодеки сжатия в фабрике данных Azure
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

*Эта статья относится к следующим разъемым: [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure File Storage](connector-azure-file-storage.md), File [System,](connector-file-system.md) [FTP,](connector-ftp.md) [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS,](connector-hdfs.md) [HTTP](connector-http.md), и [SFTP](connector-sftp.md).*

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Вы можете использовать [действие Copy](copy-activity-overview.md) для копирования файлов как между двумя файлохранилищами данных, и в этом случае данные копируются эффективно без сериализации или десериализации. 

Кроме того, можно также разобрать или создать файлы данного формата. Например, можно выполнить следующее:

* Копируйте данные из предварительной базы данных S'L Server и записывайте в Azure Data Lake Storage Gen2 в формате Parquet.
* Копирование файлов в текстовом формате (CSV) из файловой системы и запись в хранилище Azure Blob в формате Avro.
* Копируйте файлы на молнии из файловой системы, распаковывайте их на лету и записывайте извлеченные файлы в Azure Data Lake Storage Gen2.
* Копирование данных в формате сжатого текста Gzip (CSV) из хранилища Azure Blob и запишите их в базу данных Azure S'L.
* Гораздо больше мероприятий, которые требуют сериализации/ десериализации или сжатия/ декомпрессии.

## <a name="next-steps"></a>Дальнейшие действия

См. другие статьи о действиях копирования:

- [Общие сведения о действии копирования](copy-activity-overview.md)
- [Производительность действия копирования](copy-activity-performance.md)
