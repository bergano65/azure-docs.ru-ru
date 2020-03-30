---
title: Ткань службы Azure - Использование ссылок на приложение KeyVault с помощью сервиса
description: В этой статье объясняется, как использовать сервис-ткань KeyVaultReference поддержки для приложений секреты.
ms.topic: article
ms.date: 09/20/2019
ms.openlocfilehash: f7d8a083ea5ec4b66c29d392ee98927915465875
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76545489"
---
#  <a name="keyvaultreference-support-for-service-fabric-applications-preview"></a>Поддержка KeyVaultReference для приложений Service Fabric (предварительный просмотр)

Общей проблемой при создании облачных приложений является безопасное хранение секретов, необходимых для вашего приложения. Например, можно сохранить учетные данные репозитория контейнера в keyvault и ссылаться на них в манифесте приложения. Сервис Fabric KeyVaultReference использует сервисную идентификацию Fabric Managed Identity и позволяет легко ссылаться на секреты keyvault. В остальной части этой статьи подробно описано, как использовать Service Fabric KeyVaultReference и включает в себя некоторые типичные использования.

## <a name="prerequisites"></a>Предварительные требования

- Управляемая идентификация для применения (MIT)
    
    Служба Fabric KeyVaultReference поддержка использует управляемой идентичности приложения и, следовательно, приложения, планируя использовать KeyVaultСсылки следует использовать управляемый identity. Следуйте этому [документу,](concepts-managed-identity.md) чтобы включить управляемую идентификацию для вашего приложения.

- Центральный магазин секретов (CSS).

    Central Secrets Store (CSS) — зашифрованный кэш локальных секретов Service Fabric. CSS — это локальный секретный кэш хранилища, который хранит конфиденциальные данные, такие как пароль, маркеры и ключи, зашифрованные в памяти. KeyVaultReference, после того, как принес, кэшируются в CSS.

    Добавьте ниже к конфигурации кластера, `fabricSettings` чтобы включить все необходимые функции для поддержки KeyVaultReference.

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
                }
                ]
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
    > Рекомендуется использовать отдельный сертификат шифрования для CSS. Вы можете добавить его в разделе "ЦентральнаяСекретСервис".
    

    ```json
        {
            "name": "EncryptionCertificateThumbprint",
            "value": "<EncryptionCertificateThumbprint for CSS>"
        }
    ```
Для того, чтобы изменения вступили в силу, необходимо также изменить политику обновления, чтобы указать принудительное перезапуск времени выполнения service Fabric на каждом узлах по мере прохождения обновления через кластер. Этот перезапуск гарантирует запуск и работу недавно включенной системы на каждом узлах. В фрагменте ниже, forceRestart является существенной настройки; использовать существующие значения для оставшейся части настроек.
```json
"upgradeDescription": {
    "forceRestart": true,
    "healthCheckRetryTimeout": "00:45:00",
    "healthCheckStableDuration": "00:05:00",
    "healthCheckWaitDuration": "00:05:00",
    "upgradeDomainTimeout": "02:00:00",
    "upgradeReplicaSetCheckTimeout": "1.00:00:00",
    "upgradeTimeout": "12:00:00"
}
```
- Разрешение на доступ к управляемому идентификационным данным приложения для keyvault

    Справка к этому [документу,](how-to-grant-access-other-resources.md) чтобы узнать, как предоставить управляемый доступ к ключу. Также обратите внимание, что если вы используете Систему Назначенный Управляемый Identity, управляемое удостоверение создается только после развертывания приложения.

## <a name="keyvault-secret-as-application-parameter"></a>Секрет Keyvault как параметр приложения
Допустим, приложение должно прочитать пароль базы данных бэкэнда, хранящийся в keyvault, служба Fabric KeyVaultReference поддержки делает его легким. Ниже приведен `DBPassword` пример читает секрет от keyvault с помощью службы Fabric KeyVaultReference поддержки.

- Добавьте раздел в настройки.xml

    Определение `DBPassword` параметра `KeyVaultReference` с типом и значением`<KeyVaultURL>`

    ```xml
    <Section Name="dbsecrets">
        <Parameter Name="DBPassword" Type="KeyVaultReference" Value="https://vault200.vault.azure.net/secrets/dbpassword/8ec042bbe0ea4356b9b171588a8a1f32"/>
    </Section>
    ```
- Ссылка на новый раздел в ApplicationManifest.xml в`<ConfigPackagePolicies>`

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

- Использование KeyVaultReference в приложении

    СервисFabric на мгновенное обслуживание будет решать KeyVaultReference Параметр с помощью управляемого удостоверения приложения. Каждый параметр, перечисленный в `<Section  Name=dbsecrets>` соответствии будет файл под папку указал на EnvironmentVariable SecretPath. Ниже фрагмент кода C'S показать, как читать DBPassword в вашем приложении.

    ```C#
    string secretPath = Environment.GetEnvironmentVariable("SecretPath");
    using (StreamReader sr = new StreamReader(Path.Combine(secretPath, "DBPassword"))) 
    {
        string dbPassword =  sr.ReadToEnd();
        // dbPassword to connect to DB
    }
    ```
    > [!NOTE] 
    > Для контейнерного сценария можно использовать MountPoint для `secrets` управления, где будет установлен.

## <a name="keyvault-secret-as-environment-variable"></a>Секрет Keyvault как переменная окружающей среды

Переменные среды Service Fabric теперь поддерживают тип KeyVaultReference, ниже показано, как связать переменную среды с секретом, хранящимся в KeyVault.

```xml
<EnvironmentVariables>
      <EnvironmentVariable Name="EventStorePassword" Type="KeyVaultReference" Value="https://ttkvault.vault.azure.net/secrets/clustercert/e225bd97e203430d809740b47736b9b8"/>
</EnvironmentVariables>
```

```C#
string eventStorePassword =  Environment.GetEnvironmentVariable("EventStorePassword");
```
## <a name="keyvault-secret-as-container-repository-password"></a>Секрет Keyvault как пароль репозитория контейнеров
KeyVaultReference является поддерживаемым типом для контейнера RepositoryCredentials, ниже приведено, как использовать ссылку keyvault в качестве пароля репозитория контейнеров.
```xml
 <Policies>
      <ContainerHostPolicies CodePackageRef="Code">
        <RepositoryCredentials AccountName="user1" Type="KeyVaultReference" Password="https://ttkvault.vault.azure.net/secrets/containerpwd/e225bd97e203430d809740b47736b9b8"/>
      </ContainerHostPolicies>
```
## <a name="faq"></a>часто задаваемые вопросы
- Для поддержки KeyVaultReference необходимо включить управляемую идентификацию, активация приложения не сработает, если KeyVaultReference будет использоваться без включения управляемой идентификации.

- Если вы используете назначенную системой идентификацию, она создается только после развертывания приложения, что создает круговую зависимость. После развертывания приложения можно предоставить системе назначенное разрешение на доступ к идентификационным данным для keyvault. Вы можете найти систему, назначенную идентификацией, по имени (кластер) /«Имя приложения»/«имя службы»

- Keyvault должен быть в той же подписке, что и кластер сервисной ткани. 

## <a name="next-steps"></a>Дальнейшие действия

* [Документация Azure KeyVault](https://docs.microsoft.com/azure/key-vault/)
