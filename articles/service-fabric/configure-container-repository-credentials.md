---
title: Service Fabric Azure. Настройка учетных данных репозитория контейнеров
description: Настройка учетных данных репозитория для загрузки образов из реестра контейнеров
ms.topic: conceptual
ms.date: 12/09/2019
ms.custom: sfrev
ms.openlocfilehash: 9bd6e6a0a22f7568760f014897fd28ff47e9450b
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/01/2020
ms.locfileid: "76934982"
---
# <a name="configure-repository-credentials-for-your-application-to-download-container-images"></a>Настройка учетных данных репозитория для приложения для скачивания образов контейнеров

Настройте проверку подлинности реестра контейнеров, добавив `RepositoryCredentials` в раздел `ContainerHostPolicies` манифеста приложения. Добавьте учетную запись и пароль для реестра контейнеров (*myregistry.azurecr.IO* в примере ниже), что позволяет службе загрузить образ контейнера из репозитория.

```xml
<ServiceManifestImport>
    ...
    <Policies>
        <ContainerHostPolicies CodePackageRef="Code">
            <RepositoryCredentials AccountName="myregistry" Password="=P==/==/=8=/=+u4lyOB=+=nWzEeRfF=" PasswordEncrypted="false"/>
            <PortBinding ContainerPort="80" EndpointRef="Guest1TypeEndpoint"/>
        </ContainerHostPolicies>
    </Policies>
    ...
</ServiceManifestImport>
```

Рекомендуется зашифровать пароль репозитория с помощью сертификата шифрования, который развертывается на всех узлах кластера. Когда служба Service Fabric развернет пакет службы в кластере, сертификат шифрования будет использоваться для расшифровки зашифрованного текста. Командлет Invoke-ServiceFabricEncryptText используется для создания зашифрованного текста пароля, который добавляется в файл ApplicationManifest.xml.
Дополнительные сведения о сертификатах и семантике шифрования см. в разделе [Управление секретами](service-fabric-application-secret-management.md) .

## <a name="configure-cluster-wide-credentials"></a>Настройка учетных данных на уровне кластера

Service Fabric позволяет настроить учетные данные для всего кластера, которые могут использоваться приложениями как учетные данные репозитория по умолчанию.

Эту функцию можно включить или отключить, добавив атрибут `UseDefaultRepositoryCredentials` в `ContainerHostPolicies` в ApplicationManifest. XML со значением `true` или `false`.

```xml
<ServiceManifestImport>
    ...
    <Policies>
        <ContainerHostPolicies CodePackageRef="Code" UseDefaultRepositoryCredentials="true">
            <PortBinding ContainerPort="80" EndpointRef="Guest1TypeEndpoint"/>
        </ContainerHostPolicies>
    </Policies>
    ...
</ServiceManifestImport>
```

Затем Service Fabric использует учетные данные репозитория по умолчанию, которые можно указать в ClusterManifest в разделе `Hosting`.  Если `UseDefaultRepositoryCredentials` — `true`, Service Fabric считывает следующие значения с ClusterManifest:

* DefaultContainerRepositoryAccountName (строка).
* DefaultContainerRepositoryPassword (строка).
* IsDefaultContainerRepositoryPasswordEncrypted (логическое значение).
* Дефаултконтаинеррепоситорипассвордтипе (строка)

Ниже приведен пример того, что можно добавить в раздел `Hosting` в файле Клустерманифесттемплате. JSON. Раздел `Hosting` можно добавить при создании кластера или позднее при обновлении конфигурации. Дополнительные сведения см. в статьях [Настройка параметров кластера Service Fabric](service-fabric-cluster-fabric-settings.md) и [Управление секретами в приложениях Service Fabric](service-fabric-application-secret-management.md).

```json
"fabricSettings": [
    ...,
    {
        "name": "Hosting",
        "parameters": [
          {
            "name": "EndpointProviderEnabled",
            "value": "true"
          },
          {
            "name": "DefaultContainerRepositoryAccountName",
            "value": "someusername"
          },
          {
            "name": "DefaultContainerRepositoryPassword",
            "value": "somepassword"
          },
          {
            "name": "IsDefaultContainerRepositoryPasswordEncrypted",
            "value": "false"
          },
          {
            "name": "DefaultContainerRepositoryPasswordType",
            "value": "PlainText"
          }
        ]
      },
]
```

## <a name="use-tokens-as-registry-credentials"></a>Использование маркеров в качестве учетных данных реестра

Service Fabric поддерживает использование маркеров в качестве учетных данных для скачивания изображений для контейнеров.  Эта функция использует *управляемое удостоверение* базового масштабируемого набора виртуальных машин для проверки подлинности в реестре, устраняя необходимость в управлении учетными данными пользователя.  Дополнительные сведения см. в статье [управляемые удостоверения для ресурсов Azure](../active-directory/managed-identities-azure-resources/overview.md) .  Для использования этой функции необходимо выполнить следующие действия.

1. Убедитесь, что для виртуальной машины включено *управляемое удостоверение, назначенное системой* .

    ![Портал Azure: параметр "создать идентификатор масштабируемого набора виртуальных машин"](./media/configure-container-repository-credentials/configure-container-repository-credentials-acr-iam.png)

2. Предоставьте разрешения на доступ к масштабируемому набору виртуальных машин, чтобы извлечь или прочитать образы из реестра. В колонке управление доступом (IAM) реестра контейнеров Azure в портал Azure добавьте *назначение роли* для виртуальной машины:

    ![Добавление субъекта виртуальной машины в запись контроля доступа](./media/configure-container-repository-credentials/configure-container-repository-credentials-vmss-identity.png)

3. Затем измените манифест приложения. В разделе `ContainerHostPolicies` добавьте атрибут `‘UseTokenAuthenticationCredentials=”true”`.

    ```xml
      <ServiceManifestImport>
          <ServiceManifestRef ServiceManifestName="NodeServicePackage" ServiceManifestVersion="1.0"/>
      <Policies>
        <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="process" UseTokenAuthenticationCredentials="true">
          <PortBinding ContainerPort="8905" EndpointRef="Endpoint1"/>
        </ContainerHostPolicies>
        <ResourceGovernancePolicy CodePackageRef="NodeService.Code" MemoryInMB="256"/>
      </Policies>
      </ServiceManifestImport>
    ```

    > [!NOTE]
    > Флаг `UseDefaultRepositoryCredentials` установлен равным true, тогда как `UseTokenAuthenticationCredentials` имеет значение true, вызовет ошибку во время развертывания.

## <a name="next-steps"></a>Дальнейшие действия

* См. Дополнительные сведения о [проверке подлинности реестра контейнеров](../container-registry/container-registry-authentication.md).
