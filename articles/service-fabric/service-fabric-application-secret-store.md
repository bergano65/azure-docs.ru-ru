---
title: Service Fabricное хранилище секретов | Документация Майкрософт
description: В этой статье описывается, как использовать хранилище секретов Service Fabric.
services: service-fabric
author: athinanthny
ms.service: service-fabric
ms.topic: conceptual
ms.date: 07/25/2019
ms.author: atsenthi
ms.openlocfilehash: 5315a8806f45e40204e8500e97c3440bfa9ab8b2
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/14/2019
ms.locfileid: "74077353"
---
#  <a name="service-fabric-secrets-store"></a>Service Fabricное хранилище секретов
В этой статье описывается создание и использование секретов в Service Fabric приложениях с помощью хранилища секретов (CSS) Service Fabric. CSS — это локальный кэш хранилища секретов, используемый для хранения конфиденциальных данных, таких как пароль, маркеры и ключи, зашифрованные в памяти.

## <a name="enabling-secrets-store"></a>Включение хранилища секретов
 Добавьте приведенный ниже код в конфигурацию кластера в разделе `fabricSettings`, чтобы включить CSS. Для CSS рекомендуется использовать сертификат, отличный от сертификата кластера. Убедитесь, что сертификат шифрования установлен на всех узлах, а `NetworkService` имеет разрешение на чтение закрытого ключа сертификата.
  ```json
    "fabricSettings": 
    [
        ...
    {
        "parameters":  [
            "name":  "CentralSecretService"
                {
                    "name":  "IsEnabled",
                    "value":  "true"
                },
                {
                    "name":  "MinReplicaSetSize",
                    "value":  "3"
                },
                {
                    "name":  "TargetReplicaSetSize",
                    "value":  "3"
                },
                 {
                    "name" : "EncryptionCertificateThumbprint",
                    "value": "<thumbprint>"
                 }
                ,
                ],
            },
            ]
```
## <a name="declare-secret-resource"></a>Объявить секретный ресурс
Вы можете создать секретный ресурс либо с помощью шаблона диспетчер ресурсов, либо с помощью REST API.

* Шаблон Resource Manager
```json
   "resources": [
      {
        "apiVersion": "2018-07-01-preview",
        "name": "supersecret",
        "type": "Microsoft.ServiceFabricMesh/secrets",
        "location": "[parameters('location')]", 
        "dependsOn": [],
        "properties": {
          "kind": "inlinedValue",
            "description": "Application Secret",
            "contentType": "text/plain",
          }
        }
      ]
```
Указанный выше шаблон создает `supersecret` секретный ресурс, но пока значение для секретного ресурса еще не задано.

* Использование REST API

Чтобы создать секретный ресурс, `supersecret` сделать запрос на размещение `https://<clusterfqdn>:19080/Resources/Secrets/supersecret?api-version=6.4-preview`. Для создания секрета необходим сертификат кластера или сертификат клиента администратора.

```powershell
Invoke-WebRequest  -Uri https://<clusterfqdn>:19080/Resources/Secrets/supersecret?api-version=6.4-preview -Method PUT -CertificateThumbprint <CertThumbprint>
```

## <a name="set-secret-value"></a>Задать значение секрета
* Шаблон Resource Manager

В приведенном ниже шаблоне диспетчер ресурсов создается и задается значение для секретного `supersecret` с версией `ver1`.
```json
  {
  "parameters": {
  "supersecret": {
      "type": "string",
      "metadata": {
        "description": "supersecret value"
      }
   }
  },
  "resources": [
    {
      "apiVersion": "2018-07-01-preview",
        "name": "supersecret",
        "type": "Microsoft.ServiceFabricMesh/secrets",
        "location": "[parameters('location')]", 
        "dependsOn": [],
        "properties": {
          "kind": "inlinedValue",
            "description": "Application Secret",
            "contentType": "text/plain",
        }
    },
    {
      "apiVersion": "2018-07-01-preview",
      "name": "supersecret/ver1",
      "type": "Microsoft.ServiceFabricMesh/secrets/values",
      "location": "[parameters('location')]",
      "dependsOn": [
        "Microsoft.ServiceFabricMesh/secrets/supersecret"
      ],
      "properties": {
        "value": "[parameters('supersecret')]"
      }
    }
  ],
  ```
* Использование REST API

```powershell
$Params = @{"properties": {"value": "mysecretpassword"}}
Invoke-WebRequest -Uri https://<clusterfqdn>:19080/Resources/Secrets/supersecret/values/ver1?api-version=6.4-preview -Method PUT -Body $Params -CertificateThumbprint <ClusterCertThumbprint>
```
## <a name="using-the-secret-in-your-application"></a>Использование секрета в приложении

1.  Добавьте раздел в файл Settings. XML с приведенным ниже содержимым. Обратите внимание, что значение имеет формат {`secretname:version`}.

```xml
  <Section Name="testsecrets">
   <Parameter Name="TopSecret" Type="SecretsStoreRef" Value="supersecret:ver1"/
  </Section>
```
2. Теперь импортируйте раздел в ApplicationManifest. XML.
```xml
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="testservicePkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <ConfigPackagePolicies CodePackageRef="Code">
        <ConfigPackage Name="Config" SectionName="testsecrets" EnvironmentVariableName="SecretPath" />
        </ConfigPackagePolicies>
    </Policies>
  </ServiceManifestImport>
```

Переменная среды "Секретпас" будет указывать на каталог, где хранятся все секреты. Каждый параметр, указанный в разделе `testsecrets` раздела, будет храниться в отдельном файле. Теперь приложение может использовать секрет, как показано ниже.
```C#
secretValue = IO.ReadFile(Path.Join(Environment.GetEnvironmentVariable("SecretPath"),  "TopSecret"))
```
3. Подключение секретов к контейнеру

Только изменение, необходимое для обеспечения доступности секретов в контейнере, заключается в том, чтобы указать точку подключения в `<ConfigPackage>`.
Изменен ApplicationManifest. XML  

```xml
<ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="testservicePkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <ConfigPackagePolicies CodePackageRef="Code">
        <ConfigPackage Name="Config" SectionName="testsecrets" MountPoint="C:\secrets" EnvironmentVariableName="SecretPath" />
        <!-- Linux Container
         <ConfigPackage Name="Config" SectionName="testsecrets" MountPoint="/mnt/secrets" EnvironmentVariableName="SecretPath" />
        -->
      </ConfigPackagePolicies>
    </Policies>
  </ServiceManifestImport>
```
Секреты будут доступны в точке подключения внутри контейнера.

4. Привязка секрета к переменной среды 

Можно привязать секрет к переменной среды процесса, указав Type = ' Секретссторереф '. Ниже приведен пример привязки `supersecret` версии `ver1` к переменной среды `MySuperSecret` в ServiceManifest. XML.

```xml
<EnvironmentVariables>
  <EnvironmentVariable Name="MySuperSecret" Type="SecretsStoreRef" Value="supersecret:ver1"/>
</EnvironmentVariables>
```
## <a name="next-steps"></a>Дополнительная информация
Ознакомьтесь с дополнительными сведениями о [безопасности приложений и служб](service-fabric-application-and-service-security.md).