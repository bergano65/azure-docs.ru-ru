---
title: Неудается доступа к файлам хранения data Lake в Azure HDInsight
description: Неудается доступа к файлам хранения data Lake в Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/13/2019
ms.openlocfilehash: 21269f7d5a9ec832a49a613351702dd24be156af
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75894165"
---
# <a name="unable-to-access-data-lake-storage-files-in-azure-hdinsight"></a>Неудается доступа к файлам хранения data Lake в Azure HDInsight

В этой статье описаны шаги устранения неполадок и возможные решения проблем при взаимодействии с кластерами Azure HDInsight.

## <a name="issue-acl-verification-failed"></a>Выпуск: Проверка ACL не удалась

Вы получаете сообщение об ошибке, аналогичное:

```
LISTSTATUS failed with error 0x83090aa2 (Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.).
```

### <a name="cause"></a>Причина

Возможно, пользователь отозвал разрешения директора службы (SP) на файлы/папки.

### <a name="resolution"></a>Решение

1. Убедитесь, что SP имеет разрешения 'x' для прохождения по пути. Дополнительные сведения см. в разделе [Разрешения](https://hdinsight.github.io/ClusterCRUD/ADLS/adls-create-permission-setup.html). Пример команды dfs для проверки доступа к файлам/папкам в учетной записи хранения Data Lake:

    ```
    hdfs dfs -ls /<path to check access>
    ```

1. Настройка необходимых разрешений для доступа к пути на основе выполняемой операции чтения/записи. Смотрите здесь для разрешений, необходимых для различных операций файловой системы.

---

## <a name="issue-service-principal-certificate-expiry"></a>Выпуск: Срок действия основного сертификата обслуживания

Вы получаете сообщение об ошибке, аналогичное:

```
Token Refresh failed - Received invalid http response: 500
```

### <a name="cause"></a>Причина

Срок действия сертификата, предоставленного для основного доступа службы, может истечь.

1. SSH в headnode. Проверьте доступ к учетной записи хранилища с помощью следующей команды dfs:

    ```
    hdfs dfs -ls /
    ```

1. Подтвердите, что сообщение об ошибке похоже на следующее:

    ```
    {"stderr": "-ls: Token Refresh failed - Received invalid http response: 500, text = Response{protocol=http/1.1, code=500, message=Internal Server Error, url=http://gw0-abccluster.24ajrd4341lebfgq5unsrzq0ue.fx.internal.cloudapp.net:909/api/oauthtoken}}...
    ```

1. Получить один из URL-адресов от `core-site.xml property`  -  `fs.azure.datalake.token.provider.service.urls`.

1. Выполнить следующую команду локон для получения токена OAuth.

    ```
    curl gw0-abccluster.24ajrd4341lebfgq5unsrzq0ue.fx.internal.cloudapp.net:909/api/oauthtoken
    ```

1. Выход для действительного директора службы должен быть чем-то вроде:

    ```
    {"AccessToken":"MIIGHQYJKoZIhvcNAQcDoIIGDjCCBgoCAQA…….","ExpiresOn":1500447750098}
    ```

1. Если срок действия основного сертификата службы истек, выход будет выглядеть примерно так:

    ```
    Exception in OAuthTokenController.GetOAuthToken: 'System.InvalidOperationException: Error while getting the OAuth token from AAD for AppPrincipalId 23abe517-2ffd-4124-aa2d-7c224672cae2, ResourceUri https://management.core.windows.net/, AADTenantId https://login.windows.net/80abc8bf-86f1-41af-91ab-2d7cd011db47, ClientCertificateThumbprint C49C25705D60569884EDC91986CEF8A01A495783 ---> Microsoft.IdentityModel.Clients.ActiveDirectory.AdalServiceException: AADSTS70002: Error validating credentials. AADSTS50012: Client assertion contains an invalid signature. **[Reason - The key used is expired.**, Thumbprint of key used by client: 'C49C25705D60569884EDC91986CEF8A01A495783', Found key 'Start=08/03/2016, End=08/03/2017, Thumbprint=C39C25705D60569884EDC91986CEF8A01A4956D1', Configured keys: [Key0:Start=08/03/2016, End=08/03/2017, Thumbprint=C39C25705D60569884EDC91986CEF8A01A4956D1;]]
    Trace ID: e4d34f1c-a584-47f5-884e-1235026d5000
    Correlation ID: a44d870e-6f23-405a-8b23-9b44aebfa4bb
    Timestamp: 2017-10-06 20:44:56Z ---> System.Net.WebException: The remote server returned an error: (401) Unauthorized.
    at System.Net.HttpWebRequest.GetResponse()
    at Microsoft.IdentityModel.Clients.ActiveDirectory.HttpWebRequestWrapper.<GetResponseSyncOrAsync>d__2.MoveNext()
    ```

1. Любые другие ошибки, связанные с ошибками/сертификатами, могут быть распознаны путем пингинга URL-адреса шлюза, чтобы получить токен OAuth.

1. Если вы получаете следующие ошибки при попытке получить доступ к ADLS из кластера ИРЧП. Проверьте, истек ли срок действия сертификата, выяснив вышеуказанные шаги.

    ```
    Error: java.lang.IllegalArgumentException: Token Refresh failed - Received invalid http response: 500, text = Response{protocol=http/1.1, code=500, message=Internal Server Error, url=http://clustername.hmssomerandomstringc.cx.internal.cloudapp.net:909/api/oauthtoken}
    ```

### <a name="resolution"></a>Решение

Создайте новый сертификат или назначить существующий сертификат с помощью следующего скрипта PowerShell:

```powershell
$clusterName = 'CLUSTERNAME'
$resourceGroupName = 'RGNAME'
$subscriptionId = 'SUBSCRIPTIONID'
$appId = 'APPLICATIONID'
$generateSelfSignedCert = $false
$addNewCertKeyCredential = $true
$certFilePath = 'NEW_CERT_PFX_LOCAL_PATH'
$certPassword = Read-Host "Enter Certificate Password"

if($generateSelfSignedCert)
{
    Write-Host "Generating new SelfSigned certificate"
    
    $cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=hdinsightAdlsCert" -KeySpec KeyExchange
    $certBytes = $cert.Export([System.Security.Cryptography.X509Certificates.X509ContentType]::Pkcs12, $certPassword);
    $certString = [System.Convert]::ToBase64String($certBytes)
}
else
{

    Write-Host "Reading the cert file from path $certFilePath"

    $cert = new-object System.Security.Cryptography.X509Certificates.X509Certificate2($certFilePath, $certPassword)
    $certString = [System.Convert]::ToBase64String([System.IO.File]::ReadAllBytes($certFilePath))
}


Login-AzureRmAccount

if($addNewCertKeyCredential)
{
    Write-Host "Creating new KeyCredential for the app"

    $keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())
    
    New-AzureRmADAppCredential -ApplicationId $appId -CertValue $keyValue -EndDate $cert.NotAfter -StartDate $cert.NotBefore

    Write-Host "Waiting for 30 seconds for the permissions to get propagated"

    Start-Sleep -s 30
}

Select-AzureRmSubscription -SubscriptionId $subscriptionId

Write-Host "Updating the certificate on HDInsight cluster."

Invoke-AzureRmResourceAction `
    -ResourceGroupName $resourceGroupName `
    -ResourceType 'Microsoft.HDInsight/clusters' `
    -ResourceName $clusterName `
    -ApiVersion '2015-03-01-preview' `
    -Action 'updateclusteridentitycertificate' `
    -Parameters @{ ApplicationId = $appId.ToString(); Certificate = $certString; CertificatePassword = $certPassword.ToString() } `
    -Force

```

Для назначения существующего сертификата создайте сертификат, подготовите файл .pfx и пароль. Связать сертификат с основой обслуживания, с помощью которого был создан кластер, и подготовить AppId.

Выполните команду PowerShell после замены параметров фактическими значениями.

## <a name="next-steps"></a>Дальнейшие действия

Если вы не видите своего варианта проблемы или вам не удается ее устранить, дополнительные сведения можно получить, посетив один из следующих каналов.

* Получите ответы от экспертов Azure через [поддержку сообщества Azure.](https://azure.microsoft.com/support/community/)

* Связаться [@AzureSupport](https://twitter.com/azuresupport) с - официальная учетная запись Microsoft Azure для улучшения обслуживания клиентов. Подключение сообщества Azure к нужным ресурсам: ответы, поддержка и эксперты.

* Если вам нужна дополнительная помощь, вы можете отправить запрос на поддержку с [портала Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Выберите **поддержку** из бара меню или откройте концентратор **поддержки Справка и.** Для получения более подробной информации просмотрите [Как создать запрос поддержки Azure.](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) Доступ к управлению подпиской и поддержке выставления счетов включен в подписку Microsoft Azure, а техническая поддержка обеспечивается через один из [планов поддержки Azure.](https://azure.microsoft.com/support/plans/)
