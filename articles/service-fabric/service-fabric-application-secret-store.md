---
title: Azure сервис Ткань Центральный секреты магазин
description: В этой статье описывается, как использовать Central Secrets Store в azure Service Fabric.
ms.topic: conceptual
ms.date: 07/25/2019
ms.openlocfilehash: 11fb94a9fba40e6f2474ad64f5eb0c454be28ca0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77589170"
---
# <a name="central-secrets-store-in-azure-service-fabric"></a>Центральный магазин секретов в лазурном сервисе Ткань 
В этой статье описывается, как использовать Central Secrets Store (CSS) в Azure Service Fabric для создания секретов в приложениях Service Fabric. CSS — это локальный секретный кэш хранилища, который хранит конфиденциальные данные, такие как пароль, маркеры и ключи, зашифрованные в памяти.

## <a name="enable-central-secrets-store"></a>Включить центральный магазин секретов
Добавьте следующий скрипт `fabricSettings` в конфигурацию кластера под включить CSS. Мы рекомендуем использовать сертификат, отличный от кластерного сертификата для CSS. Убедитесь, что сертификат шифрования установлен `NetworkService` на всех узлах и который считывает разрешение на личный ключ сертификата.
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
## <a name="declare-a-secret-resource"></a>Объявить секретный ресурс
Вы можете создать секретный ресурс, используя шаблон Azure Resource Manager или API REST.

### <a name="use-resource-manager"></a>Использование Resource Manager

Используйте следующий шаблон для использования ресурса Manager для создания секретного ресурса. Шаблон создает `supersecret` секретный ресурс, но значение для секретного ресурса пока не установлено.


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

Чтобы создать `supersecret` секретный ресурс с помощью REST API, сделайте запрос `https://<clusterfqdn>:19080/Resources/Secrets/supersecret?api-version=6.4-preview`PUT. Для создания секретного ресурса нужен сертификат кластера или сертификат клиента админа.

```powershell
$json = '{"properties": {"kind": "inlinedValue", "contentType": "text/plain", "description": "supersecret"}}'
Invoke-WebRequest  -Uri https://<clusterfqdn>:19080/Resources/Secrets/supersecret?api-version=6.4-preview -Method PUT -CertificateThumbprint <CertThumbprint> -Body $json
```

## <a name="set-the-secret-value"></a>Установите секретное значение

### <a name="use-the-resource-manager-template"></a>Использование шаблона менеджера ресурсов

Используйте следующий шаблон менеджера ресурсов для создания и установки секретного значения. Этот шаблон устанавливает секретное `supersecret` значение для `ver1`секретного ресурса в качестве версии.
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

Используйте следующий скрипт, чтобы использовать API REST для настройки секретного значения.
```powershell
$Params = '{"properties": {"value": "mysecretpassword"}}'
Invoke-WebRequest -Uri https://<clusterfqdn>:19080/Resources/Secrets/supersecret/values/ver1?api-version=6.4-preview -Method PUT -Body $Params -CertificateThumbprint <ClusterCertThumbprint>
```
### <a name="examine-the-secret-value"></a>Изучите секретное значение
```powershell
Invoke-WebRequest -CertificateThumbprint <ClusterCertThumbprint> -Method POST -Uri "https:<clusterfqdn>/Resources/Secrets/supersecret/values/ver1/list_value?api-version=6.4-preview"
```
## <a name="use-the-secret-in-your-application"></a>Используйте секрет в приложении

Выполните следующие действия, чтобы использовать секрет в приложении Service Fabric.

1. Добавьте раздел в файл **settings.xml** со следующим фрагментом. Обратите внимание, что значение находится`secretname:version`в формате ..

   ```xml
     <Section Name="testsecrets">
      <Parameter Name="TopSecret" Type="SecretsStoreRef" Value="supersecret:ver1"/
     </Section>
   ```

1. Импортируйте раздел в **ApplicationManifest.xml**.
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

   Переменная `SecretPath` среды укажет на каталог, где хранятся все секреты. Каждый параметр, указанный в `testsecrets` разделе, хранится в отдельном файле. Приложение теперь может использовать секрет следующим образом:
   ```C#
   secretValue = IO.ReadFile(Path.Join(Environment.GetEnvironmentVariable("SecretPath"),  "TopSecret"))
   ```
1. Установите секреты в контейнер. Единственное изменение, необходимое, чтобы сделать `specify` секреты `<ConfigPackage>`доступны внутри контейнера является точка крепления в .
Следующим фрагментом является модифицированный **ApplicationManifest.xml**.  

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
   Секреты доступны под точкой крепления внутри контейнера.

1. Вы можете связать секрет с переменной `Type='SecretsStoreRef`среды процесса, указав. Следующий фрагмент является примером того, `supersecret` как `ver1` связать версию с переменной `MySuperSecret` среды в **ServiceManifest.xml**.

   ```xml
   <EnvironmentVariables>
     <EnvironmentVariable Name="MySuperSecret" Type="SecretsStoreRef" Value="supersecret:ver1"/>
   </EnvironmentVariables>
   ```

## <a name="next-steps"></a>Дальнейшие действия
Подробнее о [безопасности приложений и сервиса.](service-fabric-application-and-service-security.md)
