---
title: Развертывание приложения с назначенным пользователем управляемым удостоверением
description: В этой статье показано, как развертывать приложение Service Fabric с помощью управляемого удостоверения, назначенного пользователем
ms.topic: article
ms.date: 12/09/2019
ms.openlocfilehash: 9aef81db7a455b72c83cf96898a0c228f1c382fd
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81415627"
---
# <a name="deploy-service-fabric-application-with-a-user-assigned-managed-identity"></a>Развертывание приложения Service Fabric с управляемой итогоми, назначенной пользователем

Для развертывания приложения Service Fabric с управляемым имитатором необходимо развертывание через azure Resource Manager, обычно с шаблоном управления ресурсами Azure. Для получения дополнительной информации о том, как развертывать приложение Service Fabric через менеджер ресурсов Azure, смотрите в приложениях [и службах Управления как ресурсы менеджера ресурсов Azure.](service-fabric-application-arm-resource.md)

> [!NOTE] 
> 
> Приложения, не развернутые в качестве ресурса Azure, **не могут** иметь управляемые идентификаторы. 
>
> Развертывание приложения Service Fabric с управляемой `"2019-06-01-preview"`идентификацией поддерживается версией API. Вы также можете использовать ту же версию API для типа приложения, версии типа приложения и ресурсов обслуживания.
>

## <a name="user-assigned-identity"></a>Идентификационная личность пользователя

Чтобы включить приложение с идентификатором, назначенным пользователем, сначала добавьте свойство **идентификации** в ресурс приложения с **помощью type userAssigned** и ссылки на имена, назначенные пользователем. Затем добавьте раздел **управляемых идентификаторов** в разделе **свойств** для ресурса **приложения,** который содержит список дружественных имен для отображения principalId для каждого из назначенных пользователем идентификационных данных. Для получения дополнительной информации о назначенных пользователях идентификаторах [см. Создать, перечислить или удалить управляемый идентификатор](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell)пользователя.

### <a name="application-template"></a>Шаблон приложения

Для включения приложения с идентификацией пользователя сначала добавьте свойство **идентификации** в ресурс приложения с помощью type **userAssigned** и ссылки на пользователя, назначенные идентификаторы, а затем добавьте объект **управляемых идентификаторов** в разделе **свойств,** содержащий список дружественных имен для отображения principalId для каждого из назначенных пользователей идентификационных данных.

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

В приведенном выше названии ресурса имя назначенного пользователя удостоверяется в использовании в качестве дружественного имени управляемого удостоверения для приложения. Следующие примеры предполагают, что настоящее дружественное название "AdminUser".

### <a name="application-package"></a>Пакет приложения

1. Для каждого удостоверения, определенного `managedIdentities` в разделе в `<ManagedIdentity>` шаблоне менеджера ресурсов Azure, добавьте тег в манифесте приложения в разделе **Принципы.** Атрибут `Name` должен соответствовать `name` свойству, `managedIdentities` определенному в разделе.

    **ApplicationManifest.xml**

    ```xml
      <Principals>
        <ManagedIdentities>
          <ManagedIdentity Name="AdminUser" />
        </ManagedIdentities>
      </Principals>
    ```

2. В разделе **ServiceManifestImport** добавьте **IdentityBindingPolicy** для службы, используюющей управляемую идентификацию. Эта политика `AdminUser` отображает идентификацию к имени, конкретному службе, которое должно быть добавлено в манифест службы позже.

    **ApplicationManifest.xml**

    ```xml
      <ServiceManifestImport>
        <Policies>
          <IdentityBindingPolicy ServiceIdentityRef="WebAdmin" ApplicationIdentityRef="AdminUser" />
        </Policies>
      </ServiceManifestImport>
    ```

3. Обновление манифеста службы для добавления **Управляемой** идентичности `IdentityBindingPolicy` внутри раздела **Ресурсов** с именем, соответствующим `ServiceIdentityRef` в манифесте приложения:

    **ServiceManifest.xml**

    ```xml
      <Resources>
        ...
        <ManagedIdentities DefaultIdentity="WebAdmin">
          <ManagedIdentity Name="WebAdmin" />
        </ManagedIdentities>
      </Resources>
    ```

## <a name="next-steps"></a>Дальнейшие действия

* [Как использовать управляемый идентификационный код в коде приложения Service Fabric](how-to-managed-identity-service-fabric-app-code.md)
* [Как предоставить доступ к приложениям Service Fabric к другим ресурсам Azure](how-to-grant-access-other-resources.md)
