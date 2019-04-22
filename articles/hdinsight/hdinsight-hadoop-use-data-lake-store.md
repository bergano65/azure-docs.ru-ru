---
title: Использование Azure Data Lake Storage 1-го поколения с Hadoop в Azure HDInsight
description: Сведения о запросе данных из Azure Data Lake Storage 1-го поколения и сохранении результатов анализа.
services: hdinsight,storage
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 11/06/2018
ms.openlocfilehash: ed8884462030e10625f332b182bd900e833f34f4
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59272739"
---
# <a name="use-data-lake-storage-gen1-with-azure-hdinsight-clusters"></a>Использование Data Lake Storage 1-го поколения с кластерами Azure HDInsight

> [!Note] 
> Развертывайте новые кластеры HDInsight с помощью [Azure Data Lake Storage 2-го поколения](hdinsight-hadoop-use-data-lake-storage-gen2.md), чтобы повысить их производительность и получить дополнительные функции.

Данные для анализа в кластере HDInsight можно хранить в [службе хранилища Azure](../storage/common/storage-introduction.md), [Azure Data Lake Storage 1-го поколения](../data-lake-store/data-lake-store-overview.md) или [Azure Data Lake Storage 2-го поколения](../storage/blobs/data-lake-storage-introduction.md). Все варианты хранилищ позволяют безопасно и без потери пользовательских данных удалять используемые для расчетов кластеры HDInsight.

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
| HDInsight версии 4.0 | Нет  | Нет  |Поколение 1 ADLS с HDInsight 4.0 не поддерживается |
| HDInsight версии 3.6 | Yes | Yes | За исключением HBase|
| HDInsight версии 3.5 | Yes | Yes | За исключением HBase|
| HDInsight версия 3.4 | Нет  | Yes | |
| HDInsight версии 3.3 | Нет  | Нет  | |
| HDInsight версии 3.2 | Нет  | Yes | |
| Storm | | |Data Lake Storage 1-го поколения можно использовать для записи данных из топологии Storm. Data Lake Storage также может использоваться для хранения эталонных данных, которые затем можно будет считать с помощью топологии Storm.|

> [!WARNING]  
> HDInsight HBase не поддерживается в Azure Data Lake Storage 1-го поколения.

Использование Data Lake Storage 1-го поколения в качестве дополнительной учетной записи хранения не влияет на производительность и на возможность выполнять чтение или запись в хранилище Azure из кластера.

## <a name="use-data-lake-storage-gen1-as-default-storage"></a>Использование Data Lake Storage 1-го поколения как хранилища по умолчанию

Если для кластера HDInsight в качестве хранилища по умолчанию развернуто Data Lake Storage 1-го поколения, все файлы этого кластера хранятся в расположении `adl://mydatalakestore/<cluster_root_path>/`, где `<cluster_root_path>` обозначает имя папки, созданной в Data Lake Storage. Указав корневую папку для каждого кластера, вы можете использовать одну и ту же учетную запись Data Lake Storage для нескольких кластеров. Таким образом, у вас могут получиться такие настройки:

* Cluster1 использует путь `adl://mydatalakestore/cluster1storage`;
* Cluster2 использует путь `adl://mydatalakestore/cluster2storage`.

Обратите внимание, что оба кластера используют одну учетную запись Data Lake Storage 1-го поколения с именем **mydatalakestore**. Каждый кластер имеет доступ к собственный корневой файловой системе в Data Lake Storage. В частности, при развертывании на портале Azure в качестве корневого пути предлагается использовать имя папки, например **/clusters/\<clustername>**.

Чтобы использовать Data Lake Storage 1-го поколения как хранилище по умолчанию, необходимо предоставить субъекту-службе доступ к таким объектам:

- Корневой каталог учетной записи Data Lake Storage 1-го поколения.  Например: adl://mydatalakestore/.
- Папка для всех папок кластера.  Например: adl://mydatalakestore/clusters.
- Папка для кластера.  Например: adl://mydatalakestore/clusters/cluster1storage.

Дополнительные сведения о создании субъекта-службы и предоставлении доступа см. в разделе "Настройка доступа к Data Lake Storage".

### <a name="extracting-a-certificate-from-azure-keyvault-for-use-in-cluster-creation"></a>Извлечение сертификата из Azure Key Vault для использования при создании кластера

Если вы хотите настроить Azure Data Lake Storage 1-го поколения в качестве хранилища по умолчанию для нового кластера, а сертификат для субъекта-службы хранится в Azure Key Vault, потребуется выполнить дополнительные действия для преобразования сертификата в требуемый формат. В приведенном ниже фрагменте кода показано, как выполнить преобразование.

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

Data Lake Storage 1-го поколения можно использовать в качестве дополнительного хранилища кластера. В таких случаях хранилищем кластера по умолчанию может быть Azure Storage Blob или учетная запись Data Lake Storage. При выполнении заданий HDInsight с применением данных, хранящихся в Data Lake Storage как дополнительном хранилище, необходимо использовать полный путь к файлам. Например: 

    adl://mydatalakestore.azuredatalakestore.net/<file_path>

