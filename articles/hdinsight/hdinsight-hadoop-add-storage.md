---
title: Добавление дополнительных учетных записей хранения Azure в HDInsight
description: Сведения о добавлении дополнительных учетных записей хранения Azure в существующий кластер HDInsight.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: hrasheed
ms.openlocfilehash: 54d7a0bf0474db4a9f9d74a1f694f10ef1be91cc
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59792280"
---
# <a name="add-additional-storage-accounts-to-hdinsight"></a>Добавление дополнительных учетных записей хранения в HDInsight

Узнайте, как добавить дополнительное хранилище Azure с помощью действий сценария *учетные записи* для HDInsight. Действия, описанные в этом документе добавить хранилище *учетной записи* в существующий кластер HDInsight под управлением Linux. Эта статья относится к хранилищу *учетные записи* (не по умолчанию учетной записи хранения кластера) и не дополнительного хранилища, такие как [Gen1 хранилища Озера данных Azure](hdinsight-hadoop-use-data-lake-store.md) и [Gen2 хранилища Озера данных Azure ](hdinsight-hadoop-use-data-lake-storage-gen2.md).

> [!IMPORTANT]  
> В этом документе описано, как добавить дополнительное хранилище в кластер после его создания. Сведения о добавлении учетных записей хранения во время создания кластера см. в статье о [настройке кластеров HDInsight с использованием Apache Hadoop, Apache Spark, Apache Kafka и других платформ](hdinsight-hadoop-provision-linux-clusters.md).

## <a name="prerequisites"></a>Технические условия

