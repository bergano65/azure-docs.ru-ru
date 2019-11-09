---
title: Использование Azure Data Lake Storage 1-го поколения с Hadoop в Azure HDInsight
description: Сведения о запросе данных из Azure Data Lake Storage 1-го поколения и сохранении результатов анализа.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 309af904f0dbfc0664c4341803cb6a4dc8a2c8a4
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73839282"
---
# <a name="use-data-lake-storage-gen1-with-azure-hdinsight-clusters"></a>Использование Data Lake Storage 1-го поколения с кластерами Azure HDInsight

> [!Note]
> Развертывайте новые кластеры HDInsight с помощью [Azure Data Lake Storage 2-го поколения](hdinsight-hadoop-use-data-lake-storage-gen2.md), чтобы повысить их производительность и получить дополнительные функции.

Для анализа данных в кластере HDInsight данные можно хранить в [службе хранилища Azure](../storage/common/storage-introduction.md), [Azure Data Lake Storage Gen 1](../data-lake-store/data-lake-store-overview.md)или [Azure Data Lake Storage Gen 2](../storage/blobs/data-lake-storage-introduction.md). Все варианты хранилищ позволяют безопасно и без потери пользовательских данных удалять используемые для расчетов кластеры HDInsight.

Из этой статьи вы узнаете, как Data Lake Storage 1-го поколения работает с кластерами HDInsight. Дополнительные сведения о работе службы хранилища Azure с кластерами HDInsight см. в разделе [Использование службы хранилища Azure с кластерами Azure HDInsight](hdinsight-hadoop-use-blob-storage.md). Дополнительные сведения о создании кластера HDInsight см. в статье [Установка кластеров в HDInsight с использованием Hadoop, Spark, Kafka и других технологий](hdinsight-hadoop-provision-linux-clusters.md).

> [!NOTE]  
> Доступ к Data Lake Storage 1-го поколения всегда осуществляется по безопасному каналу, поэтому никогда не применяется имя схемы файловой системы `adls`. Всегда используется `adl`.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="availability-for-hdinsight-clusters"></a>Сведения о доступности кластеров HDInsight

В Apache Hadoop поддерживается концепция файловой системы по умолчанию. Файловая система по умолчанию подразумевает использование центра сертификации и схемы по умолчанию. Она также может использоваться для разрешения относительных путей. При создании кластера HDInsight в качестве используемой по умолчанию файловой системы можно указать контейнер больших двоичных объектов в службе хранилища Azure, а в случае использования HDInsight 3.5 и более поздних версий — выбрать службу хранилища Azure или Azure Data Lake Storage 1-го поколения с некоторыми исключениями.

Есть два способа использовать Data Lake Storage 1-го поколения с кластерами HDInsight:

* в качестве хранилища по умолчанию;
* в качестве дополнительного хранилища (при этом в качестве хранилища по умолчанию используется Azure Storage Blob).

Сейчас только некоторые типы и версии кластеров HDInsight поддерживают использование Data Lake Storage 1-го поколения в качестве хранилища по умолчанию и дополнительных учетных записей хранения.

| Тип кластера HDInsight | Использование Data Lake Storage 1-го поколения как хранилища по умолчанию | Использование Data Lake Storage 1-го поколения как дополнительного хранилища| Примечания |
|------------------------|------------------------------------|---------------------------------------|------|
| HDInsight версии 4,0 | Нет | Нет |ADLS 1-го поколения не поддерживается в HDInsight 4,0 |
| HDInsight версии 3.6 | Да | Да | За исключением HBase|
| HDInsight версии 3.5 | Да | Да | За исключением HBase|
| HDInsight версия 3.4 | Нет | Да | |
| HDInsight версии 3.3 | Нет | Нет | |
| HDInsight версии 3.2 | Нет | Да | |
| Storm | | |Data Lake Storage 1-го поколения можно использовать для записи данных из топологии Storm. Data Lake Storage также может использоваться для хранения эталонных данных, которые затем можно будет считать с помощью топологии Storm.|

> [!WARNING]  
> HDInsight HBase не поддерживается в Azure Data Lake Storage 1-го поколения.

Использование Data Lake Storage 1-го поколения в качестве дополнительной учетной записи хранения не влияет на производительность или возможность чтения или записи в хранилище Azure из кластера.

## <a name="use-data-lake-storage-gen1-as-default-storage"></a>Использование Data Lake Storage 1-го поколения как хранилища по умолчанию

Если для кластера HDInsight в качестве хранилища по умолчанию развернуто Data Lake Storage 1-го поколения, все файлы этого кластера хранятся в расположении `adl://mydatalakestore/<cluster_root_path>/`, где `<cluster_root_path>` обозначает имя папки, созданной в Data Lake Storage. Указав корневую папку для каждого кластера, вы можете использовать одну и ту же учетную запись Data Lake Storage для нескольких кластеров. Таким образом, у вас могут получиться такие настройки:

