---
title: Service Fabric Azure. Настройка учетных данных для репозитория контейнеров | Документация Майкрософт
description: Настройка учетных данных репозитория для загрузки образов из реестра контейнеров
services: service-fabric
documentationcenter: .net
author: arya
manager: gkhanna
ms.assetid: b93d31e5-9e4c-4405-b266-c0efa4643d97
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 8/1/2019
ms.author: arya
ms.openlocfilehash: cfe212a150da0e5828f48de3bf2692ab2a44c672
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/20/2019
ms.locfileid: "69657168"
---
# <a name="configure-repository-credentials-for-your-application-to-download-container-images"></a>Настройка учетных данных репозитория для приложения для скачивания образов контейнеров

Чтобы настроить проверку подлинности для реестра контейнеров, в файле ApplicationManifest.xml добавьте `RepositoryCredentials` в элемент `ContainerHostPolicies`. Добавьте учетную запись и пароль для реестра контейнеров myregistry.azurecr.io. Это позволит службе скачать образ контейнера из репозитория.

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

Эту функцию можно включить или отключить, добавив `UseDefaultRepositoryCredentials` `ContainerHostPolicies` атрибут в в `true` ApplicationManifest. XML со значением или `false` .

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

Затем Service Fabric использует учетные данные репозитория по умолчанию, которые можно указать в ClusterManifest `Hosting` в разделе.  Если `UseDefaultRepositoryCredentials` — `true`, Service Fabric считывает следующие значения с ClusterManifest:

* DefaultContainerRepositoryAccountName (строка).
* DefaultContainerRepositoryPassword (строка).
* IsDefaultContainerRepositoryPasswordEncrypted (логическое значение).
* DefaultContainerRepositoryPasswordType (строка) — поддерживается, начиная со среды выполнения 6.4.

Ниже приведен пример того, что можно добавить `Hosting` в раздел файла клустерманифесттемплате. JSON. Этот `Hosting` раздел можно добавить при создании кластера или позднее при обновлении конфигурации. Дополнительные сведения см. в статьях [Настройка параметров кластера Service Fabric](service-fabric-cluster-fabric-settings.md) и [Управление секретами в приложениях Service Fabric](service-fabric-application-secret-management.md).

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

## <a name="leveraging-the-managed-identity-of-the-virtual-machine-scale-set-by-using-managed-identity-service-msi"></a>Использование управляемого удостоверения масштабируемого набора виртуальных машин с помощью управляемой службы удостоверений (MSI)

Service Fabric поддерживает использование маркеров в качестве учетных данных для скачивания изображений для контейнеров.  Эта функция использует управляемое удостоверение базового масштабируемого набора виртуальных машин для проверки подлинности в реестре, устраняя необходимость в управлении учетными данными пользователя.  Дополнительные сведения о MSI см. в разделе [управляемое удостоверение службы](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) .  Для использования этой функции необходимо выполнить следующие действия.

1.  Убедитесь, что управляемое удостоверение, назначенное системой, включено для виртуальной машины (см. снимок экрана ниже).

    ![Создание удостоверения масштабируемого набора виртуальных машин](./media/configure-container-repository-credentials/configure-container-repository-credentials-acr-iam.png)

2.  После этого предоставьте разрешения на виртуальную машину (SS) для извлечения и чтения образов из реестра.  Перейдите в раздел Управление доступом (IAM) вашей записи контроля доступа через колонку Azure и предоставьте виртуальной машине (SS) правильные разрешения, как показано ниже:

    ![Добавление субъекта виртуальной машины в запись контроля доступа](./media/configure-container-repository-credentials/configure-container-repository-credentials-vmss-identity.png)

3.  После выполнения описанных выше действий измените файл applicationmanifest. XML.  Найдите тег с меткой "ContainerHostPolicies" и добавьте атрибут `‘UseTokenAuthenticationCredentials=”true”`.

    ```json
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
    > Флаг `UseDefaultRepositoryCredentials` , установленный в true `UseTokenAuthenticationCredentials` , в то время как имеет значение true, вызовет ошибку во время развертывания.

## <a name="next-steps"></a>Следующие шаги

* См. Дополнительные сведения о [проверке подлинности реестра контейнеров](/azure/container-registry/container-registry-authentication).
