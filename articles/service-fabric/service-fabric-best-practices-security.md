---
title: Рекомендации по безопасности Azure Service Fabric | Документация Майкрософт
description: Рекомендации по безопасности Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: peterpogorski
manager: chackdan
editor: ''
ms.assetid: 19ca51e8-69b9-4952-b4b5-4bf04cded217
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/23/2019
ms.author: pepogors
ms.openlocfilehash: fec81e843753656d651c6d5d0b73077a964be9d4
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/04/2019
ms.locfileid: "74807448"
---
# <a name="azure-service-fabric-security"></a>Безопасность Azure Service Fabric 

Дополнительные сведения о [рекомендациях по безопасности Azure](https://docs.microsoft.com/azure/security/) см. в [этой статье](https://docs.microsoft.com/azure/security/fundamentals/service-fabric-best-practices).

## <a name="key-vault"></a>Key Vault

[Azure Key Vault](https://docs.microsoft.com/azure/key-vault/) — это рекомендуемая служба управления секретами для приложений и кластеров Azure Service Fabric.
> [!NOTE]
> Если сертификаты или секреты из Key Vault развертываются в масштабируемом наборе виртуальных машин как секрет масштабируемого набора виртуальных машин, хранилище ключей и масштабируемый набор должны располагаться совместно.

## <a name="create-certificate-authority-issued-service-fabric-certificate"></a>Создание сертификата Service Fabric, выданного центром сертификации

Сертификат Azure Key Vault можно создать или импортировать в хранилище Key Vault. При создании сертификата хранилища Key Vault закрытый ключ создается внутри Key Vault и никогда не предоставляется владельцу сертификата. Ниже приведены способы создания сертификата в хранилище Key Vault.

- Создайте самозаверяющий сертификат, чтобы создать пару открытого и закрытого ключей и связать ее с сертификатом. Сертификат будет подписан собственным ключом. 
- Создайте сертификат вручную, чтобы создать пару открытого и закрытого ключей и создать запрос на подпись сертификата X.509. Запрос на подпись может быть подписан центром регистрации или сертификации. Для завершения создания сертификата в Key Vault подписанный сертификат x509 может быть объединен с ожидающей парой ключей. Несмотря на то что для этого метода необходимо выполнить дополнительные шаги, он более безопасен, так как созданный закрытый ключ доступен только для Key Vault. Это описано на приведенной ниже схеме. 

Дополнительные сведения см. в статье [Способы создания сертификатов](https://docs.microsoft.com/azure/key-vault/create-certificate).

## <a name="deploy-key-vault-certificates-to-service-fabric-cluster-virtual-machine-scale-sets"></a>Развертывание сертификатов Key Vault в масштабируемых наборах виртуальных машин кластера Service Fabric.

Чтобы развернуть сертификаты из совместно размещенного хранилища ключей в масштабируемый набор виртуальных машин, используйте набор [osProfile](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile). Ниже приведены свойства шаблона Resource Manager:

```json
"secrets": [
   {
       "sourceVault": {
           "id": "[parameters('sourceVaultValue')]"
       },
       "vaultCertificates": [
          {
              "certificateStore": "[parameters('certificateStoreValue')]",
              "certificateUrl": "[parameters('certificateUrlValue')]"
          }
       ]
   }
]
```

> [!NOTE]
> Хранилище должно быть включено для развертывания шаблона Resource Manager.

## <a name="apply-an-access-control-list-acl-to-your-certificate-for-your-service-fabric-cluster"></a>Применение списка управления доступом (ACL) к сертификату для кластера Service Fabric

Для настройки безопасности узлов используется издатель [расширений масштабируемых наборов виртуальных машин](https://docs.microsoft.com/cli/azure/vmss/extension?view=azure-cli-latest) Microsoft.Azure.ServiceFabric.
Чтобы применить список управления доступом к сертификатам процессов кластера Service Fabric, используйте следующие свойства шаблона Resource Manager:

```json
"certificate": {
   "commonNames": [
       "[parameters('certificateCommonName')]"
   ],
   "x509StoreName": "[parameters('certificateStoreValue')]"
}
```

## <a name="secure-a-service-fabric-cluster-certificate-by-common-name"></a>Защита сертификата кластера Service Fabric по общему имени

Чтобы защитить кластер Service Fabric сертификатом, `Common Name`используйте свойство шаблона Resource Manager [certificateCommonNames](https://docs.microsoft.com/rest/api/servicefabric/sfrp-model-clusterproperties#certificatecommonnames) следующим образом:

```json
"certificateCommonNames": {
    "commonNames": [
        {
            "certificateCommonName": "[parameters('certificateCommonName')]",
            "certificateIssuerThumbprint": "[parameters('certificateIssuerThumbprint')]"
        }
    ],
    "x509StoreName": "[parameters('certificateStoreValue')]"
}
```

> [!NOTE]
> Кластеры Service Fabric будут использовать первый действительный сертификат, найденный в хранилище сертификатов узла. В Windows это будет сертификат с последней датой истечения срока действия, соответствующий общему имени и отпечатку издателя.

Домены Azure, такие как *\<ВАШ ПОДДОМЕН\>.cloudapp.azure.com или \<ВАШ ПОДДОМЕН\>.trafficmanager.net принадлежат корпорации Майкрософт. Центры сертификации не будут выдавать сертификаты для доменов неавторизованным пользователям. Большинству пользователей потребуется приобрести домен у регистратора или быть авторизованным администратором домена, чтобы центр сертификации выдал сертификат с этим общим именем.

Дополнительные сведения о настройке службы DNS для преобразования домена в IP-адрес Майкрософт см. в [этой статье](https://docs.microsoft.com/azure/dns/dns-delegate-domain-azure-dns).

> [!NOTE]
> После делегирования серверов доменных имен серверам имен зон DNS Azure добавьте в зону DNS следующие две записи:
> - Запись "A" для вершины домена, которая не является `Alias record set` для всех IP-адресов, которые будет разрешать личный домен.
> - Запись "C" для подготовленных поддоменов Майкрософт, которые не являются `Alias record set`. Например, можно использовать диспетчер трафика или DNS-имя Load Balancer.

Чтобы обновить портал для отображения пользовательского DNS-имени кластера Service Fabric `"managementEndpoint"`, обновите свойства шаблона Resource Manager для кластера Service Fabric.

```json
 "managementEndpoint": "[concat('https://<YOUR CUSTOM DOMAIN>:',parameters('nt0fabricHttpGatewayPort'))]",
```

## <a name="encrypting-service-fabric-package-secret-values"></a>Шифрование значений секретов пакетов Service Fabric

Общие значения, зашифрованные в пакетах Service Fabric, включают в себя учетные данные Реестра контейнеров Azure (ACR), переменные среды, параметры и ключи учетной записи хранения подключаемого модуля томов Azure.

Чтобы [настроить сертификат шифрования и шифрование секретов в кластерах Windows](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-secret-management-windows):

Создайте самозаверяющий сертификат для шифрования секрета:

```powershell
New-SelfSignedCertificate -Type DocumentEncryptionCert -KeyUsage DataEncipherment -Subject mydataenciphermentcert -Provider 'Microsoft Enhanced Cryptographic Provider v1.0'
```

Используйте инструкции из [этого раздела](#deploy-key-vault-certificates-to-service-fabric-cluster-virtual-machine-scale-sets), чтобы развернуть сертификаты Key Vault в масштабируемые наборы виртуальных машин кластера Service Fabric.

Зашифруйте секрет с помощью следующей команды PowerShell, а затем обновите в манифесте приложения Service Fabric зашифрованное значение.

``` powershell
Invoke-ServiceFabricEncryptText -CertStore -CertThumbprint "<thumbprint>" -Text "mysecret" -StoreLocation CurrentUser -StoreName My
```

Чтобы [настроить сертификат шифрования и шифрование секретов в кластерах Linux](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-secret-management-linux):

Создайте самозаверяющий сертификат для шифрования секретов:

```bash
user@linux:~$ openssl req -newkey rsa:2048 -nodes -keyout TestCert.prv -x509 -days 365 -out TestCert.pem
user@linux:~$ cat TestCert.prv >> TestCert.pem
```

Используйте инструкции из [этого раздела](#deploy-key-vault-certificates-to-service-fabric-cluster-virtual-machine-scale-sets) для работы с масштабируемыми наборами виртуальных машин кластера Service Fabric.

Зашифруйте секрет с помощью следующих команд, а затем обновите в манифесте приложения Service Fabric зашифрованное значение.

```bash
user@linux:$ echo "Hello World!" > plaintext.txt
user@linux:$ iconv -f ASCII -t UTF-16LE plaintext.txt -o plaintext_UTF-16.txt
user@linux:$ openssl smime -encrypt -in plaintext_UTF-16.txt -binary -outform der TestCert.pem | base64 > encrypted.txt
```

После шифрования защищенных значений [укажите зашифрованные секреты в приложении Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-secret-management#specify-encrypted-secrets-in-an-application) и [расшифруйте зашифрованные секреты из служебного кода](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-secret-management#decrypt-encrypted-secrets-from-service-code).

## <a name="include-certificate-in-service-fabric-applications"></a>Включить сертификат в Service Fabric приложения

Чтобы предоставить приложению доступ к секретам, включите сертификат, добавив элемент **секретсцертификате** в манифест приложения.

```xml
<ApplicationManifest … >
  ...
  <Certificates>
    <SecretsCertificate Name="MyCert" X509FindType="FindByThumbprint" X509FindValue="[YourCertThumbrint]"/>
  </Certificates>
</ApplicationManifest>
```
## <a name="authenticate-service-fabric-applications-to-azure-resources-using-managed-service-identity-msi"></a>Аутентификация приложений Service Fabric в ресурсах Azure с помощью Управляемого удостоверения службы (MSI)

Дополнительные сведения об управляемых удостоверениях в ресурсах Azure см. в разделе [Принцип работы управляемых удостоверений для ресурсов Azure](../active-directory/managed-identities-azure-resources/overview.md#how-does-the-managed-identities-for-azure-resources-work).
Кластеры Azure Service Fabric размещаются в масштабируемых наборах виртуальных машин, которые поддерживают [Управляемое удостоверение службы](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/services-support-msi#azure-services-that-support-managed-identities-for-azure-resources).
Список служб, для аутентификации которых можно использовать MSI, см. в разделе [Службы Azure, поддерживающие аутентификацию Azure AD](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/services-support-msi#azure-services-that-support-azure-ad-authentication).


Чтобы включить управляемое удостоверение, назначаемое системой, для нового или имеющегося масштабируемого набора виртуальных машин, объявите следующее значение `"Microsoft.Compute/virtualMachinesScaleSets"`:

```json
"identity": { 
    "type": "SystemAssigned"
}
```
Дополнительные сведения см. в разделе [Управляемое удостоверение, назначаемое системой](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-template-windows-vmss#system-assigned-managed-identity).

Если вы создали [управляемое удостоверение, назначаемое пользователем](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm#create-a-user-assigned-managed-identity), объявите в шаблоне следующий ресурс, чтобы назначить его масштабируемому набору виртуальной машины. Замените `\<USERASSIGNEDIDENTITYNAME\>` именем созданного управляемого удостоверения, назначаемого пользователем.

```json
"identity": {
    "type": "userAssigned",
    "userAssignedIdentities": {
        "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]": {}
    }
}
```

Прежде чем приложение Service Fabric сможет использовать управляемое удостоверение, необходимо предоставить разрешения для ресурсов Azure, с помощью которых приложение должно проходить аутентификацию.
Следующие команды предоставляют доступ к ресурсу Azure:

```bash
principalid=$(az resource show --id /subscriptions/<YOUR SUBSCRIPTON>/resourceGroups/<YOUR RG>/providers/Microsoft.Compute/virtualMachineScaleSets/<YOUR SCALE SET> --api-version 2018-06-01 | python -c "import sys, json; print(json.load(sys.stdin)['identity']['principalId'])")

az role assignment create --assignee $principalid --role 'Contributor' --scope "/subscriptions/<YOUR SUBSCRIPTION>/resourceGroups/<YOUR RG>/providers/<PROVIDER NAME>/<RESOURCE TYPE>/<RESOURCE NAME>"
```

В коде приложения Service Fabric [Получите маркер доступа](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/how-to-use-vm-token#get-a-token-using-http) для Azure Resource Manager, выполнив все остальные действия, аналогичные следующим:

```bash
access_token=$(curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -H Metadata:true | python -c "import sys, json; print json.load(sys.stdin)['access_token']")

```

Затем ваше приложение Service Fabric может использовать маркер доступа для аутентификации в ресурсах Azure, которые поддерживают Active Directory.
В приведенном ниже примере показано, как это сделать в ресурсе Cosmos DB:

```bash
cosmos_db_password=$(curl 'https://management.azure.com/subscriptions/<YOUR SUBSCRIPTION>/resourceGroups/<YOUR RG>/providers/Microsoft.DocumentDB/databaseAccounts/<YOUR ACCOUNT>/listKeys?api-version=2016-03-31' -X POST -d "" -H "Authorization: Bearer $access_token" | python -c "import sys, json; print(json.load(sys.stdin)['primaryMasterKey'])")
```
## <a name="windows-security-baselines"></a>Базовые параметры безопасности Windows
[Мы рекомендуем реализовать стандартную промышленную конфигурацию, которая широко известна и хорошо тестируется, например базовые планы безопасности Майкрософт, а не самостоятельное создание базовых показателей](https://docs.microsoft.com/windows/security/threat-protection/windows-security-baselines). параметр для подготовки этих данных в масштабируемых наборах виртуальных машин заключается в использовании обработчика расширения Desired State Configuration (DSC) Azure для настройки виртуальных машин в режиме «в сети», чтобы они выполняли рабочее программное обеспечение.

## <a name="azure-firewall"></a>Брандмауэр Azure
[Брандмауэр Azure — это управляемая облачная служба безопасности сети, которая защищает ресурсы виртуальной сети Azure. Это полностью работоспособный брандмауэр в качестве службы со встроенными возможностями обеспечения высокой доступности и масштабируемости облака.](https://docs.microsoft.com/azure/firewall/overview) Это позволяет ограничить исходящий трафик HTTP/S указанным списком полных доменных имен (FQDN), включая подстановочные. Эта функция не требует завершения SSL-запросов. Рекомендуется использовать [теги полного доменного имени брандмауэра Azure](https://docs.microsoft.com/azure/firewall/fqdn-tags) для обновлений Windows, а также включить сетевой трафик для конечных точек центр обновления Windows Майкрософт, которые могут проходить через брандмауэр. [Развертывание брандмауэра Azure с помощью шаблона](https://docs.microsoft.com/azure/firewall/deploy-template) содержит образец для определения шаблона ресурсов Microsoft. Network/азурефиреваллс. Правила брандмауэра, общие для Service Fabric приложений, позволяют выполнять следующие действия для виртуальной сети кластеров:

- \* download.microsoft.com
- \* servicefabric.azure.com
- *.core.windows.net

Эти правила брандмауэра дополняют разрешенные исходящие группы безопасности сети, которые включают ServiceFabric и хранилище в качестве разрешенных целевых объектов из виртуальной сети.

## <a name="tls-12"></a>TLS 1.2
[тсг](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides/blob/master/Security/TLS%20Configuration.md)

## <a name="windows-defender"></a>Защитник Windows 

По умолчанию антивирус Защитника Windows установлен на Windows Server 2016. Дополнительные сведения см. в статье [Windows Defender Antivirus on Windows Server 2016](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-on-windows-server-2016) (Антивирусная программа "Защитник Windows" в Windows Server 2016). Пользовательский интерфейс установлен по умолчанию на некоторых номерах SKU, но не является обязательным. Для снижения влияния Защитника Windows на производительность и потребление ресурсов, а также если политики безопасности позволяют исключить процессы и пути для программного обеспечения с открытым кодом, объявите следующие свойства шаблона расширения Resource Manager для масштабируемого набора виртуальных машин, чтобы исключить кластер Service Fabric из сканирования.


```json
 {
    "name": "[concat('VMIaaSAntimalware','_vmNodeType0Name')]",
    "properties": {
        "publisher": "Microsoft.Azure.Security",
        "type": "IaaSAntimalware",
        "typeHandlerVersion": "1.5",
        "settings": {
            "AntimalwareEnabled": "true",
            "Exclusions": {
                "Paths": "[concat(parameters('svcFabData'), ';', parameters('svcFabLogs'), ';', parameters('svcFabRuntime'))]",
                "Processes": "Fabric.exe;FabricHost.exe;FabricInstallerService.exe;FabricSetup.exe;FabricDeployer.exe;ImageBuilder.exe;FabricGateway.exe;FabricDCA.exe;FabricFAS.exe;FabricUOS.exe;FabricRM.exe;FileStoreService.exe"
            },
            "RealtimeProtectionEnabled": "true",
            "ScheduledScanSettings": {
                "isEnabled": "true",
                "scanType": "Quick",
                "day": "7",
                "time": "120"
            }
        },
        "protectedSettings": null
    }
}
```

> [!NOTE]
> Если вы не используете Защитник Windows, обратитесь к документации по работе с антивредоносным ПО, чтобы ознакомиться с правилами настройки. Защитник Windows не поддерживается в Linux.

## <a name="platform-isolation"></a>Изоляция платформ
По умолчанию Service Fabric приложениям предоставляется доступ к самой среде выполнения Service Fabric, которая сама по себе проявляется в разных формах: [переменные среды](service-fabric-environment-variables-reference.md) , указывающие на пути к файлам на узле, соответствующие файлам приложения и структуры, конечную точку межпроцессного обмена данными, которая принимает запросы конкретного приложения, а также сертификат клиента, который будет использоваться приложением для проверки подлинности. В конечном итоге, если служба размещает недоверенный код, рекомендуется отключить этот доступ к среде выполнения SF, если это не требуется явным образом. Доступ к среде выполнения удаляется с помощью следующего объявления в разделе "политики" манифеста приложения: 

```xml
<ServiceManifestImport>
    <Policies>
        <ServiceFabricRuntimeAccessPolicy RemoveServiceFabricRuntimeAccess="true"/>
    </Policies>
</ServiceManifestImport>

```

## <a name="next-steps"></a>Дальнейшие действия

* Создайте кластер на виртуальных машинах или компьютерах под управлением Windows Server: [Service Fabric создания кластера для Windows Server](service-fabric-cluster-creation-for-windows-server.md).
* Создание кластера на виртуальных машинах или компьютерах под управлением Linux: [Создание кластера Linux](service-fabric-cluster-creation-via-portal.md).
* Узнайте о [вариантах поддержки Service Fabric](service-fabric-support.md).

[Image1]: ./media/service-fabric-best-practices/generate-common-name-cert-portal.png