Обратите внимание, что в этом URL-адресе нет **cluster_root_path**. Это объясняется тем, что в данном случае Data Lake Storage не является хранилищем по умолчанию, поэтому требуется просто указать путь к файлам.

Чтобы использовать Data Lake Storage 1-го поколения как дополнительное хранилище, достаточно предоставить субъекту-службе доступ к расположениям, в которых хранятся файлы.  Например: 

    adl://mydatalakestore.azuredatalakestore.net/<file_path>

Дополнительные сведения о создании субъекта-службы и предоставлении доступа см. в разделе "Настройка доступа к Data Lake Storage".


## <a name="use-more-than-one-data-lake-storage-accounts"></a>Использование нескольких учетных записей Data Lake Storage

Можно добавить учетную запись Data Lake Storage в качестве дополнительного хранилища и несколько учетных записей Data Lake Storage, предоставив кластеру HDInsight разрешение на доступ к данным в одной учетной записи Data Lake Storage или нескольких. Ознакомьтесь с разделом "Настройка доступа к Data Lake Storage".

## <a name="configure-data-lake-storage-access"></a>Настройка доступа к Data Lake Storage

Чтобы настроить доступ к Data Lake Storage из кластера HDInsight, требуется субъект-служба Azure Active Directory (AAD). Создать субъект-службу может только администратор Azure AD. Для создания субъекта-службы необходим сертификат. Дополнительные сведения см. в [кратком руководстве по настройке кластеров в HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md) и [созданию субъекта-службы с самозаверяющим сертификатом](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-self-signed-certificate).

> [!NOTE]  
> Если вы собираетесь добавить Azure Data Lake Storage 1-го поколения в качестве дополнительного хранилища для кластера HDInsight, мы настоятельно рекомендуем сделать это во время создания кластера, как описано в этой статье. Сценарий с добавлением Azure Data Lake Storage 1-го поколения в качестве дополнительного хранилища для существующего кластера HDInsight не поддерживается.
>

## <a name="access-files-from-the-cluster"></a>Доступ к файлам из кластера

Существует несколько способов доступа к файлам в Data Lake Storage из кластера HDInsight.

* **С помощью полного доменного имени**. При таком подходе необходимо указать полный путь к файлу, к которому требуется доступ.

        adl://mydatalakestore.azuredatalakestore.net/<cluster_root_path>/<file_path>

* **Используя сокращенный формат пути**. При таком подходе путь до корня кластера заменяется на adl:///. Таким образом, в приведенном выше примере `adl://mydatalakestore.azuredatalakestore.net/<cluster_root_path>/` можно заменить на `adl:///`.

        adl:///<file path>

* **С помощью относительного пути**. При таком подходе указывается только относительный путь к файлу, к которому требуется доступ. Например, если полный путь к файлу —

        adl://mydatalakestore.azuredatalakestore.net/<cluster_root_path>/example/data/sample.log

    то получить доступ к этому же файлу sample.log можно с помощью относительного пути:

        /example/data/sample.log

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

Login-AzAccount
Select-AzSubscription -SubscriptionId $subscriptionId

if($addNewCertKeyCredential)
{
    Write-Host "Creating new KeyCredential for the app"
    $keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())
    New-AzADAppCredential -ApplicationId $appId -CertValue $keyValue -EndDate $cert.NotAfter -StartDate $cert.NotBefore
    Write-Host "Waiting for 30 seconds for the permissions to get propagated"
    Start-Sleep -s 30
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

* [Руководство по Hadoop. Начало работы с Hadoop в HDInsight на платформе Linux][hdinsight-get-started]
* [Краткое руководство по настройке кластеров в HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md)
* [Использование Azure PowerShell для создания кластера HDInsight с Azure Data Lake Storage 1-го поколения (в качестве дополнительного хранилища)](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell.md).
* [Отправка данных в HDInsight][hdinsight-upload-data]
* [Использование Apache Hive с HDInsight][hdinsight-use-hive]
* [Использование Apache Pig с HDInsight][hdinsight-use-pig]
* [Использование подписанных URL-адресов хранилища Azure для ограничения доступа к данным с помощью HDInsight][hdinsight-use-sas]

[hdinsight-use-sas]: hdinsight-storage-sharedaccesssignature-permissions.md
[powershell-install]: /powershell/azureps-cmdlets-docs
[hdinsight-creation]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-get-started]:hadoop/apache-hadoop-linux-tutorial-get-started.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-hive]:hadoop/hdinsight-use-hive.md
[hdinsight-use-pig]:hadoop/hdinsight-use-pig.md

[blob-storage-restAPI]: https://msdn.microsoft.com/library/windowsazure/dd135733.aspx
[azure-storage-create]:../storage/common/storage-create-storage-account.md

[img-hdi-powershell-blobcommands]: ./media/hdinsight-hadoop-use-blob-storage/HDI.PowerShell.BlobCommands.png
[img-hdi-quick-create]: ./media/hdinsight-hadoop-use-blob-storage/HDI.QuickCreateCluster.png
[img-hdi-custom-create-storage-account]: ./media/hdinsight-hadoop-use-blob-storage/HDI.CustomCreateStorageAccount.png  
