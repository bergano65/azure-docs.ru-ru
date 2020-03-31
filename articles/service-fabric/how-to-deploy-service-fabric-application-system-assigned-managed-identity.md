---
title: Развертывание приложения Service Fabric с системным назначенным MI
description: В этой статье показано, как присвоить управляемое удостоверение, назначенное системой, приложению Azure Service Fabric
ms.topic: article
ms.date: 07/25/2019
ms.openlocfilehash: d5a14722363d642957904f9c7c699d3cf1d66c0f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75614831"
---
# <a name="deploy-service-fabric-application-with-system-assigned-managed-identity-preview"></a>Развертывание приложения Service Fabric с системой назначенных управляемых итог (предварительный просмотр)

Для доступа к функции управляемой идентификации для приложений Azure Service Fabric необходимо сначала включить службу управляемых токенов в кластере. Эта служба отвечает за аутентификацию приложений Service Fabric с использованием управляемых идентификаторов, а также за получение токенов доступа от их имени. После включения службы вы можете увидеть ее в Service Fabric Explorer под разделом **System** в левом стеле, работая под **названием ткани:/System/ManagedIdentityTokenService** рядом с другими системными службами.

> [!NOTE] 
> Развертывание приложений Service Fabric с управляемыми идентификаторами поддерживается начиная с версии `"2019-06-01-preview"`API. Вы также можете использовать ту же версию API для типа приложения, версии типа приложения и ресурсов обслуживания. Минимальное время выполнения службы Fabric составляет 6,5 CU2. В additoin, сборка / пакет среды также должны иметь SF .Net SDK на CU2 или выше

## <a name="system-assigned-managed-identity"></a>Управляемое удостоверение, назначаемое системой

### <a name="application-template"></a>Шаблон приложения

Для включения приложения с системой, назначенной управляемой имитаю, добавьте свойство **идентификации** в ресурс приложения, с **системой типаНазначенный,** как показано в примере ниже:

```json
    {
      "apiVersion": "2019-06-01-preview",
      "type": "Microsoft.ServiceFabric/clusters/applications",
      "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[concat('Microsoft.ServiceFabric/clusters/', parameters('clusterName'), '/applicationTypes/', parameters('applicationTypeName'), '/versions/', parameters('applicationTypeVersion'))]"
      ],
      "identity": {
        "type" : "systemAssigned"
      },
      "properties": {
        "typeName": "[parameters('applicationTypeName')]",
        "typeVersion": "[parameters('applicationTypeVersion')]",
        "parameters": {
        }
      }
    }
```
Это свойство декларирует (менеджеру ресурсов Azure и поставщикам ресурсов управляемых identity и сервисных`system assigned`ресурсов, соответственно, что этот ресурс должен иметь неявное () управляемое удостоверение.

### <a name="application-and-service-package"></a>Пакет приложений и услуг

1. Обновление манифеста приложения для добавления элемента **ManagedIdentity** в разделе **Принципы,** содержащего одну запись, как показано ниже:

    **ApplicationManifest.xml**

    ```xml
    <Principals>
      <ManagedIdentities>
        <ManagedIdentity Name="SystemAssigned" />
      </ManagedIdentities>
    </Principals>
    ```
    Это отображает идентификацию, присвоенную приложению в качестве ресурса к дружественному имени, для дальнейшего назначения в службы, включающие приложение. 

2. В разделе **ServiceManifestImport,** соответствующем службе, которой присваивается управляемое удостоверение, добавьте элемент **IdentityBindingPolicy,** указанный ниже:

    **ApplicationManifest.xml**

      ```xml
        <ServiceManifestImport>
          <Policies>
            <IdentityBindingPolicy ServiceIdentityRef="WebAdmin" ApplicationIdentityRef="SystemAssigned" />
          </Policies>
        </ServiceManifestImport>
      ```

    Этот элемент присваивает службе удостоверение приложения; без этого назначения служба не сможет получить доступ к удостоверению приложения. В фрагменте выше, `SystemAssigned` личность (которая является зарезервированным ключевым словом) отображается к определению `WebAdmin`службы под дружественным названием.

3. Обновление манифеста службы для добавления элемента **ManagedIdentity** в `ServiceIdentityRef` разделе **Ресурсов** с именем, соответствующим значению параметра из `IdentityBindingPolicy` определения в манифесте приложения:

    **ServiceManifest.xml**

    ```xml
      <Resources>
        ...
        <ManagedIdentities DefaultIdentity="WebAdmin">
          <ManagedIdentity Name="WebAdmin" />
        </ManagedIdentities>
      </Resources>
    ```
    Это эквивалентное отображение идентификации службе, описанной выше, но с точки зрения определения службы. Идентификация упоминается здесь своим дружественным названием (),`WebAdmin`как удекларировано в манифесте приложения.

## <a name="next-steps"></a>Next Steps
* Обзор [управляемой поддержки идентификации](./concepts-managed-identity.md) в Azure Service Fabric
* [Развертывание нового](./configure-new-azure-service-fabric-enable-managed-identity.md) Кластер Azure Service Fabric с управляемой идентификационной поддержкой 
* [Включить управляемое удостоверение](./configure-existing-cluster-enable-managed-identity-token-service.md) в существующем кластере Azure Service Fabric
* Использование [управляемого удостоверения](./how-to-managed-identity-service-fabric-app-code.md) приложения Service Fabric из исходного кода
* [Развертывание приложения Azure Service Fabric с помощью управляемого удостоверения пользователя](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
* [Предоставить доступ к другим ресурсам Azure](./how-to-grant-access-other-resources.md)
