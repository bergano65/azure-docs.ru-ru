---
title: Запуск службы Service Fabric Azure в учетной записи gMSA
description: Узнайте, как запустить службу как управляемую группой учетную запись службы (gMSA) в Service Fabric изолированном кластере Windows.
author: dkkapur
ms.topic: how-to
ms.date: 03/29/2018
ms.author: dekapur
ms.custom: sfrev
ms.openlocfilehash: 19343d370547cb5457f6bed70a8465187ff27102
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "76988402"
---
# <a name="run-a-service-as-a-group-managed-service-account"></a>Запуск службы в групповой управляемой учетной записи службы

В изолированном кластере Windows Server службу можно запустить как *групповую управляемую учетную запись службы* (gMSA) с помощью политики *запуска от имени* .  По умолчанию Service Fabric приложения выполняются под учетной записью, `Fabric.exe` под которой выполняется процесс. Запуск приложений в разных учетных записях позволяет изолировать друг от друга выполняемые приложения, даже если они запущены в общей среде. При использовании групповой управляемой учетной записи службы в манифесте приложения не сохраняется ни обычный, ни зашифрованный пароль.  Кроме того, можно запустить службу как [группу или пользователя Active Directory](service-fabric-run-service-as-ad-user-or-group.md).

В следующем примере показано, как создать учетную запись gMSA с именем *SVC-Test $*, как развернуть эту управляемую учетную запись службы на узлах кластера и как настроить субъект-пользователя.

> [!NOTE]
> Для использования gMSA с автономным кластером Service Fabric требуется Active Directory локально в домене (а не Azure Active Directory (Azure AD)).

Предварительные требования:

- Домену нужен корневой ключ KDS.
- В домене должен быть по крайней мере один контроллер домена Windows Server 2012 (или R2).

1. Администратор домена Active Directory создать управляемую группой учетную запись службы с помощью `New-ADServiceAccount` командлета и убедитесь, что `PrincipalsAllowedToRetrieveManagedPassword` включает все узлы Service Fabric кластера. Значения `AccountName`, `DnsHostName` и `ServicePrincipalName` должны быть уникальными.

    ```powershell
    New-ADServiceAccount -name svc-Test$ -DnsHostName svc-test.contoso.com  -ServicePrincipalNames http/svc-test.contoso.com -PrincipalsAllowedToRetrieveManagedPassword SfNode0$,SfNode1$,SfNode2$,SfNode3$,SfNode4$
    ```

2. Установите и протестируйте групповую управляемую учетную запись на каждом из узлов кластера Service Fabric (например, `SfNode0$,SfNode1$,SfNode2$,SfNode3$,SfNode4$`).
    
    ```powershell
    Add-WindowsFeature RSAT-AD-PowerShell
    Install-AdServiceAccount svc-Test$
    Test-AdServiceAccount svc-Test$
    ```

3. Настройте субъект-пользователя и настройте `RunAsPolicy` для ссылки на [пользователя](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-fabric-settings#runas).
    
    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="MyApplicationType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
        <ServiceManifestImport>
          <ServiceManifestRef ServiceManifestName="MyServiceTypePkg" ServiceManifestVersion="1.0.0" />
          <ConfigOverrides />
          <Policies>
              <RunAsPolicy CodePackageRef="Code" UserRef="DomaingMSA"/>
          </Policies>
        </ServiceManifestImport>
      <Principals>
        <Users>
          <User Name="DomaingMSA" AccountType="ManagedServiceAccount" AccountName="domain\svc-Test$"/>
        </Users>
      </Principals>
    </ApplicationManifest>
    ```

> [!NOTE]
> Если применить политику запуска от имени к службе, манифест которой объявляет ресурсы конечной точки с протоколом HTTP, необходимо указать **SecurityAccessPolicy**.  Дополнительные сведения см. в статье [Назначение политики безопасности доступа для конечных точек HTTP и HTTPS](service-fabric-assign-policy-to-endpoint.md).
>

Следующие статьи помогут вам выполнить следующие действия:

- [Сведения о модели приложения](service-fabric-application-model.md)
- [Указание ресурсов в манифесте службы](service-fabric-service-manifest-resources.md)
- [Развертывание приложения](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
