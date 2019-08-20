---
title: Service Fabric Azure. Развертывание приложения с управляемым пользователем удостоверением | Документация Майкрософт
description: В этой статье показано, как развернуть приложение Service Fabric с управляемым удостоверением, назначенным пользователем.
services: service-fabric
author: athinanthny
ms.service: service-fabric
ms.topic: article
ms.date: 08/09/2019
ms.author: atsenthi
ms.openlocfilehash: f8dfaa39f02aefbdda1f34afa5011ce5fadbae49
ms.sourcegitcommit: 55e0c33b84f2579b7aad48a420a21141854bc9e3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/19/2019
ms.locfileid: "69624917"
---
# <a name="deploy-service-fabric-application-with-a-user-assigned-managed-identity-preview"></a>Развертывание Service Fabric приложения с помощью управляемого удостоверения, назначенного пользователем (Предварительная версия)

Чтобы развернуть приложение Service Fabric с управляемым удостоверением, приложение должно быть развернуто с помощью Azure Resource Manager, обычно с шаблоном Azure Resource Manager. Дополнительные сведения о развертывании приложения Service Fabric с помощью Azure Resource Manager см. в статье [Управление приложениями и службами в качестве Azure Resource Managerных ресурсов](service-fabric-application-arm-resource.md).

> [!NOTE] 
> 
> Приложения, которые не развертываются как ресурсы Azure, **не могут** иметь управляемые удостоверения. 
>
> Service Fabric развертывание приложения с управляемым удостоверением поддерживается в версии `"2019-06-01-preview"`API. Можно также использовать ту же версию API для типа приложения, версию типа приложения и ресурсы службы.
>

## <a name="user-assigned-identity"></a>Удостоверение, назначенное пользователем

Чтобы включить приложение с удостоверением, назначенным пользователем, сначала добавьте свойство **Identity** в ресурс приложения с типом **усерассигнед** и назначенными пользователем удостоверениями. Затем добавьте раздел **манажедидентитиес** в раздел **Properties** для ресурса **приложения** , который содержит список понятных имен для сопоставления principalId для каждого назначенного пользователем удостоверения.

### <a name="application-template"></a>Шаблон приложения

Чтобы включить приложение с назначенным пользователем удостоверением, сначала добавьте свойство **Identity** в ресурс приложения с типом **усерассигнед** и назначенными пользователем удостоверениями, а затем добавьте объект **манажедидентитиес** в  **раздел Properties** , содержащий список понятных имен для сопоставления principalId для каждого назначенного пользователем удостоверения.

    {
      "apiVersion": "2019-06-01-preview",
      "type": "Microsoft.ServiceFabric/clusters/applications",
      "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[concat('Microsoft.ServiceFabric/clusters/', parameters('clusterName'), '/applicationTypes/', parameters('applicationTypeName'), '/versions/', parameters('applicationTypeVersion'))]",
        "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities/', parameters('userAssignedIdentityName'))]"
      ],
      "identity": {
        "type" : "userAssigned",
        "userAssignedIdentities": {
            "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities/', parameters('userAssignedIdentityName'))]": {}
        }
      },
      "properties": {
        "typeName": "[parameters('applicationTypeName')]",
        "typeVersion": "[parameters('applicationTypeVersion')]",
        "parameters": {
        },
        "managedIdentities": [
          {
            "name" : "[parameters('userAssignedIdentityName')]",
            "principalId" : "[reference(resourceId('Microsoft.ManagedIdentity/userAssignedIdentities/', parameters('userAssignedIdentityName')), '2018-11-30').principalId]"
          }
        ]
      }
    }

В приведенном выше примере имя ресурса назначенного пользователя используется в качестве понятного имени управляемого удостоверения для приложения. В следующих примерах предполагается, что фактически понятное имя — «AdminUser».

### <a name="application-package"></a>Пакет приложения

1. Для каждого удостоверения, определенного в `managedIdentities` разделе шаблона Azure Resource Manager, `<ManagedIdentity>` добавьте тег в манифест приложения в разделе **участников** . Атрибут должен соответствовать свойству, определенному в `managedIdentities` разделе. `name` `Name`

    **ApplicationManifest. XML**

    ```xml
      <Principals>
        <ManagedIdentities>
          <ManagedIdentity Name="AdminUser" />
        </ManagedIdentities>
      </Principals>
    ```

2. В разделе **ServiceManifestImport** добавьте **идентитибиндингполици** для службы, которая использует управляемое удостоверение. Эта политика сопоставляет `AdminUser` удостоверение с именем удостоверения, зависящего от службы, которое необходимо добавить в манифест службы позже.

    **ApplicationManifest. XML**

    ```xml
      <ServiceManifestImport>
        <Policies>
          <IdentityBindingPolicy ServiceIdentityRef="WebAdmin" ApplicationIdentityRef="AdminUser" />
        </Policies>
      </ServiceManifestImport>
    ```

3. Обновите манифест службы, чтобы добавить **ManagedIdentity** в раздел Resources с `ServiceIdentityRef` именем, соответствующим в `IdentityBindingPolicy` манифесте приложения:

    **ServiceManifest. XML**

    ```xml
      <Resources>
        ...
        <ManagedIdentities DefaultIdentity="WebAdmin">
          <ManagedIdentity Name="WebAdmin" />
        </ManagedIdentities>
      </Resources>
    ```

## <a name="next-steps"></a>Следующие шаги

* [Использование управляемого удостоверения в коде приложения Service Fabric](how-to-managed-identity-service-fabric-app-code.md)
* [Как предоставить Service Fabric доступ к другим ресурсам Azure для приложений](how-to-grant-access-other-resources.md)