---
title: Azure сервис Ткань Центральный секреты магазин
description: В этой статье описывается, как использовать Central Secrets Store в azure Service Fabric.
ms.topic: conceptual
ms.date: 07/25/2019
ms.openlocfilehash: 4087e7ccdcb2281c4a08af155d35a10c66147a85
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770411"
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
Вы можете создать секретный ресурс с помощью REST API.
  > [!NOTE] 
  > Если кластер использует аутентификацию окон, запрос REST отправляется через незащищенный канал HTTP. Рекомендация заключается в использовании кластера на основе X509 с безопасными конечными точками.

Чтобы создать `supersecret` секретный ресурс с помощью REST API, сделайте запрос `https://<clusterfqdn>:19080/Resources/Secrets/supersecret?api-version=6.4-preview`PUT. Для создания секретного ресурса нужен сертификат кластера или сертификат клиента админа.

```powershell
$json = '{"properties": {"kind": "inlinedValue", "contentType": "text/plain", "description": "supersecret"}}'
Invoke-WebRequest  -Uri https://<clusterfqdn>:19080/Resources/Secrets/supersecret?api-version=6.4-preview -Method PUT -CertificateThumbprint <CertThumbprint> -Body $json
```

## <a name="set-the-secret-value"></a>Установите секретное значение

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

## <a name="next-steps"></a>Следующие шаги
Подробнее о [безопасности приложений и сервиса.](service-fabric-application-and-service-security.md)
