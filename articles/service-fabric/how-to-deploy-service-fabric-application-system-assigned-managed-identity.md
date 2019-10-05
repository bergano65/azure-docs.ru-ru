---
title: Service Fabric Azure. Развертывание приложения Service Fabric Azure с управляемым удостоверением, назначенным системой | Документация Майкрософт
description: В этой статье показано, как назначить управляемое системой удостоверение для приложения Service Fabric Azure.
services: service-fabric
author: athinanthny
ms.service: service-fabric
ms.topic: article
ms.date: 07/25/2019
ms.author: atsenthi
ms.openlocfilehash: cf971d71c2566d91bc5a2490d47521725c62b17d
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/05/2019
ms.locfileid: "71973426"
---
# <a name="deploy-service-fabric-application-with-system-assigned-managed-identity-preview"></a>Развертывание Service Fabric приложения с управляемым удостоверением, назначенным системой (Предварительная версия)

Чтобы получить доступ к функции управляемого удостоверения для приложений Azure Service Fabric, необходимо сначала включить службу токенов управляемого удостоверения в кластере. Эта служба отвечает за проверку подлинности Service Fabric приложений с помощью их управляемых удостоверений и получение маркеров доступа от их имени. Когда служба будет включена, ее можно увидеть в Service Fabric Explorer в разделе " **система** " в левой области, работающей под именем **Fabric:/System/манажедидентититокенсервице** рядом с другими системными службами.

> [!NOTE] 
> Развертывание Service Fabric приложений с управляемыми удостоверениями поддерживается начиная с версии API `"2019-06-01-preview"`. Можно также использовать ту же версию API для типа приложения, версию типа приложения и ресурсы службы. Минимальная поддерживаемая среда выполнения Service Fabric — 6,5 CU2. В аддитоин среда сборки или пакета также должна иметь пакет SDK для .NET «SF» по адресу CU2 или выше.

## <a name="system-assigned-managed-identity"></a>Управляемое удостоверение, назначаемое системой

### <a name="application-template"></a>Шаблон приложения

Чтобы включить приложение с управляемым удостоверением, связанным с системой, добавьте свойство **Identity** в ресурс приложения с типом **systemAssigned** , как показано в следующем примере:

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
Это свойство объявляет (для Azure Resource Manager и управляемые поставщики удостоверений и Service Fabric ресурсов соответственно, что этот ресурс должен иметь неявное управляемое удостоверение (`system assigned`).

### <a name="application-and-service-package"></a>Пакет приложения и службы

1. Обновите манифест приложения, чтобы добавить элемент **ManagedIdentity** в раздел **участников** , содержащий одну запись, как показано ниже:

    **ApplicationManifest. XML**

    ```xml
    <Principals>
      <ManagedIdentities>
        <ManagedIdentity Name="SystemAssigned" />
      </ManagedIdentities>
    </Principals>
    ```
    Это позволяет сопоставить удостоверение, назначенное приложению, с понятным именем в качестве ресурса для дальнейшего назначения службам, составляющим приложение. 

2. В разделе **ServiceManifestImport** , соответствующем службе, которой назначается управляемое удостоверение, добавьте элемент **идентитибиндингполици** , как показано ниже:

    **ApplicationManifest. XML**

      ```xml
        <ServiceManifestImport>
          <Policies>
            <IdentityBindingPolicy ServiceIdentityRef="WebAdmin" ApplicationIdentityRef="SystemAssigned" />
          </Policies>
        </ServiceManifestImport>
      ```

    Этот элемент назначает службе удостоверение приложения. без этого назначения Служба не сможет получить доступ к удостоверениям приложения. В приведенном выше фрагменте удостоверение `SystemAssigned` (которое является зарезервированным ключевым словом) сопоставляется с определением службы под понятным именем `WebAdmin`.

3. Обновите манифест службы, чтобы добавить элемент **ManagedIdentity** в раздел **Resources** с именем, соответствующим значению параметра `ServiceIdentityRef` из определения `IdentityBindingPolicy` в манифесте приложения:

    **ServiceManifest. XML**

    ```xml
      <Resources>
        ...
        <ManagedIdentities DefaultIdentity="WebAdmin">
          <ManagedIdentity Name="WebAdmin" />
        </ManagedIdentities>
      </Resources>
    ```
    Это эквивалентное сопоставление удостоверения со службой, как описано выше, но с точки зрения определения службы. Ссылка на удостоверение указывается по его понятному имени (`WebAdmin`), как объявлено в манифесте приложения.

## <a name="next-steps"></a>Следующие шаги
* Проверка [поддержки управляемого удостоверения](./concepts-managed-identity.md) в Azure Service Fabric
* [Развертывание нового](./configure-new-azure-service-fabric-enable-managed-identity.md) Кластер Azure Service Fabric с поддержкой управляемых удостоверений 
* [Включение управляемого удостоверения](./configure-existing-cluster-enable-managed-identity-token-service.md) в существующем кластере Azure Service Fabric
* Использование [управляемого удостоверения приложения Service Fabric из исходного кода](./how-to-managed-identity-service-fabric-app-code.md)
* [Развертывание приложения Service Fabric Azure с помощью управляемого удостоверения, назначенного пользователем](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
* [Предоставление приложению Azure Service Fabric доступа к другим ресурсам Azure](./how-to-grant-access-other-resources.md)
