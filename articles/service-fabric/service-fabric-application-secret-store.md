---
title: Хранилище секретов Azure Service Fabric Central
description: В этой статье описывается, как использовать центральное хранилище секретов в Azure Service Fabric.
ms.topic: conceptual
ms.date: 07/25/2019
ms.openlocfilehash: 4087e7ccdcb2281c4a08af155d35a10c66147a85
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81770411"
---
# <a name="central-secrets-store-in-azure-service-fabric"></a>Центральное хранилище секретов в Azure Service Fabric 
В этой статье описывается, как использовать центральное хранилище секретов (CSS) в Service Fabric Azure для создания секретов в Service Fabric приложениях. CSS — это локальный кэш хранилища секретов, в котором хранятся конфиденциальные данные, такие как пароль, маркеры и ключи, зашифрованные в памяти.

## <a name="enable-central-secrets-store"></a>Включить центральное хранилище секретов
Добавьте следующий скрипт в конфигурацию кластера в разделе `fabricSettings` , чтобы включить CSS. Для CSS рекомендуется использовать сертификат, отличный от сертификата кластера. Убедитесь, что сертификат шифрования установлен на всех узлах и `NetworkService` имеет разрешение на чтение закрытого ключа сертификата.
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
Вы можете создать секретный ресурс с помощью REST API.
  > [!NOTE] 
  > Если кластер использует проверку подлинности Windows, запрос на ОСТАВШУЮся отсылается по незащищенному каналу HTTP. Рекомендуется использовать кластер на основе X509 с защищенными конечными точками.

Чтобы создать `supersecret` секретный ресурс с помощью REST API, выполните запрос на размещение `https://<clusterfqdn>:19080/Resources/Secrets/supersecret?api-version=6.4-preview`. Для создания секретного ресурса необходим сертификат кластера или сертификат клиента администратора.

```powershell
$json = '{"properties": {"kind": "inlinedValue", "contentType": "text/plain", "description": "supersecret"}}'
Invoke-WebRequest  -Uri https://<clusterfqdn>:19080/Resources/Secrets/supersecret?api-version=6.4-preview -Method PUT -CertificateThumbprint <CertThumbprint> -Body $json
```

## <a name="set-the-secret-value"></a>Установка значения секрета

Используйте следующий скрипт, чтобы использовать REST API для задания значения секрета.
```powershell
$Params = '{"properties": {"value": "mysecretpassword"}}'
Invoke-WebRequest -Uri https://<clusterfqdn>:19080/Resources/Secrets/supersecret/values/ver1?api-version=6.4-preview -Method PUT -Body $Params -CertificateThumbprint <ClusterCertThumbprint>
```
### <a name="examine-the-secret-value"></a>Проверка значения секрета
```powershell
Invoke-WebRequest -CertificateThumbprint <ClusterCertThumbprint> -Method POST -Uri "https:<clusterfqdn>/Resources/Secrets/supersecret/values/ver1/list_value?api-version=6.4-preview"
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

   Переменная `SecretPath` среды будет указывать на каталог, в котором хранятся все секреты. Каждый параметр, `testsecrets` указанный в разделе, хранится в отдельном файле. Теперь приложение может использовать секрет, как показано ниже.
   ```C#
   secretValue = IO.ReadFile(Path.Join(Environment.GetEnvironmentVariable("SecretPath"),  "TopSecret"))
   ```
1. Подключите секреты к контейнеру. Единственное изменение, необходимое для того, чтобы сделать секреты доступными внутри контейнера, `specify` — это точка подключения `<ConfigPackage>`в.
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

1. Можно привязать секрет к переменной среды процесса, указав `Type='SecretsStoreRef`. В следующем фрагменте кода приведен пример `supersecret` привязки версии `ver1` к переменной `MySuperSecret` среды в **ServiceManifest. XML**.

   ```xml
   <EnvironmentVariables>
     <EnvironmentVariable Name="MySuperSecret" Type="SecretsStoreRef" Value="supersecret:ver1"/>
   </EnvironmentVariables>
   ```

## <a name="next-steps"></a>Дальнейшие шаги
Дополнительные сведения о [безопасности приложений и служб](service-fabric-application-and-service-security.md).
