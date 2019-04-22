---
title: Использование HDFS CLI в хранилище Azure Data Lake Storage 2-го поколения
description: Общие сведения о HDFS CLI в хранилище Data Lake Storage 2-го поколения
services: storage
author: artemuwka
ms.service: storage
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: artek
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: d1c9eff08a7b9cc50ccdca4ce798ac4d0f3d35f2
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/17/2019
ms.locfileid: "59678026"
---
# <a name="using-the-hdfs-cli-with-data-lake-storage-gen2"></a>Использование HDFS CLI в Data Lake Storage Gen2

Можно получить доступ к и управления данными в вашей учетной записи хранения с помощью интерфейса командной строки, так же, как с помощью [Hadoop распределенной файловой системы (HDFS)](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html). В этой статье приводятся некоторые примеры, которые помогут вам приступить к работе.

HDInsight предоставляет доступ к распределенной файловой системе, которая локально присоединена к вычислительным узлам. Можно получить доступ к этой файловой системы с помощью оболочки, напрямую взаимодействующий с HDFS и другие файловые системы, которые поддерживает Hadoop.

Дополнительные сведения об HDFS CLI, см. в разделе [официальной документации](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html) и [руководстве по разрешениям в HDFS](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html)

>[!NOTE]
>Если вы используете Azure Databricks вместо HDInsight, и вы хотите взаимодействовать с данными с помощью интерфейса командной строки, интерфейса командной строки Databricks можно использовать для взаимодействия с файловой системой Databricks. См. в разделе [интерфейса командной строки Databricks](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html).

## <a name="use-the-hdfs-cli-with-an-hdinsight-hadoop-cluster-on-linux"></a>Использование HDFS CLI с кластером HDInsight Hadoop в Linux

Сначала установите [удаленный доступ к службам](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-information#remote-access-to-services). Если выбрать параметр [SSH](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix), образец кода PowerShell будет выглядеть следующим образом.

```powershell
#Connect to the cluster via SSH.
ssh sshuser@clustername-ssh.azurehdinsight.net
#Execute basic HDFS commands. Display the hierarchy.
hdfs dfs -ls /
#Create a sample directory.
hdfs dfs -mkdir /samplefolder
```
Строка подключения находится на портале Azure в колонке кластера HDInsight в разделе "SSH и вход в кластер". Учетные данные SSH были указаны во время создания кластера.

>[!IMPORTANT]
>Начисление оплаты начинается после создания кластера HDInsight и прекращается только после его удаления. Кластеры оплачиваются поминутно, поэтому всегда следует удалять кластер, когда он больше не нужен. Сведения об удалении кластера см. в [статье на эту тему](../../hdinsight/hdinsight-delete-cluster.md). Данные, размещенные в учетной записи хранения с поддержкой Data Lake Storage 2-го поколения, сохраняются даже после удаления кластера HDInsight.

## <a name="create-a-file-system"></a>Создание файловой системы

    hdfs dfs -D "fs.azure.createRemoteFileSystemDuringInitialization=true" -ls abfs://<file-system-name>@<storage-account-name>.dfs.core.windows.net/

* Замените заполнитель `<file-system-name>` именем, которое вы хотите предоставить вашей файловой системе.

* Замените заполнитель `<storage-account-name>` именем вашей учетной записи хранения.

## <a name="get-a-list-of-files-or-directories"></a>Получение списка файлов или каталогов

    hdfs dfs -ls <path>

Замените заполнитель `<path>` на URI файловой системы или папки в файловой системе.

Например: `hdfs dfs -ls abfs://my-file-system@mystorageaccount.dfs.core.windows.net/my-directory-name`

## <a name="create-a-directory"></a>создать каталог;

    hdfs dfs -mkdir [-p] <path>

Замените заполнитель `<path>` на имя корневой файловой системы или папки в файловой системе.

Например: `hdfs dfs -mkdir abfs://my-file-system@mystorageaccount.dfs.core.windows.net/`

## <a name="delete-a-file-or-directory"></a>Удаление файла или каталога

    hdfs dfs -rm <path>

Замените заполнитель `<path>` на URI файла или папки, которые нужно удалить.

Например: `hdfs dfs -rmdir abfs://my-file-system@mystorageaccount.dfs.core.windows.net/my-directory-name/my-file-name`

## <a name="display-the-access-control-lists-acls-of-files-and-directories"></a>Отображение списков управления доступом (ACL) файлов и каталогов

    hdfs dfs -getfacl [-R] <path>

Пример:

`hdfs dfs -getfacl -R /dir`

См. [getfacl](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#getfacl)

## <a name="set-acls-of-files-and-directories"></a>Настройка ACL файлов и каталогов

    hdfs dfs -setfacl [-R] [-b|-k -m|-x <acl_spec> <path>]|[--set <acl_spec> <path>]

Пример:

`hdfs dfs -setfacl -m user:hadoop:rw- /file`

См. [setfacl](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#setfacl)

## <a name="change-the-owner-of-files"></a>Изменение владельца файлов

    hdfs dfs -chown [-R] <new_owner>:<users_group> <URI>

См. [chown](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#chown)

## <a name="change-group-association-of-files"></a>Изменение связи группы файлов

    hdfs dfs -chgrp [-R] <group> <URI>

См. [chgrp](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#chgrp)

## <a name="change-the-permissions-of-files"></a>Изменение разрешений файлов

    hdfs dfs -chmod [-R] <mode> <URI>

См. [chmod](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#chmod)

Полный список команд можно просмотреть на веб-сайте [Apache Hadoop 2.4.1 File System Guide](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html).

## <a name="next-steps"></a>Дальнейшие действия

* [Краткое руководство. Запуск задания Spark в Azure Databricks с помощью портала Azure](./data-lake-storage-quickstart-create-databricks-account.md)

* [Дополнительные сведения о списках управления доступом для файлов и каталогов](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)
