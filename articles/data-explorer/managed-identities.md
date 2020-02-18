---
title: Настройка управляемых удостоверений для кластера Azure обозреватель данных
description: Узнайте, как настроить управляемые удостоверения для кластера Azure обозреватель данных.
author: saguiitay
ms.author: itsagui
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: e76ae2e072bb780ac9788902e9157db871e4f09d
ms.sourcegitcommit: ef568f562fbb05b4bd023fe2454f9da931adf39a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/17/2020
ms.locfileid: "77373376"
---
# <a name="configure-managed-identities-for-your-azure-data-explorer-cluster"></a>Настройка управляемых удостоверений для кластера Azure обозреватель данных

[Управляемое удостоверение из Azure Active Directory](/azure/active-directory/managed-identities-azure-resources/overview) позволяет кластеру легко получать доступ к другим ресурсам, защищенным AAD, таким как Azure Key Vault. Удостоверение управляется платформой Azure и не требует предоставления или смены секретов. В этой статье показано, как создать управляемое удостоверение для кластеров Azure обозреватель данных. Конфигурация управляемого удостоверения в настоящее время поддерживается только для [включения управляемых клиентом ключей для кластера](/azure/data-explorer/security#customer-managed-keys-with-azure-key-vault).

> [!Note]
> Управляемые удостоверения для Azure обозреватель данных не будут работать должным образом, если приложение переносится между подписками или клиентами. Приложению потребуется получить новое удостоверение, которое можно сделать, отключив и повторно включив функцию с помощью [удаления удостоверения](#remove-an-identity). Кроме того, для использования нового удостоверения необходимо обновить политики доступа к нижестоящим ресурсам.

## <a name="add-a-system-assigned-identity"></a>Добавление удостоверения, назначенного системой

Кластеру может быть назначено **системное удостоверение** , привязанное к кластеру, и оно удаляется при удалении кластера. Кластер может иметь только одно назначенное системой удостоверение. Для создания кластера с удостоверением, назначенным системой, требуется задать дополнительное свойство в кластере.

### <a name="add-a-system-assigned-identity-using-c"></a>Добавление удостоверения, назначенного системой с помощьюC#

Чтобы настроить управляемое удостоверение с помощью клиента Azure C# обозреватель данных, выполните следующие действия.

* Установите [пакет NuGet для Azure обозреватель данных (Kusto)](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/).
* Установите [пакет NuGet Microsoft. IdentityModel. Clients. ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) для проверки подлинности.
* Чтобы запустить следующий пример, [Создайте приложение Azure AD](/azure/active-directory/develop/howto-create-service-principal-portal) и субъект-службу, которые могут получать доступ к ресурсам. Вы можете добавить назначение ролей в области подписки и получить необходимые `Directory (tenant) ID`, `Application ID`и `Client Secret`.

#### <a name="create-or-update-your-cluster"></a>Создание или обновление кластера

1. Создайте или обновите кластер с помощью свойства `Identity`:

    ```csharp
    var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
    var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
    var clientSecret = "xxxxxxxxxxxxxx";//Client Secret
    var subscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";
    var authenticationContext = new AuthenticationContext($"https://login.windows.net/{tenantId}");
    var credential = new ClientCredential(clientId, clientSecret);
    var result = await authenticationContext.AcquireTokenAsync(resource: "https://management.core.windows.net/", clientCredential: credential);
    
    var credentials = new TokenCredentials(result.AccessToken, result.AccessTokenType);
    
    var kustoManagementClient = new KustoManagementClient(credentials)
    {
        SubscriptionId = subscriptionId
    };
    
    var resourceGroupName = "testrg";
    var clusterName = "mykustocluster";
    var location = "Central US";
    var skuName = "Standard_D13_v2";
    var tier = "Standard";
    var capacity = 5;
    var sku = new AzureSku(skuName, tier, capacity);
    var identity = new Identity(IdentityType.SystemAssigned);
    var cluster = new Cluster(location, sku, identity: identity);
    await kustoManagementClient.Clusters.CreateOrUpdateAsync(resourceGroupName, clusterName, cluster);
    ```
    
2. Выполните следующую команду, чтобы проверить успешность создания или обновления кластера с помощью удостоверения.

    ```csharp
    kustoManagementClient.Clusters.Get(resourceGroupName, clusterName);
    ```

    Если результат содержит `ProvisioningState` со значением `Succeeded`, то кластер был создан или обновлен и должен иметь следующие свойства:
   
    ```csharp
    var principalId = cluster.Identity.PrincipalId;
    var tenantId = cluster.Identity.TenantId;
    ```

    `PrincipalId` и `TenantId` заменяются идентификаторами GUID. Свойство `TenantId` идентифицирует клиент AAD, которому принадлежит удостоверение. `PrincipalId` — это уникальный идентификатор для нового удостоверения кластера. В AAD субъект-служба имеет то же имя, которое было присвоено экземпляру Службы приложений или Функций Azure.

### <a name="add-a-system-assigned-identity-using-an-azure-resource-manager-template"></a>Добавление удостоверения, назначенного системой, с помощью шаблона Azure Resource Manager

Шаблон Azure Resource Manager можно использовать для автоматизации развертывания ресурсов Azure. Дополнительные сведения о развертывании в Azure обозреватель данных см. в статье [Создание кластера и базы данных azure обозреватель данных с помощью шаблона Azure Resource Manager](create-cluster-database-resource-manager.md).

Добавление назначенного системой типа указывает Azure создавать удостоверение кластера и управлять им. Любой ресурс типа `Microsoft.Kusto/clusters` можно создать с помощью удостоверения, добавив следующее свойство в определение ресурса: 

```json
"identity": {
    "type": "SystemAssigned"
}    
```

Пример:

```json
{
    "apiVersion": "2019-09-07",
    "type": "Microsoft.Kusto/clusters",
    "name": "[variables('clusterName')]",
    "location": "[resourceGroup().location]",
    "identity": {
        "type": "SystemAssigned"
    },
    "properties": {
        "trustedExternalTenants": [],
        "virtualNetworkConfiguration": null,
        "optimizedAutoscale": null,
        "enableDiskEncryption": false,
        "enableStreamingIngest": false,
    }
}
```

При создании кластера он имеет следующие дополнительные свойства:

```json
"identity": {
    "type": "SystemAssigned",
    "tenantId": "<TENANTID>",
    "principalId": "<PRINCIPALID>"
}
```

`<TENANTID>` и `<PRINCIPALID>` заменяются идентификаторами GUID. Свойство `TenantId` идентифицирует клиент AAD, которому принадлежит удостоверение. `PrincipalId` — это уникальный идентификатор для нового удостоверения кластера. В AAD субъект-служба имеет то же имя, которое было присвоено экземпляру Службы приложений или Функций Azure.

## <a name="remove-an-identity"></a>Удаление удостоверения

Удаление назначенного системой удостоверения также приведет к его удалению из AAD. Назначенные системой удостоверения также автоматически удаляются из AAD при удалении ресурса кластера. Чтобы удалить назначенное системой удостоверение, отключите эту функцию:

```json
"identity": {
    "type": "None"
}
```

## <a name="next-steps"></a>Дальнейшие действия

* [Защита кластеров Azure обозреватель данных в Azure](security.md)
* [Защитите свой кластер в Azure обозреватель данных — портал Azure](manage-cluster-security.md) , включив шифрование неактивных компонентов.
 * [Настройка ключей, управляемых клиентом, с помощьюC#](customer-managed-keys-csharp.md)
 * [Настройка ключей, управляемых клиентом, с помощью шаблона Azure Resource Manager](customer-managed-keys-resource-manager.md)