* Cluster1 использует путь `adl://mydatalakestore/cluster1storage`;
* Cluster2 использует путь `adl://mydatalakestore/cluster2storage`.

Обратите внимание, что оба кластера используют одну учетную запись Data Lake Storage 1-го поколения с именем **mydatalakestore**. Каждый кластер имеет доступ к собственный корневой файловой системе в Data Lake Storage. В частности, при развертывании на портале Azure в качестве корневого пути предлагается использовать имя папки, например **/clusters/\<clustername>** .

Чтобы использовать Data Lake Storage 1-го поколения как хранилище по умолчанию, необходимо предоставить субъекту-службе доступ к таким объектам:

* Корневой каталог учетной записи Data Lake Storage 1-го поколения.  Например: adl://mydatalakestore/.
* Папка для всех папок кластера.  Например: adl://mydatalakestore/clusters.
* Папка для кластера.  Например: adl://mydatalakestore/clusters/cluster1storage.

Дополнительные сведения о создании субъекта-службы и предоставлении доступа см. в разделе "Настройка доступа к Data Lake Storage".

### <a name="extracting-a-certificate-from-azure-keyvault-for-use-in-cluster-creation"></a>Извлечение сертификата из Azure Key Vault для использования при создании кластера

Если вы хотите настроить Azure Data Lake Storage 1-го поколения в качестве хранилища по умолчанию для нового кластера, а сертификат для субъекта-службы хранится в Azure Key Vault, необходимо выполнить несколько дополнительных действий по преобразованию сертификата в правильный формат. В приведенном ниже фрагменте кода показано, как выполнить преобразование.

Прежде всего, загрузите сертификат из Key Vault и извлеките `SecretValueText`.

```powershell
$certPassword = Read-Host "Enter Certificate Password"
$cert = (Get-AzureKeyVaultSecret -VaultName 'MY-KEY-VAULT' -Name 'MY-SECRET-NAME')
$certValue = [System.Convert]::FromBase64String($cert.SecretValueText)
```

Затем преобразуйте `SecretValueText` в сертификат.

```powershell
$certObject = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList $certValue,$null,"Exportable, PersistKeySet"
$certBytes = $certObject.Export([System.Security.Cryptography.X509Certificates.X509ContentType]::Pkcs12, $certPassword.SecretValueText);
$identityCertificate = [System.Convert]::ToBase64String($certBytes)
```

После этого можно используйте `$identityCertificate` для развертывания нового кластера, как показано в следующем фрагменте.

```powershell
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $pathToArmTemplate `
    -identityCertificate $identityCertificate `
    -identityCertificatePassword $certPassword.SecretValueText `
    -clusterName  $clusterName `
    -clusterLoginPassword $SSHpassword `
    -sshPassword $SSHpassword `
    -servicePrincipalApplicationId $application.ApplicationId
```

## <a name="use-data-lake-storage-gen1-as-additional-storage"></a>Использование Data Lake Storage 1-го поколения как дополнительного хранилища

Data Lake Storage 1-го поколения можно использовать в качестве дополнительного хранилища кластера. В таких случаях хранилищем кластера по умолчанию может быть Azure Storage Blob или учетная запись Data Lake Storage. Если вы запускаете задания HDInsight для данных, хранящихся в Data Lake Storage в качестве дополнительного хранилища, необходимо использовать полный путь к файлам. Например:

    adl://mydatalakestore.azuredatalakestore.net/<file_path>

Обратите внимание, что в этом URL-адресе нет **cluster_root_path**. Это связано с тем, что Data Lake Storage не является хранилищем по умолчанию в данном случае, поэтому достаточно указать путь к файлам.

Чтобы использовать Data Lake Storage 1-го поколения как дополнительное хранилище, достаточно предоставить субъекту-службе доступ к расположениям, в которых хранятся файлы.  Например:

    adl://mydatalakestore.azuredatalakestore.net/<file_path>

Дополнительные сведения о создании субъекта-службы и предоставлении доступа см. в разделе "Настройка доступа к Data Lake Storage".

## <a name="use-more-than-one-data-lake-storage-accounts"></a>Использование нескольких учетных записей Data Lake Storage

Можно добавить учетную запись Data Lake Storage в качестве дополнительного хранилища и несколько учетных записей Data Lake Storage, предоставив кластеру HDInsight разрешение на доступ к данным в одной учетной записи Data Lake Storage или нескольких. Ознакомьтесь с разделом "Настройка доступа к Data Lake Storage".

## <a name="configure-data-lake-storage-access"></a>Настройка доступа к Data Lake Storage

