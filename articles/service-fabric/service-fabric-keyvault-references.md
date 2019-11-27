---
title: Service Fabric Azure. Использование Service Fabric приложений KeyVault ссылки | Документация Майкрософт
description: В этой статье объясняется, как использовать поддержку Кэйваултреференце Service-Fabric для секретов приложений.
services: service-fabric
author: athinanthny
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.date: 09/20/2019
ms.author: atsenthi
ms.openlocfilehash: 96da89a00b054767553b0ed3d8debf30c344dd62
ms.sourcegitcommit: 8a2949267c913b0e332ff8675bcdfc049029b64b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/21/2019
ms.locfileid: "74307337"
---
#  <a name="keyvaultreference-support-for-service-fabric-applications-preview"></a>Поддержка Кэйваултреференце приложений для Service Fabric (Предварительная версия)

Распространенной проблемой при создании облачных приложений является безопасное хранение секретов, необходимых для приложения. Например, может потребоваться сохранить учетные данные репозитория контейнеров в keyvault и ссылаться на них в манифесте приложения. Service Fabric Кэйваултреференце использует Service Fabric управляемое удостоверение и упрощает ссылку на секреты keyvault. В оставшейся части этой статьи подробно описано, как использовать Service Fabric Кэйваултреференце, а также некоторые типичные способы их использования.

## <a name="prerequisites"></a>предварительным требованиям

- Управляемое удостоверение для приложения (MIT)
    
    Service Fabric поддержка Кэйваултреференце использует управляемое удостоверение приложения, поэтому приложения, планирующие использовать Кэйваултреференцес, должны использовать управляемое удостоверение. Следуйте этому [документу](concepts-managed-identity.md) , чтобы включить управляемое удостоверение для приложения.

- Центральное хранилище секретов (CSS).

    Центральное хранилище секретов (CSS) — это зашифрованный локальный кэш секретов Service Fabric, Кэйваултреференце после получения в кэш CSS.

    Добавьте приведенный ниже элемент в конфигурацию кластера в разделе `fabricSettings`, чтобы включить все необходимые компоненты для поддержки Кэйваултреференце.

    ```json
    "fabricSettings": 
    [
        ...
    {
        "parameters":  [
            "name":  "CentralSecretService",
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
                }
                ],
            },
            {
                "name":  "ManagedIdentityTokenService",
                "parameters":  [
                {
                    "name":  "IsEnabled",
                    "value":  "true"
                }
                ]
            }
            ]
    ```

    > [!NOTE] 
    > Рекомендуется использовать отдельный сертификат шифрования для CSS. Его можно добавить в раздел "Централсекретсервице".

    ```json
        {
            "name": "EncryptionCertificateThumbprint",
            "value": "<EncryptionCertificateThumbprint for CSS>"
        }
    ```

- Предоставление управляемому удостоверению приложения разрешения на доступ к keyvault

    Обратитесь к этому [документу](how-to-grant-access-other-resources.md) , чтобы узнать, как предоставить управляемому удостоверению доступ к keyvault. Также обратите внимание, что при использовании управляемого удостоверения, назначенного системой, управляемое удостоверение создается только после развертывания приложения.

## <a name="keyvault-secret-as-application-parameter"></a>Keyvault секрет в качестве параметра приложения
Предположим, что приложению необходимо считать пароль к серверной базе данных, хранящийся в keyvault, Service Fabric поддержка Кэйваултреференце упрощает эту задачу. Ниже приведен пример, считывающий `DBPassword` секрет из keyvault с помощью поддержки Service Fabric Кэйваултреференце.

- Добавление раздела в файл Settings. XML

    Определите `DBPassword` параметр с типом `KeyVaultReference` и значением `<KeyVaultURL>`

    ```xml
    <Section Name="dbsecrets">
        <Parameter Name="DBPassword" Type="KeyVaultReference" Value="https://vault200.vault.azure.net/secrets/dbpassword/8ec042bbe0ea4356b9b171588a8a1f32"/>
    </Section>
    ```
