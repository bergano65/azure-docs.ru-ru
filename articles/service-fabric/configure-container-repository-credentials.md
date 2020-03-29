---
title: Azure Service Fabric - учетные данные репозитория контейнеров
description: Настройка учетных данных репозитория для загрузки изображений из реестра контейнеров
ms.topic: conceptual
ms.date: 12/09/2019
ms.custom: sfrev
ms.openlocfilehash: 9bd6e6a0a22f7568760f014897fd28ff47e9450b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76934982"
---
# <a name="configure-repository-credentials-for-your-application-to-download-container-images"></a>Настройка учетных данных репозитория для вашего приложения для загрузки изображений контейнеров

Налажить проверку `RepositoryCredentials` подлинности `ContainerHostPolicies` реестра контейнеров, добавив в раздел манифеста приложения. Добавьте учетную запись и пароль для реестра контейнеров *(myregistry.azurecr.io* в приведенном ниже примере), что позволяет службе загрузить изображение контейнера из репозитория.

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

Рекомендуется шифровать пароль репозитория с помощью сертификата шифрования, развернутого во всех узлах кластера. Когда служба Service Fabric развернет пакет службы в кластере, сертификат шифрования будет использоваться для расшифровки зашифрованного текста. Командлет Invoke-ServiceFabricEncryptText используется для создания зашифрованного текста пароля, который добавляется в файл ApplicationManifest.xml.
Подробнее о сертификатах и семантике шифрования читайте в [справке ..](service-fabric-application-secret-management.md)

## <a name="configure-cluster-wide-credentials"></a>Настройка учетных данных на уровне кластера

Service Fabric позволяет настроить учетные данные по всему кластеру, которые могут быть использованы в качестве учетных данных репозитория по умолчанию приложениями.

Эта функция может быть включена `UseDefaultRepositoryCredentials` или `ContainerHostPolicies` отключена путем добавления атрибута в ApplicationManifest.xml с `true` `false` или значением.

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

Затем Service Fabric использует учетные данные репозитория по `Hosting` умолчанию, которые могут быть указаны в ClusterManifest в разделе.  Если `UseDefaultRepositoryCredentials` — `true`, Service Fabric считывает следующие значения с ClusterManifest:

* DefaultContainerRepositoryAccountName (строка).
* DefaultContainerRepositoryPassword (строка).
* IsDefaultContainerRepositoryPasswordEncrypted (логическое значение).
* DefaultContainerRepositoryPasswordType (строка)

Вот пример того, что можно `Hosting` добавить в разделе в файле ClusterManifestTemplate.json. Раздел `Hosting` может быть добавлен при создании кластера или позже в обновлении конфигурации. Дополнительные сведения см. в статьях [Настройка параметров кластера Service Fabric](service-fabric-cluster-fabric-settings.md) и [Управление секретами в приложениях Service Fabric](service-fabric-application-secret-management.md).

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

## <a name="use-tokens-as-registry-credentials"></a>Используйте токены в качестве учетных данных реестра

Service Fabric поддерживает использование токенов в качестве учетных данных для загрузки изображений для контейнеров.  Эта функция использует *управляемую идентификацию* базовой виртуальной шкалы машин, установленную для проверки подлинности в реестре, устраняя необходимость управления учетными данными пользователей.  Для получения дополнительной информации можно получить [информацию об управляемых идентификаторах для ресурсов Azure.](../active-directory/managed-identities-azure-resources/overview.md)  Использование этой функции требует следующих шагов:

1. Убедитесь, что *система назначена управляемой идентификации* включена для VM.

    ![Портал Azure: Создание опции идентификации набора виртуальной шкалы машины](./media/configure-container-repository-credentials/configure-container-repository-credentials-acr-iam.png)

2. Разрешение на использование виртуальной шкалы машин для вытягивания/чтения изображений из реестра. Из лезвия управления доступом (IAM) в контейнерном реестре Azure на портале Azure добавьте *ролевую задачу* для виртуальной машины:

    ![Добавление доверителя VM в ACR](./media/configure-container-repository-credentials/configure-container-repository-credentials-vmss-identity.png)

3. Затем измените манифест приложения. В `ContainerHostPolicies` разделе добавьте `‘UseTokenAuthenticationCredentials=”true”`атрибут.

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
    > Флаг, `UseDefaultRepositoryCredentials` верный, `UseTokenAuthenticationCredentials` пока он истинен, вызовет ошибку во время развертывания.

## <a name="next-steps"></a>Дальнейшие действия

* Узнайте больше о [проверке подлинности реестра контейнеров](../container-registry/container-registry-authentication.md).