Чтобы настроить доступ к Data Lake Storage из кластера HDInsight, требуется субъект-служба Azure Active Directory (AAD). Создать субъект-службу может только администратор Azure AD. Для создания субъекта-службы необходим сертификат. Дополнительные сведения см. в разделах [Краткое руководство. Настройка кластеров в HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md) и [Создание субъекта-службы с самозаверяющим сертификатом](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-self-signed-certificate).

> [!NOTE]  
> Если вы собираетесь добавить Azure Data Lake Storage 1-го поколения в качестве дополнительного хранилища для кластера HDInsight, мы настоятельно рекомендуем сделать это во время создания кластера, как описано в этой статье. Сценарий с добавлением Azure Data Lake Storage 1-го поколения в качестве дополнительного хранилища для существующего кластера HDInsight не поддерживается.

## <a name="access-files-from-the-cluster"></a>Доступ к файлам из кластера

Существует несколько способов доступа к файлам в Data Lake Storage из кластера HDInsight.

* **С помощью полного доменного имени**. При таком подходе необходимо указать полный путь к файлу, к которому требуется доступ.

    ```
    adl://<data_lake_account>.azuredatalakestore.net/<cluster_root_path>/<file_path>
    ```

* **Используя сокращенный формат пути**. При таком подходе путь к корню кластера заменяется на:

    ```
    adl:///<file path>
    ```

* **С помощью относительного пути**. При таком подходе указывается только относительный путь к файлу, к которому требуется доступ.

    ```
    /<file.path>/
    ```

### <a name="data-access-examples"></a>Примеры доступа к данным

Примеры основаны на [SSH-подключении](./hdinsight-hadoop-linux-use-ssh-unix.md) к головному узлу кластера. В примерах используются все три схемы URI. Замените `DATALAKEACCOUNT` и `CLUSTERNAME` соответствующими значениями.

#### <a name="a-few-hdfs-commands"></a>Несколько команд HDFS

1. Создание простого файла в локальном хранилище.

    ```bash
    touch testFile.txt
    ```

1. Создание каталогов в хранилище кластера.

    ```bash
    hdfs dfs -mkdir adl://DATALAKEACCOUNT.azuredatalakestore.net/clusters/CLUSTERNAME/sampledata1/
    hdfs dfs -mkdir adl:///sampledata2/
    hdfs dfs -mkdir /sampledata3/
    ```

1. Копирование данных из локального хранилища в хранилище кластера.

    ```bash
    hdfs dfs -copyFromLocal testFile.txt adl://DATALAKEACCOUNT.azuredatalakestore.net/clusters/CLUSTERNAME/sampledata1/
    hdfs dfs -copyFromLocal testFile.txt adl:///sampledata2/
    hdfs dfs -copyFromLocal testFile.txt /sampledata3/
    ```

1. Список содержимого каталога в хранилище кластера.

    ```bash
    hdfs dfs -ls adl://DATALAKEACCOUNT.azuredatalakestore.net/clusters/CLUSTERNAME/sampledata1/
    hdfs dfs -ls adl:///sampledata2/
    hdfs dfs -ls /sampledata3/
    ```

#### <a name="creating-a-hive-table"></a>Создание таблицы Hive

Для наглядности показаны три расположения файлов. Для фактического выполнения используйте только одну из записей `LOCATION`.

```hql
DROP TABLE myTable;
CREATE EXTERNAL TABLE myTable (
    t1 string,
    t2 string,
    t3 string,
    t4 string,
    t5 string,
    t6 string,
    t7 string)
ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
STORED AS TEXTFILE
LOCATION 'adl://DATALAKEACCOUNT.azuredatalakestore.net/clusters/CLUSTERNAME/example/data/';
LOCATION 'adl:///example/data/';
LOCATION '/example/data/';
```

## <a name="identify-storage-path-from-abmari"></a>Указание пути к хранилищу из Абмари

Чтобы указать полный путь к настроенному хранилищу по умолчанию, перейдите к **HDFS** > **configs** и введите `fs.defaultFS` в поле ввода фильтра.

## <a name="create-hdinsight-clusters-with-access-to-data-lake-storage-gen1"></a>Создание кластеров HDInsight с доступом к Data Lake Storage 1-го поколения

По указанным ниже ссылкам вы найдете подробные инструкции по созданию кластеров HDInsight с доступом к Data Lake Storage 1-го поколения.

