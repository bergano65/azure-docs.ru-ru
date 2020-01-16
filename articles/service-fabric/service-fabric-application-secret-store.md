---
title: Хранилище секретов Azure Service Fabric Central
description: В этой статье описывается, как использовать центральное хранилище секретов в Azure Service Fabric.
ms.topic: conceptual
ms.date: 07/25/2019
ms.openlocfilehash: bc6ea6260bf50d5b4f8e294e0a3827426f90bee3
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/15/2020
ms.locfileid: "75980942"
---
# <a name="central-secrets-store-in-azure-service-fabric"></a>Центральное хранилище секретов в Azure Service Fabric 
В этой статье описывается, как использовать центральное хранилище секретов (CSS) в Service Fabric Azure для создания секретов в Service Fabric приложениях. CSS — это локальный кэш хранилища секретов, в котором хранятся конфиденциальные данные, такие как пароль, маркеры и ключи, зашифрованные в памяти.

## <a name="enable-central-secrets-store"></a>Включить центральное хранилище секретов
Добавьте следующий скрипт в конфигурацию кластера в разделе `fabricSettings`, чтобы включить CSS. Для CSS рекомендуется использовать сертификат, отличный от сертификата кластера. Убедитесь, что сертификат шифрования установлен на всех узлах и у `NetworkService` есть разрешение на чтение закрытого ключа сертификата.
  ```json
    "fabricSettings": 
    [
        ...
    {
        "name":  "CentralSecretService",
        "parameters":  [
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
     }
        ...
     ]
```
## <a name="declare-a-secret-resource"></a>Объявление секретного ресурса
Секретный ресурс можно создать с помощью шаблона Azure Resource Manager или REST API.

### <a name="use-resource-manager"></a>Использование диспетчер ресурсов

Используйте следующий шаблон, чтобы использовать диспетчер ресурсов для создания секретного ресурса. Шаблон создает `supersecret` секретный ресурс, но пока значение для секретного ресурса еще не задано.


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

### <a name="use-the-rest-api"></a>Использование REST API

Чтобы создать `supersecret` секретный ресурс с помощью REST API, выполните запрос на размещение `https://<clusterfqdn>:19080/Resources/Secrets/supersecret?api-version=6.4-preview`. Для создания секретного ресурса необходим сертификат кластера или сертификат клиента администратора.

```powershell
Invoke-WebRequest  -Uri https://<clusterfqdn>:19080/Resources/Secrets/supersecret?api-version=6.4-preview -Method PUT -CertificateThumbprint <CertThumbprint>
```

## <a name="set-the-secret-value"></a>Установка значения секрета

### <a name="use-the-resource-manager-template"></a>Использование шаблона диспетчер ресурсов

Используйте следующий шаблон диспетчер ресурсов, чтобы создать и задать значение секрета. Этот шаблон задает значение секрета для `supersecret` секретного ресурса как версия `ver1`.
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
### <a name="use-the-rest-api"></a>Использование REST API

Используйте следующий скрипт, чтобы использовать REST API для задания значения секрета.
```powershell
$Params = @{"properties": {"value": "mysecretpassword"}}
Invoke-WebRequest -Uri https://<clusterfqdn>:19080/Resources/Secrets/supersecret/values/ver1?api-version=6.4-preview -Method PUT -Body $Params -CertificateThumbprint <ClusterCertThumbprint>
```
## <a name="use-the-secret-in-your-application"></a>Использование секрета в приложении

Выполните следующие действия, чтобы использовать секрет в приложении Service Fabric.

1. Добавьте раздел в файл **Settings. XML** с помощью следующего фрагмента кода. Обратите внимание, что значение имеет формат {`secretname:version`}.

   ```xml
     <Section Name="testsecrets">
      <Parameter Name="TopSecret" Type="SecretsStoreRef" Value="supersecret:ver1"/
     </Section>
   ```

1. Импортируйте раздел в **ApplicationManifest. XML**.
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

   Переменная среды `SecretPath` будет указывать на каталог, где хранятся все секреты. Каждый параметр, указанный в разделе `testsecrets`, хранится в отдельном файле. Теперь приложение может использовать секрет, как показано ниже.
   ```C#
   secretValue = IO.ReadFile(Path.Join(Environment.GetEnvironmentVariable("SecretPath"),  "TopSecret"))
   ```
1. Подключите секреты к контейнеру. Единственное изменение, необходимое для предоставления секретов в контейнере, заключается в `specify` точки подключения в `<ConfigPackage>`.
Следующий фрагмент кода является модифицированным **ApplicationManifest. XML**.  

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
   Секреты доступны в точке подключения внутри контейнера.

1. Можно привязать секрет к переменной среды процесса, указав `Type='SecretsStoreRef`. В следующем фрагменте кода приведен пример привязки `supersecret` версии `ver1` к переменной среды `MySuperSecret` в **ServiceManifest. XML**.

   ```xml
   <EnvironmentVariables>
     <EnvironmentVariable Name="MySuperSecret" Type="SecretsStoreRef" Value="supersecret:ver1"/>
   </EnvironmentVariables>
   ```

## <a name="next-steps"></a>Дальнейшие действия
Дополнительные сведения о [безопасности приложений и служб](service-fabric-application-and-service-security.md).
