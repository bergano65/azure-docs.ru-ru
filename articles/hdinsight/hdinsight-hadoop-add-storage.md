---
title: Добавление дополнительных учетных записей хранения Azure в HDInsight
description: Узнайте, как добавить дополнительные учетные записи хранения Azure в существующий кластер HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/21/2020
ms.openlocfilehash: 87eb04b7323186175195babf6a602fa12d25176f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78206713"
---
# <a name="add-additional-storage-accounts-to-hdinsight"></a>Добавление дополнительных учетных записей хранения в HDInsight

Узнайте, как использовать действия скриптов для добавления дополнительных *учетных записей* хранилища Azure в HDInsight. Шаги в этом документе добавляют *учетную запись* хранилища в существующий кластер HDInsight. Эта статья относится к *учетным записям* хранения данных (не учетной записи кластера по умолчанию), а не к дополнительным хранилищам, таким как [Azure Data Lake Storage Gen1](hdinsight-hadoop-use-data-lake-store.md) и [Azure Data Lake Storage Gen2.](hdinsight-hadoop-use-data-lake-storage-gen2.md)

> [!IMPORTANT]  
> Информация в этом документе о добавлении дополнительных учетных записей хранения (ы) в кластер после его создания. Сведения о добавлении учетных записей хранения во время создания кластера см. в статье о [настройке кластеров HDInsight с использованием Apache Hadoop, Apache Spark, Apache Kafka и других платформ](hdinsight-hadoop-provision-linux-clusters.md).

## <a name="prerequisites"></a>Предварительные требования

* Кластер Хадуп на HDInsight. Ознакомьтесь со статьей [Краткое руководство. Использование Apache Hadoop и Apache Hive в Azure HDInsight с шаблоном Resource Manager](./hadoop/apache-hadoop-linux-tutorial-get-started.md).
* Имя учетной записи и ключ для хранения. Смотрите [ключи доступа к учетной записи хранилища.](../storage/common/storage-account-keys-manage.md)
* При использовании PowerShell вам понадобится модуль , который будет использоваться в случае использования.  Смотрите [обзор Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview).

## <a name="how-it-works"></a>Принцип работы

При обработке скрипт выполняет следующие действия.

* Если учетная запись хранения уже существует в конфигурации core-site.xml для кластера, скрипт завершает работу и дальнейшие действия не выполняются.

* Проверяет существование учетной записи хранения и ее доступность с помощью ключа.

* Шифрует ключ с использованием учетных данных кластера.

* Добавляет учетную запись хранилища в файл core-site.xml.

