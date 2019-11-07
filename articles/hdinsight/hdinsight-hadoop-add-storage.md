---
title: Добавление дополнительных учетных записей хранения Azure в HDInsight
description: Сведения о добавлении дополнительных учетных записей хранения Azure в существующий кластер HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/31/2019
ms.openlocfilehash: e29041942157e720cce3414f7b6e6904667c1894
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73665477"
---
# <a name="add-additional-storage-accounts-to-hdinsight"></a>Добавление дополнительных учетных записей хранения в HDInsight

Узнайте, как использовать действия сценария для добавления дополнительных *учетных записей* хранения Azure в HDInsight. Действия, описанные в этом документе, позволяют добавить *учетную запись* хранения в существующий кластер HDInsight под управлением Linux. Эта статья относится к *учетным записям* хранения (а не к учетной записи хранения кластера по умолчанию), а также к дополнительным хранилищам, таким как [Azure Data Lake Storage 1-го поколения](hdinsight-hadoop-use-data-lake-store.md) и [Azure Data Lake Storage 2-го поколения](hdinsight-hadoop-use-data-lake-storage-gen2.md).

> [!IMPORTANT]  
> Сведения в этом документе посвящены добавлению дополнительных учетных записей хранения в кластер после его создания. Сведения о добавлении учетных записей хранения во время создания кластера см. в статье о [настройке кластеров HDInsight с использованием Apache Hadoop, Apache Spark, Apache Kafka и других платформ](hdinsight-hadoop-provision-linux-clusters.md).

## <a name="prerequisites"></a>Предварительные требования

