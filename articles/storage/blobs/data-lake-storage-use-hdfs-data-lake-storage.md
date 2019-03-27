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
ms.openlocfilehash: 051681150501f7c5737f335f8eb48144b08bb990
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/26/2019
ms.locfileid: "58482672"
---
# <a name="using-the-hdfs-cli-with-data-lake-storage-gen2"></a>Использование HDFS CLI в Data Lake Storage Gen2

Хранилище Azure Data Lake Storage 2-го поколения позволяет получать доступ к данным и управлять ими так же, как и в [распределенной файловой системе Hadoop (HDFS)](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html). Если кластер HDInsight подключен или с помощью Azure Databricks выполняет задание Apache Spark по анализу данных, которые хранятся в учетной записи хранения Azure, вы можете использовать интерфейс командной строки для извлечения и обработки загруженных данных.

## <a name="hdfs-cli-with-hdinsight"></a>Использование HDFS CLI в HDInsight

HDInsight предоставляет доступ к распределенной файловой системе, которая локально присоединена к вычислительным узлам. Доступ к этой файловой системе можно получить с помощью оболочки, которая напрямую взаимодействует с HDFS и другими файловыми системами, поддерживаемыми Hadoop. Ниже приведен список стандартных команд и ссылки на полезные ресурсы.

>[!IMPORTANT]
>Начисление оплаты начинается после создания кластера HDInsight и прекращается только после его удаления. Кластеры оплачиваются поминутно, поэтому всегда следует удалять кластер, когда он больше не нужен. Сведения об удалении кластера см. в [статье на эту тему](../../hdinsight/hdinsight-delete-cluster.md). Данные, размещенные в учетной записи хранения с поддержкой Data Lake Storage 2-го поколения, сохраняются даже после удаления кластера HDInsight.

### <a name="create-a-file-system"></a>Создание файловой системы

    hdfs dfs -D "fs.azure.createRemoteFileSystemDuringInitialization=true" -ls abfs://<file-system-name>@<storage-account-name>.dfs.core.windows.net/

* Замените заполнитель `<file-system-name>` именем, которое вы хотите предоставить вашей файловой системе.

* Замените заполнитель `<storage-account-name>` именем вашей учетной записи хранения.

### <a name="get-a-list-of-files-or-directories"></a>Получение списка файлов или каталогов

    hdfs dfs -ls <path>

Замените заполнитель `<path>` на URI файловой системы или папки в файловой системе.

Например: `hdfs dfs -ls abfs://my-file-system@mystorageaccount.dfs.core.windows.net/my-directory-name`

### <a name="create-a-directory"></a>создать каталог;

    hdfs dfs -mkdir [-p] <path>

Замените заполнитель `<path>` на имя корневой файловой системы или папки в файловой системе.

Например: `hdfs dfs -mkdir abfs://my-file-system@mystorageaccount.dfs.core.windows.net/`

### <a name="delete-a-file-or-directory"></a>Удаление файла или каталога

    hdfs dfs -rm <path>

Замените заполнитель `<path>` на URI файла или папки, которые нужно удалить.

Например: `hdfs dfs -rmdir abfs://my-file-system@mystorageaccount.dfs.core.windows.net/my-directory-name/my-file-name`

### <a name="use-the-hdfs-cli-with-an-hdinsight-hadoop-cluster-on-linux"></a>Использование HDFS CLI с кластером HDInsight Hadoop в Linux

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

Дополнительные сведения о HDFS CLI можно узнать [на официальном сайте](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html) или из [HDFS Permissions Guide](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html) (Руководства по разрешениям в HDFS). Дополнительные сведения о списках ACL в Databricks см. в статье [Секреты CLI](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#secrets-cli).

## <a name="hdfs-cli-with-azure-databricks"></a>Использование HDFS CLI в Azure Databricks

Интерфейс командной строки, предоставляемый Databricks, построен на базе Databricks REST API и его легко использовать. Проект с открытым исходным кодом размещается на сайте [GitHub](https://github.com/databricks/databricks-cli). Ниже приведен список стандартных команд.

### <a name="get-a-list-of-files-or-directories"></a>Получение списка файлов или каталогов

    dbfs ls [-l]

### <a name="create-a-directory"></a>создать каталог;

    dbfs mkdirs

### <a name="delete-a-file"></a>Удаление файла

    dbfs rm [-r]

Одним из способом взаимодействия с Databricks является использование записных книжек. В то время как у записной книжки есть основной язык, у вас появляется возможность смешивать языки, указав magic-команду %language в начале ячейки. Команда %sh позволяет выполнять код оболочки в записной книжке. Похожий пример был приведен в этой статье ранее (в разделе о HDInsight).

### <a name="get-a-list-of-files-or-directories"></a>Получение списка файлов или каталогов

    %sh ls <args>

### <a name="create-a-directory"></a>создать каталог;

    %sh mkdir [-p] <paths>

### <a name="delete-a-file-or-a-directory"></a>Удаление файла или каталога

    %sh rm [-skipTrash] URI [URI ...]

Создайте новую записную книжку после запуска кластера Spark в Azure Databricks. Примеры сценария записной книжки приведены ниже.

    #Execute basic HDFS commands invoking the shell. Display the hierarchy.
    %sh ls /
    #Create a sample directory.
    %sh mkdir /samplefolder
    #Get the ACL of the newly created directory.
    hdfs dfs -getfacl /samplefolder

Дополнительные сведения о Databricks CLI можно узнать из [официальной документации](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html). Дополнительные сведения о записных книжках можно узнать из раздела [документации](https://docs.azuredatabricks.net/user-guide/notebooks/index.html).

## <a name="set-file-and-directory-level-permissions"></a>Настройка разрешений на уровне файлов и каталогов

Можно задать и получить разрешения на доступ на уровне файлов и каталогов. Ниже приведено несколько команд для ознакомления. 

Дополнительные сведения о разрешениях на уровне файлов и каталогов Azure Data Lake Storage 2-го поколения см. в статье [Access control in Azure Data Lake Storage Gen2](storage-data-lake-storage-access-control.md) (Контроль доступа в Azure Data Lake Storage 2-го поколения).

### <a name="display-the-access-control-lists-acls-of-files-and-directories"></a>Отображение списков управления доступом (ACL) файлов и каталогов

    hdfs dfs -getfacl [-R] <path>

Пример:

`hdfs dfs -getfacl -R /dir`

См. [getfacl](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#getfacl)

### <a name="set-acls-of-files-and-directories"></a>Настройка ACL файлов и каталогов

    hdfs dfs -setfacl [-R] [-b|-k -m|-x <acl_spec> <path>]|[--set <acl_spec> <path>]

Пример:

`hdfs dfs -setfacl -m user:hadoop:rw- /file`

См. [setfacl](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#setfacl)

### <a name="change-the-owner-of-files"></a>Изменение владельца файлов

    hdfs dfs -chown [-R] <new_owner>:<users_group> <URI>

См. [chown](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#chown)

### <a name="change-group-association-of-files"></a>Изменение связи группы файлов

    hdfs dfs -chgrp [-R] <group> <URI>

См. [chgrp](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#chgrp)

### <a name="change-the-permissions-of-files"></a>Изменение разрешений файлов

    hdfs dfs -chmod [-R] <mode> <URI>

См. [chmod](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#chmod)

Полный список команд можно просмотреть на веб-сайте [Apache Hadoop 2.4.1 File System Guide](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html).

## <a name="next-steps"></a>Дальнейшие действия

[Краткое руководство. Запуск задания Spark в Azure Databricks с помощью портала Azure](./data-lake-storage-quickstart-create-databricks-account.md) 