* Останавливает и перезапускает службы [Apache Oozie](https://oozie.apache.org/), [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html), [Apache Hadoop MapReduce2](https://hadoop.apache.org/docs/current/hadoop-mapreduce-client/hadoop-mapreduce-client-core/MapReduceTutorial.html) и [Apache Hadoop HDFS](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsUserGuide.html). Остановка и запуск этих служб позволяет им использовать новую учетную запись хранения.

> [!WARNING]  
> Использование учетной записи хранения, расположение которой отличается от расположения кластера HDInsight, не поддерживается.

## <a name="add-storage-account"></a>Добавление учетной записи хранения

Используйте [действие скрипта](hdinsight-hadoop-customize-cluster-linux.md#script-action-to-a-running-cluster) для применения изменений со следующими соображениями:

|Свойство | Значение |
|---|---|
|URI bash-скрипта|`https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh`|
|Типы узлов|Head|
|Параметры|`ACCOUNTNAME``ACCOUNTKEY` `-p` (необязательно)|

* `ACCOUNTNAME`— это название учетной записи хранилища для добавления в кластер HDInsight.
* `ACCOUNTKEY`является ключом `ACCOUNTNAME`доступа для .
* Аргумент `-p` является необязательным. Если указано, ключ не зашифрован и хранится в файле core-site.xml в виде обычного текста.

## <a name="verification"></a>Проверка

При просмотре кластера HDInsight на портале Azure выбор __записи учетных записей хранилища__ под __свойствами__ не отображает учетные записи хранения, добавленные через это действие скрипта. Azure PowerShell и Azure CLI также не отображают дополнительную учетную запись хранения. Информация о хранении не отображается, поскольку скрипт только изменяет конфигурацию `core-site.xml` для кластера. Эта информация не используется при извлечении информации о кластере с помощью APIs управления Azure.

Для проверки дополнительного хранилища используйте один из приведенных ниже методов:

### <a name="powershell"></a>PowerShell

Скрипт вернет имя учетной записи хранилища (ы), связанное с данным кластером. Заменить `CLUSTERNAME` фактическое имя кластера, а затем запустить сценарий.

```powershell
# Update values
$clusterName = "CLUSTERNAME"

$creds = Get-Credential -UserName "admin" -Message "Enter the cluster login credentials"

$clusterName = $clusterName.ToLower();

# getting service_config_version
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName`?fields=Clusters/desired_service_config_versions/HDFS" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content

$configVersion=$respObj.Clusters.desired_service_config_versions.HDFS.service_config_version

$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=$configVersion" `
    -Credential $creds
$respObj = ConvertFrom-Json $resp.Content

# extract account names
$value = ($respObj.items.configurations | Where type -EQ "core-site").properties | Get-Member -membertype properties | Where Name -Like "fs.azure.account.key.*"
foreach ($name in $value ) { $name.Name.Split(".")[4]}
```

### <a name="apache-ambari"></a>Apache Ambari

1. Из веб-браузера, `https://CLUSTERNAME.azurehdinsight.net`перейдите к , где `CLUSTERNAME` имя вашего кластера.

1. Перейдите на **HDFS** > **Configs** > **Advanced** > Custom**core-site.**

1. Наблюдайте за `fs.azure.account.key`ключами, которые начинаются с . Имя учетной записи будет частью ключа, как видно на этом изображении образца:

   ![проверка через Apache Ambari](./media/hdinsight-hadoop-add-storage/apache-ambari-verification.png)

## <a name="remove-storage-account"></a>Удаление учетной записи хранения

1. Из веб-браузера, `https://CLUSTERNAME.azurehdinsight.net`перейдите к , где `CLUSTERNAME` имя вашего кластера.

1. Перейдите на **HDFS** > **Configs** > **Advanced** > Custom**core-site.**

1. Удалите следующие клавиши:
    * `fs.azure.account.key.<STORAGE_ACCOUNT_NAME>.blob.core.windows.net`
    * `fs.azure.account.keyprovider.<STORAGE_ACCOUNT_NAME>.blob.core.windows.net`

После удаления этих ключей и сохранения конфигурации необходимо перезапустить Oozie, Yarn, MapReduce2, HDFS и Hive по одному.

## <a name="known-issues"></a>Известные проблемы

### <a name="storage-firewall"></a>Брандмауэр для хранения

Если вы решите обезопасить свою учетную запись хранения с помощью **брандмауэров и** ограничений виртуальных сетей в **выбранных сетях,** не забудьте включить исключение **Разрешить доверенные службы Microsoft ...,** так что HDInsight может получить доступ к вашей учетной записи хранения.

### <a name="unable-to-access-storage-after-changing-key"></a>Не удается получить доступ к хранилищу после изменения ключа

Если вы измените ключ для учетной записи хранения, HDInsight больше не сможет обращаться к этой учетной записи. HDInsight использует кэшированную копию ключа в core-site.xml для кластера. Эту копию нужно обновить в соответствии с новым ключом.

При повторном запуске действия сценария ключ __не__ обновляется, так как сценарий проверяет наличие записи для учетной записи хранения. Если запись уже существует, она не внося никаких изменений.

Чтобы обойти эту проблему:  
1. Удалите учетную запись хранилища.
1. Добавьте учетную запись хранилища.

> [!IMPORTANT]  
> Вращение ключа хранилища для основной учетной записи хранилища, прикрепленного к кластеру, не поддерживается.

### <a name="poor-performance"></a>Низкая производительность

Если учетная запись хранения и кластер HDInsight расположены в разных регионах, это может негативно повлиять на производительность. Для доступа к данным в другом регионе сетевой трафик отправляется за пределы центра обработки данных Azure через общедоступный сегмент Интернета, что может приводить к задержкам.

### <a name="additional-charges"></a>Дополнительные расходы

Если учетная запись хранения и кластер HDInsight расположены в разных регионах, в ваш счет за использование Azure будет включена дополнительная плата за исходящий трафик. Когда данные покидают центр обработки данных, исходящий трафик тарифицируется. Эта плата взимается, даже если трафик предназначается для другого центра обработки данных Azure в другом регионе.

## <a name="next-steps"></a>Дальнейшие действия

Вы узнали, как добавить дополнительные учетные записи хранения в существующий кластер HDInsight. Дополнительные сведения о действиях скриптов см. в статье [Настройка кластеров HDInsight под управлением Linux с помощью действия сценария](hdinsight-hadoop-customize-cluster-linux.md).