* Кластер Hadoop в HDInsight. Ознакомьтесь со статьей [Краткое руководство. Использование Apache Hadoop и Apache Hive в Azure HDInsight с шаблоном Resource Manager](./hadoop/apache-hadoop-linux-tutorial-get-started.md).
* Имя и ключ учетной записи хранения. См. раздел [Управление параметрами учетной записи хранения в портал Azure](../storage/common/storage-account-manage.md).
* [Правильное имя кластера с учетом регистра](hdinsight-hadoop-manage-ambari-rest-api.md#identify-correctly-cased-cluster-name).
* При использовании PowerShell вам потребуется модуль AZ.  См. [обзор Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview).
* Если вы еще не установили Azure CLI, см. раздел [интерфейс командной строки Azure (CLI)](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).
* Если вы используете bash или командную строку Windows, вам также потребуется **JQ**, обработчик JSON командной строки.  Дополнительные сведения см. на странице [https://stedolan.github.io/jq/](https://stedolan.github.io/jq/). Для bash в системе Ubuntu в Windows 10 см. раздел [подсистема Windows для установки Linux для Windows 10](https://docs.microsoft.com/windows/wsl/install-win10).

## <a name="how-it-works"></a>Принцип работы

Этот скрипт принимает следующие параметры.

* __Имя учетной записи хранения Azure__: имя учетной записи хранения, добавляемой в кластер HDInsight. После выполнения сценария HDInsight сможет считывать и записывать данные, хранящиеся в этой учетной записи хранения.

* __Ключ учетной записи хранения Azure__: ключ, который предоставляет доступ к учетной записи хранения.

* __-p__ (необязательно). Если параметр указан, ключ не шифруется и хранится в файле Core-site. XML в виде обычного текста.

При обработке скрипт выполняет следующие действия.

* Если учетная запись хранения уже существует в конфигурации core-site.xml для кластера, скрипт завершает работу и дальнейшие действия не выполняются.

* Проверяет существование учетной записи хранения и ее доступность с помощью ключа.

* Шифрует ключ с использованием учетных данных кластера.

* Добавляет учетную запись хранилища в файл core-site.xml.

* Останавливает и перезапускает службы [Apache Oozie](https://oozie.apache.org/), [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html), [Apache Hadoop MapReduce2](https://hadoop.apache.org/docs/current/hadoop-mapreduce-client/hadoop-mapreduce-client-core/MapReduceTutorial.html) и [Apache Hadoop HDFS](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsUserGuide.html). Остановка и запуск этих служб позволяет им использовать новую учетную запись хранения.

> [!WARNING]  
> Использование учетной записи хранения, расположение которой отличается от расположения кластера HDInsight, не поддерживается.

## <a name="the-script"></a>Сценарий

__Расположение скрипта__: [https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh](https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh)

__Требования__. сценарий должен быть применен к __головным узлам__. Вам не нужно помечать этот скрипт как __сохраняемый__, так как он непосредственно обновляет конфигурацию Ambari для кластера.

## <a name="to-use-the-script"></a>Использование скрипта

Этот скрипт можно использовать из Azure PowerShell, Azure CLI или портал Azure.

### <a name="powershell"></a>PowerShell

С помощью команды [Submit-аздинсигхтскриптактион](https://docs.microsoft.com/powershell/module/az.hdinsight/submit-azhdinsightscriptaction). Замените `CLUSTERNAME`, `ACCOUNTNAME`и `ACCOUNTKEY` соответствующими значениями.

```powershell
# Update these parameters
$clusterName = "CLUSTERNAME"
$parameters = "ACCOUNTNAME ACCOUNTKEY"

$scriptActionName = "addStorage"
$scriptActionUri = "https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh"

# Execute script
Submit-AzHDInsightScriptAction `
    -ClusterName $clusterName `
    -Name $scriptActionName `
    -Uri $scriptActionUri `
    -NodeTypes "headnode" `
    -Parameters $parameters
```

### <a name="azure-cli"></a>Azure CLI

С помощью команды [AZ hdinsight Script — действие Execute](https://docs.microsoft.com/cli/azure/hdinsight/script-action?view=azure-cli-latest#az-hdinsight-script-action-execute).  Замените `CLUSTERNAME`, `RESOURCEGROUP`, `ACCOUNTNAME`и `ACCOUNTKEY` соответствующими значениями.

```cli
az hdinsight script-action execute ^
    --name CLUSTERNAME ^
    --resource-group RESOURCEGROUP ^
    --roles headnode ^
    --script-action-name addStorage ^
    --script-uri "https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh" ^
    --script-parameters "ACCOUNTNAME ACCOUNTKEY"
```

### <a name="azure-portal"></a>Портал Azure

См. раздел [применение действия сценария в работающем кластере](hdinsight-hadoop-customize-cluster-linux.md#apply-a-script-action-to-a-running-cluster).

## <a name="known-issues"></a>Известные проблемы

### <a name="storage-firewall"></a>Брандмауэр хранилища

Если вы решили защитить учетную запись хранения с ограничениями **брандмауэра и виртуальной сети** в **выбранных сетях**, обязательно включите исключение **Разрешить доверенные службы Майкрософт...** , чтобы HDInsight могла получить доступ к хранилищу. учетной записи.

### <a name="storage-accounts-not-displayed-in-azure-portal-or-tools"></a>Учетные записи хранения не отображаются на портале Azure или в Azure Tools

При просмотре кластера HDInsight в портал Azure при выборе элемента " __учетные записи хранения__ " в разделе " __свойства__ " не отображаются учетные записи хранения, добавленные с помощью этого действия скрипта. Azure PowerShell и Azure CLI не отображают дополнительную учетную запись хранения.

Информация о хранилище не отображается, так как сценарий изменяет только конфигурацию core-site.xml для кластера. Эта информация не используется при получении сведений о кластере с помощью API-интерфейсов управления Azure.

Чтобы просмотреть сведения об учетной записи хранения, добавленной в кластер с помощью этого сценария, используйте REST API Ambari. Чтобы получить эти сведения для кластера, выполните следующие команды:

### <a name="powershell"></a>PowerShell

Замените `CLUSTERNAME` именем кластера, соответствующим регистру. Замените `ACCOUNTNAME` фактическими именами. При появлении запроса введите пароль для входа в кластер.

```powershell
# Update values
$clusterName = "CLUSTERNAME"
$accountName = "ACCOUNTNAME"

$creds = Get-Credential -UserName "admin" -Message "Enter the cluster login credentials"

# getting service_config_version
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName`?fields=Clusters/desired_service_config_versions/HDFS" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content

$configVersion=$respObj.Clusters.desired_service_config_versions.HDFS.service_config_version

$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=$configVersion" `
    -Credential $creds
$respObj = ConvertFrom-Json $resp.Content
$respObj.items.configurations.properties."fs.azure.account.key.$accountName.blob.core.windows.net"
```

### <a name="bash"></a>bash

Замените `CLUSTERNAME` именем кластера, соответствующим регистру. Замените `PASSWORD` паролем администратора кластера. Замените `STORAGEACCOUNT` фактическим именем учетной записи хранения.

```bash
export clusterName="CLUSTERNAME"
export password='PASSWORD'
export storageAccount="STORAGEACCOUNT"

export ACCOUNTNAME='"'fs.azure.account.key.$storageAccount.blob.core.windows.net'"'

export configVersion=$(curl --silent -u admin:$password -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName?fields=Clusters/desired_service_config_versions/HDFS" \
| jq ".Clusters.desired_service_config_versions.HDFS[].service_config_version")

curl --silent -u admin:$password -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=$configVersion" \
| jq ".items[].configurations[].properties[$ACCOUNTNAME] | select(. != null)"
```

### <a name="cmd"></a>cmd.

Замените `CLUSTERNAME` правильным именем кластера с учетом регистра в обоих скриптах. Сначала укажите используемую версию конфигурации службы, введя следующую команду:

```cmd
curl --silent -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME?fields=Clusters/desired_service_config_versions/HDFS" | ^
jq-win64 ".Clusters.desired_service_config_versions.HDFS[].service_config_version"
```

Замените `ACCOUNTNAME` фактическим именем учетной записи хранения. Затем замените `4` на фактическую версию конфигурации службы и введите команду:

```cmd
curl --silent -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=4" | ^
jq-win64 ".items[].configurations[].properties["""fs.azure.account.key.ACCOUNTNAME.blob.core.windows.net"""] | select(. != null)"
```

---

Выходные данные этой команды выглядят так:

    "MIIB+gYJKoZIhvcNAQcDoIIB6zCCAecCAQAxggFaMIIBVgIBADA+MCoxKDAmBgNVBAMTH2RiZW5jcnlwdGlvbi5henVyZWhkaW5zaWdodC5uZXQCEA6GDZMW1oiESKFHFOOEgjcwDQYJKoZIhvcNAQEBBQAEggEATIuO8MJ45KEQAYBQld7WaRkJOWqaCLwFub9zNpscrquA2f3o0emy9Vr6vu5cD3GTt7PmaAF0pvssbKVMf/Z8yRpHmeezSco2y7e9Qd7xJKRLYtRHm80fsjiBHSW9CYkQwxHaOqdR7DBhZyhnj+DHhODsIO2FGM8MxWk4fgBRVO6CZ5eTmZ6KVR8wYbFLi8YZXb7GkUEeSn2PsjrKGiQjtpXw1RAyanCagr5vlg8CicZg1HuhCHWf/RYFWM3EBbVz+uFZPR3BqTgbvBhWYXRJaISwssvxotppe0ikevnEgaBYrflB2P+PVrwPTZ7f36HQcn4ifY1WRJQ4qRaUxdYEfzCBgwYJKoZIhvcNAQcBMBQGCCqGSIb3DQMHBAhRdscgRV3wmYBg3j/T1aEnO3wLWCRpgZa16MWqmfQPuansKHjLwbZjTpeirqUAQpZVyXdK/w4gKlK+t1heNsNo1Wwqu+Y47bSAX1k9Ud7+Ed2oETDI7724IJ213YeGxvu4Ngcf2eHW+FRK"

Это — пример ключа шифрования, который используется для доступа к учетной записи хранения.

### <a name="unable-to-access-storage-after-changing-key"></a>Не удается получить доступ к хранилищу после изменения ключа

Если вы измените ключ для учетной записи хранения, HDInsight больше не сможет обращаться к этой учетной записи. HDInsight использует кэшированную копию ключа в core-site.xml для кластера. Эту копию нужно обновить в соответствии с новым ключом.

При повторном запуске действия сценария ключ __не__ обновляется, так как сценарий проверяет наличие записи для учетной записи хранения. Если запись уже существует, изменения не вносятся.

Чтобы решить эту проблему, необходимо удалить существующую запись для учетной записи хранения. Чтобы удалить имеющуюся запись, выполните указанные ниже действия.

> [!IMPORTANT]  
> Смена ключа хранилища для основной учетной записи хранения, подключенной к кластеру, не поддерживается.

1. Откройте в браузере веб-интерфейс Ambari для кластера HDInsight. Значение URI — `https://CLUSTERNAME.azurehdinsight.net`. Замените `CLUSTERNAME` именем кластера.

    При появлении запроса введите имя пользователя и пароль для входа в кластер по протоколу HTTP.

2. В списке служб в левой части страницы выберите __HDFS__. В центральной области откройте вкладку __Конфигурации__.

3. В поле __Фильтр__ введите значение __fs.azure.account__. Будут возвращены записи для всех дополнительных учетных записей хранения, добавленных в кластер. Есть два типа записей: __keyprovider__ и __key__. Оба содержат имя учетной записи хранения в имени ключа.

    Ниже представлены примеры записей для учетной записи хранения с именем __mystorage__:

        fs.azure.account.keyprovider.mystorage.blob.core.windows.net
        fs.azure.account.key.mystorage.blob.core.windows.net

4. Определив ключи для учетной записи хранения, которую необходимо удалить, используйте красный значок "-" справа от записи, чтобы удалить его. Нажмите кнопку __Сохранить__, чтобы сохранить изменения.

5. Сохранив изменения, используйте действие скрипта, чтобы добавить учетную запись хранения и новое значение ключа кластера.

### <a name="poor-performance"></a>Низкая производительность

Если учетная запись хранения и кластер HDInsight расположены в разных регионах, это может негативно повлиять на производительность. Для доступа к данным в другом регионе сетевой трафик отправляется за пределы центра обработки данных Azure через общедоступный сегмент Интернета, что может приводить к задержкам.

### <a name="additional-charges"></a>Дополнительные расходы

Если учетная запись хранения и кластер HDInsight расположены в разных регионах, в ваш счет за использование Azure будет включена дополнительная плата за исходящий трафик. Когда данные покидают центр обработки данных, исходящий трафик тарифицируется. Эта плата взимается, даже если трафик предназначается для другого центра обработки данных Azure в другом регионе.

## <a name="next-steps"></a>Дальнейшие действия

Вы узнали, как добавлять дополнительные учетные записи хранения в существующий кластер HDInsight. Дополнительные сведения о действиях скриптов см. в статье [Настройка кластеров HDInsight под управлением Linux с помощью действия сценария](hdinsight-hadoop-customize-cluster-linux.md).