* [Использование портала](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md)
* [Создание кластеров HDInsight, использующих Azure Data Lake Storage 1-го поколения в качестве хранилища по умолчанию, с помощью PowerShell](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
* [Использование Azure PowerShell для создания кластера HDInsight с Azure Data Lake Storage 1-го поколения (в качестве дополнительного хранилища)](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell.md)
* [Создание кластера HDInsight с Data Lake Store с помощью шаблона Azure Resource Manager](../data-lake-store/data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)

## <a name="refresh-the-hdinsight-certificate-for-data-lake-storage-gen1-access"></a>Обновление сертификата HDInsight для получения доступа к Data Lake Storage 1-го поколения

Следующий фрагмент кода PowerShell считывает сертификат из локального файла или Azure Key Vault и настраивает его в кластере HDInsight для доступа к Azure Data Lake Storage 1-го поколения. Укажите собственные имена кластера HDInsight и группы ресурсов, идентификатор подписки и приложения, а также локальный путь к сертификату. Когда отобразится соответствующий запрос, введите пароль.

```powershell-interactive
$clusterName = '<clustername>'
$resourceGroupName = '<resourcegroupname>'
$subscriptionId = '01234567-8a6c-43bc-83d3-6b318c6c7305'
$appId = '01234567-e100-4118-8ba6-c25834f4e938'
$addNewCertKeyCredential = $true
$certFilePath = 'C:\localfolder\adls.pfx'
$KeyVaultName = "my-key-vault-name"
$KeyVaultSecretName = "my-key-vault-secret-name"
$certPassword = Read-Host "Enter Certificate Password"
# certSource
# 0 - create self signed cert
# 1 - read cert from file path
# 2 - read cert from key vault
$certSource = 0

Login-AzAccount
Select-AzSubscription -SubscriptionId $subscriptionId

if($certSource -eq 0)
{
    Write-Host "Generating new SelfSigned certificate"

    $cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=hdinsightAdlsCert" -KeySpec KeyExchange
    $certBytes = $cert.Export([System.Security.Cryptography.X509Certificates.X509ContentType]::Pkcs12, $certPassword);
    $certString = [System.Convert]::ToBase64String($certBytes)
}
elseif($certSource -eq 1)
{

    Write-Host "Reading the cert file from path $certFilePath"

    $cert = new-object System.Security.Cryptography.X509Certificates.X509Certificate2($certFilePath, $certPassword)
    $certString = [System.Convert]::ToBase64String([System.IO.File]::ReadAllBytes($certFilePath))
}
elseif($certSource -eq 2)
{

    Write-Host "Reading the cert file from Azure Key Vault $KeyVaultName"

    $cert = (Get-AzureKeyVaultSecret -VaultName $KeyVaultName -Name $KeyVaultSecretName)
    $certValue = [System.Convert]::FromBase64String($cert.SecretValueText)
    $certObject = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList $certValue, $null,"Exportable, PersistKeySet"

    $certBytes = $certObject.Export([System.Security.Cryptography.X509Certificates.X509ContentType]::Pkcs12, $certPassword.SecretValueText);

    $certString =[System.Convert]::ToBase64String($certBytes)
}

if($addNewCertKeyCredential)
{
    Write-Host "Creating new KeyCredential for the app"
    $keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())
    New-AzADAppCredential -ApplicationId $appId -CertValue $keyValue -EndDate $cert.NotAfter -StartDate $cert.NotBefore
    Write-Host "Waiting for 7 minutes for the permissions to get propagated"
    Start-Sleep -s 420 #7 minutes
}

Write-Host "Updating the certificate on HDInsight cluster..."

Invoke-AzResourceAction `
    -ResourceGroupName $resourceGroupName `
    -ResourceType 'Microsoft.HDInsight/clusters' `
    -ResourceName $clusterName `
    -ApiVersion '2015-03-01-preview' `
    -Action 'updateclusteridentitycertificate' `
    -Parameters @{ ApplicationId = $appId; Certificate = $certString; CertificatePassword = $certPassword.ToString() } `
    -Force
```

## <a name="next-steps"></a>Дальнейшие действия

Из этой статьи вы узнали, как использовать HDFS-совместимую службу Azure Data Lake Storage 1-го поколения с HDInsight. Это позволяет создавать масштабируемые, долгосрочные решения для получения данных архивирования, а также использовать HDInsight для разблокирования информации внутри хранимых структурированных и неструктурированных данных.

Дополнительные сведения можно найти в разделе

* [Приступая к работе с Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Краткое руководство по установке кластеров в HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md)
* [Использование Azure PowerShell для создания кластера HDInsight с Azure Data Lake Storage 1-го поколения (в качестве дополнительного хранилища)](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell.md).
* [Отправка данных в HDInsight](hdinsight-upload-data.md)
* [Использование Hive и HiveQL с Hadoop в HDInsight для анализа примера файла Apache log4j](hadoop/hdinsight-use-hive.md)
* [Использование подписей общего доступа к хранилищу Azure для ограничения доступа к данным с помощью HDInsight](hdinsight-storage-sharedaccesssignature-permissions.md)
* [Руководство. Извлечение, преобразование и загрузка данных с помощью интерактивного запроса в Azure HDInsight](./interactive-query/interactive-query-tutorial-analyze-flight-data.md)