* Кластер Hadoop в HDInsight. См. в разделе [начало работы с HDInsight на платформе Linux](./hadoop/apache-hadoop-linux-tutorial-get-started.md).
* Имя учетной записи хранения и ключ. См. в разделе [Управление параметрами учетной записи хранения на портале Azure](../storage/common/storage-account-manage.md).
* [Имя кластера правильно верблюжьим](hdinsight-hadoop-manage-ambari-rest-api.md#identify-correctly-cased-cluster-name).
* Если с помощью PowerShell, вам потребуется модуль AZ.  См. в разделе [Общие сведения об Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview).
* Если вы еще не установили Azure CLI, см. в разделе [интерфейса командной строки Azure (CLI)](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).
* Если используется bash или командной строки windows, вам также потребуется **jq**, обработчик командной строки JSON.  Дополнительные сведения см. на странице [https://stedolan.github.io/jq/](https://stedolan.github.io/jq/). Bash на Ubuntu в Windows 10 см. в разделе [подсистемы Windows для руководство по установке Linux для Windows 10](https://docs.microsoft.com/windows/wsl/install-win10).

## <a name="how-it-works"></a>Принцип работы

Этот скрипт принимает следующие параметры.

* __Имя учетной записи хранения Azure__: имя учетной записи хранения, добавляемой в кластер HDInsight. После выполнения сценария HDInsight сможет считывать и записывать данные, хранящиеся в этой учетной записи хранения.

* __Ключ учетной записи хранения Azure__: ключ, который позволяет получить доступ к учетной записи хранения.

* __-p__ (необязательно): если этот параметр указан, ключ не шифруется и хранится в файле core-site.xml как обычный текст.

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

__Требования__  Скрипт должен применяться к __головным узлам__. Вам не нужно отмечать этот скрипт как __сохраняемый__, так как он непосредственно обновляет конфигурацию Ambari для кластера.

## <a name="to-use-the-script"></a>Использование скрипта

Этот скрипт можно использовать Azure PowerShell, интерфейса командной строки Azure или портала Azure.

### <a name="powershell"></a>PowerShell

С помощью [отправить AzHDInsightScriptAction](https://docs.microsoft.com/powershell/module/az.hdinsight/submit-azhdinsightscriptaction). Замените `CLUSTERNAME`, `ACCOUNTNAME`, и `ACCOUNTKEY` с соответствующими значениями.

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

### <a name="azure-cli"></a>Инфраструктура CLI Azure

С помощью [выполнить действие сценария в hdinsight az](https://docs.microsoft.com/cli/azure/hdinsight/script-action?view=azure-cli-latest#az-hdinsight-script-action-execute).  Замените `CLUSTERNAME`, `RESOURCEGROUP`, `ACCOUNTNAME`, и `ACCOUNTKEY` с соответствующими значениями.

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

См. в разделе [применение действия сценария в работающем кластере](hdinsight-hadoop-customize-cluster-linux.md#apply-a-script-action-to-a-running-cluster).

## <a name="known-issues"></a>Известные проблемы

### <a name="storage-firewall"></a>Брандмауэр хранилища

Если выбран для защиты вашей учетной записи хранения с **брандмауэры и виртуальные сети** ограничения на **выбранные сети**, не забудьте включить исключения **Разрешить надежные Microsoft службы...**  HDInsight можно получить доступ к вашей учетной записи хранения.

### <a name="storage-accounts-not-displayed-in-azure-portal-or-tools"></a>Учетные записи хранения не отображаются на портале Azure или в Azure Tools

При просмотре кластера HDInsight на портале Azure (для этого нужно щелкнуть __Учетные записи хранения__ в разделе __Свойства__) не отображаются учетные записи хранения, добавленные с помощью действия сценария. Azure PowerShell и Azure CLI также не отображают дополнительные учетные записи хранения.

Информация о хранилище не отображается, так как сценарий изменяет только конфигурацию core-site.xml для кластера. Эта информация не используется при получении сведений о кластере с помощью интерфейсов API управления Azure.

Чтобы просмотреть сведения об учетной записи хранения, добавленной в кластер с помощью этого сценария, используйте REST API Ambari. Чтобы получить эти сведения для кластера, выполните следующие команды:

### <a name="powershell"></a>PowerShell

Замените `CLUSTERNAME` именем правильно верблюжьим кластера. Прежде всего определите версию конфигурации службы используется, введя следующую команду:

```powershell
# getting service_config_version
$clusterName = "CLUSTERNAME"

$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName`?fields=Clusters/desired_service_config_versions/HDFS" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.Clusters.desired_service_config_versions.HDFS.service_config_version
```

Замените `ACCOUNTNAME` реальными именами. Затем замените `4` с фактическим config версия обновления и введите команду. При запросе введите пароль для входа кластера.

```powershell
# Update values
$accountName = "ACCOUNTNAME"
$version = 4

$creds = Get-Credential -UserName "admin" -Message "Enter the cluster login credentials"
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=$version" `
    -Credential $creds
$respObj = ConvertFrom-Json $resp.Content
$respObj.items.configurations.properties."fs.azure.account.key.$accountName.blob.core.windows.net"
```

### <a name="bash"></a>bash
Замените `myCluster` именем правильно верблюжьим кластера.

```bash
export CLUSTERNAME='myCluster'

curl --silent -u admin -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME?fields=Clusters/desired_service_config_versions/HDFS" \
| jq ".Clusters.desired_service_config_versions.HDFS[].service_config_version" 
```

Замените `myAccount` на имя учетной записи хранения. Затем замените `4` с фактическим config версия обновления и введите команду:

```bash
export ACCOUNTNAME='"fs.azure.account.key.myAccount.blob.core.windows.net"'
export VERSION='4'

curl --silent -u admin -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=$VERSION" \
| jq ".items[].configurations[].properties[$ACCOUNTNAME] | select(. != null)"
```

### <a name="cmd"></a>cmd.

Замените `CLUSTERNAME` именем правильно верблюжьим кластера в обоих сценариях. Прежде всего определите версию конфигурации службы используется, введя следующую команду:

```cmd
curl --silent -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME?fields=Clusters/desired_service_config_versions/HDFS" | ^
jq-win64 ".Clusters.desired_service_config_versions.HDFS[].service_config_version" 
```

Замените `ACCOUNTNAME` на имя учетной записи хранения. Затем замените `4` с фактическим config версия обновления и введите команду:

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

При повторном запуске действия сценария ключ __не__ обновляется, так как сценарий проверяет наличие записи для учетной записи хранения. Если запись уже существует, он не вносит какие-либо изменения.

Чтобы решить эту проблему, необходимо удалить существующую запись для учетной записи хранения. Чтобы удалить имеющуюся запись, выполните указанные ниже действия.

1. Откройте в браузере веб-интерфейс Ambari для кластера HDInsight. Значение URI — `https://CLUSTERNAME.azurehdinsight.net`. Замените `CLUSTERNAME` именем кластера.

    При появлении запроса введите имя пользователя и пароль для входа в кластер по протоколу HTTP.

2. В списке служб в левой части страницы выберите __HDFS__. В центральной области откройте вкладку __Конфигурации__.

3. В поле __Фильтр__ введите значение __fs.azure.account__. Будут возвращены записи для всех дополнительных учетных записей хранения, добавленных в кластер. Есть два типа записей: __keyprovider__ и __key__. Оба содержат имя учетной записи хранения в имени ключа.

    Ниже представлены примеры записей для учетной записи хранения с именем __mystorage__:

        fs.azure.account.keyprovider.mystorage.blob.core.windows.net
        fs.azure.account.key.mystorage.blob.core.windows.net

4. Определив ключи для учетной записи хранения, используйте красный значок с минусом (-) справа от записи, чтобы удалить ее. Нажмите кнопку __Сохранить__, чтобы сохранить изменения.

5. Сохранив изменения, используйте действие скрипта, чтобы добавить учетную запись хранения и новое значение ключа кластера.

### <a name="poor-performance"></a>Низкая производительность

Если учетная запись хранения и кластер HDInsight расположены в разных регионах, это может негативно повлиять на производительность. Для доступа к данным в другом регионе сетевой трафик отправляется за пределы центра обработки данных Azure через общедоступный сегмент Интернета, что может приводить к задержкам.

### <a name="additional-charges"></a>Дополнительные расходы

Если учетная запись хранения и кластер HDInsight расположены в разных регионах, в ваш счет за использование Azure будет включена дополнительная плата за исходящий трафик. Когда данные покидают центр обработки данных, исходящий трафик тарифицируется. Эта плата взимается, даже если трафик предназначается для другого центра обработки данных Azure в другом регионе.

## <a name="next-steps"></a>Дальнейшие действия

Вы узнали, как добавлять дополнительные учетные записи хранения Azure в существующий кластер HDInsight. Дополнительные сведения о действиях скриптов см. в статье [Настройка кластеров HDInsight под управлением Linux с помощью действия сценария](hdinsight-hadoop-customize-cluster-linux.md).
