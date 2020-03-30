---
title: Запустите службу Azure Fabric под учетной записью gMSA
description: Узнайте, как запустить службу в виде группового сервисного счета (gMSA) в отдельном кластере Service Fabric Windows.
author: dkkapur
ms.topic: how-to
ms.date: 03/29/2018
ms.author: dekapur
ms.custom: sfrev
ms.openlocfilehash: 19343d370547cb5457f6bed70a8465187ff27102
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76988402"
---
# <a name="run-a-service-as-a-group-managed-service-account"></a>Запуск службы в групповой управляемой учетной записи службы

В отдельном кластере Windows Server можно запустить службу в качестве *учетной записи службы группы* (gMSA) с помощью политики *RunAs.*  По умолчанию приложения Service Fabric работают под учетной записью, под которым `Fabric.exe` выполняется процесс. Запуск приложений в разных учетных записях позволяет изолировать друг от друга выполняемые приложения, даже если они запущены в общей среде. При использовании групповой управляемой учетной записи службы в манифесте приложения не сохраняется ни обычный, ни зашифрованный пароль.  Кроме того, можно запустить службу как [группу или пользователя Active Directory](service-fabric-run-service-as-ad-user-or-group.md).

В следующем примере показано, как создать учетную запись gMSA под названием *svc-Test$,* как развернуть учетную запись управляемой службы в кластерные узлы и как настроить основной учет пользователя.

> [!NOTE]
> Использование gMSA с автономным кластером Service Fabric требует active Directory на месте в вашем домене (а не Active Directory Azure (Azure AD)).

Предварительные требования:

- Домену нужен корневой ключ KDS.
- В домене должен быть по крайней мере один DC Windows Server 2012 (или R2).

1. Поиметь администратора домена Active Directory создать `New-ADServiceAccount` управляемую группой `PrincipalsAllowedToRetrieveManagedPassword` учетную запись службы с помощью cmdlet и убедиться, что включает в себя все кластерные узлы Service Fabric. Значения `AccountName`, `DnsHostName` и `ServicePrincipalName` должны быть уникальными.

    ```powershell
    New-ADServiceAccount -name svc-Test$ -DnsHostName svc-test.contoso.com  -ServicePrincipalNames http/svc-test.contoso.com -PrincipalsAllowedToRetrieveManagedPassword SfNode0$,SfNode1$,SfNode2$,SfNode3$,SfNode4$
    ```

2. Установите и протестируйте групповую управляемую учетную запись на каждом из узлов кластера Service Fabric (например, `SfNode0$,SfNode1$,SfNode2$,SfNode3$,SfNode4$`).
    
    ```powershell
    Add-WindowsFeature RSAT-AD-PowerShell
    Install-AdServiceAccount svc-Test$
    Test-AdServiceAccount svc-Test$
    ```

3. Наверстойминить основной `RunAsPolicy` пользователь и настроить ссылку [на пользователя.](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-fabric-settings#runas)
    
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

Следующие статьи помогут вам провести следующие шаги:

- [Сведения о модели приложения](service-fabric-application-model.md)
- [Указание ресурсов в манифесте службы](service-fabric-service-manifest-resources.md)
- [Развертывание приложения](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