- Сослаться на новый раздел в файле ApplicationManifest. XML в `<ConfigPackagePolicies>`

    ```xml
    <ServiceManifestImport>
        <Policies>
        <IdentityBindingPolicy ServiceIdentityRef="WebAdmin" ApplicationIdentityRef="ttkappuser" />
        <ConfigPackagePolicies CodePackageRef="Code">
            <!--Linux container example-->
            <ConfigPackage Name="Config" SectionName="dbsecrets" EnvironmentVariableName="SecretPath" MountPoint="/var/secrets"/>
            <!--Windows container example-->
            <!-- <ConfigPackage Name="Config" SectionName="dbsecrets" EnvironmentVariableName="SecretPath" MountPoint="C:\secrets"/> -->
        </ConfigPackagePolicies>
        </Policies>
    </ServiceManifestImport>
    ```

- Использование Кэйваултреференце в приложении

    Service Fabric при создании экземпляра службы будет разрешать параметр Кэйваултреференце с помощью управляемого удостоверения приложения. Каждый параметр, указанный в разделе `<Section  Name=dbsecrets>`, будет файлом в папке, на которую указывает EnvironmentVariable Секретпас. Ниже C# приведен фрагмент кода, демонстрирующий чтение дбпассворд в приложении.

    ```C#
    string secretPath = Environment.GetEnvironmentVariable("SecretPath");
    using (StreamReader sr = new StreamReader(Path.Combine(secretPath, "DBPassword"))) 
    {
        string dbPassword =  sr.ReadToEnd();
        // dbPassword to connect to DB
    }
    ```
    > [!NOTE] 
    > Для сценария контейнера точку подключения можно использовать для управления тем, где будет монтирован `secrets`.

## <a name="keyvault-secret-as-environment-variable"></a>Keyvault секрет в качестве переменной среды

Service Fabric переменные среды теперь поддерживают тип Кэйваултреференце. в приведенном ниже примере показано, как привязать переменную среды к секрету, хранящемуся в KeyVault.

```xml
<EnvironmentVariables>
      <EnvironmentVariable Name="EventStorePassword" Type="KeyVaultReference" Value="https://ttkvault.vault.azure.net/secrets/clustercert/e225bd97e203430d809740b47736b9b8"/>
</EnvironmentVariables>
```

```C#
string eventStorePassword =  Environment.GetEnvironmentVariable("EventStorePassword");
```
## <a name="keyvault-secret-as-container-repository-password"></a>Keyvault секрета как пароль репозитория контейнеров
Кэйваултреференце является поддерживаемым типом для контейнера Репоситорикредентиалс. в примере ниже показано, как использовать ссылку keyvault в качестве пароля репозитория контейнеров.
```xml
 <Policies>
      <ContainerHostPolicies CodePackageRef="Code">
        <RepositoryCredentials AccountName="user1" Type="KeyVaultReference" Password="https://ttkvault.vault.azure.net/secrets/containerpwd/e225bd97e203430d809740b47736b9b8"/>
      </ContainerHostPolicies>
```
## <a name="faq"></a>часто задаваемые вопросы
- Для поддержки Кэйваултреференце необходимо включить управляемое удостоверение. Активация приложения завершится ошибкой, если Кэйваултреференце используется без включения управляемого удостоверения.

- Если вы используете назначенное системой удостоверение, оно создается только после развертывания приложения и создает циклическую зависимость. После развертывания приложения можно предоставить системному назначению разрешение на доступ к удостоверениям keyvault. Удостоверение, назначенное системой, можно найти по имени {Cluster}/{аппликатион имя}/{сервиценаме}

- Keyvault должен находиться в той же подписке, что и кластер Service Fabric. 

## <a name="next-steps"></a>Дополнительная информация

* [Документация по Azure KeyVault](https://docs.microsoft.com/azure/key-vault/)
