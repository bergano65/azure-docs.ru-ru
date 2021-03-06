---
title: Подключение клиента к системе делегированного управления ресурсами Azure
description: Узнайте, как подключить клиента к системе делегированного управления ресурсами Azure, предоставив доступ к ресурсам и возможность управления ими через собственный клиент.
ms.date: 11/7/2019
ms.topic: conceptual
ms.openlocfilehash: 88e75b92ca09f3c6671fe4399c4672240f863f66
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/02/2019
ms.locfileid: "74689260"
---
# <a name="onboard-a-customer-to-azure-delegated-resource-management"></a>Подключение клиента к системе делегированного управления ресурсами Azure

В этой статье объясняется, как вы в роли поставщика услуг можете подключить клиента к системе делегированного управления ресурсами Azure, позволяя получать доступ к своим делегированным ресурсам (подпискам и (или) группам ресурсов) и управлять ими через собственный клиент Azure Active Directory (Azure AD). Хотя здесь будет идти речь о поставщиках услуг и клиентах, предприятия, управляющие несколькими арендаторами, могут использовать один и тот же процесс для консолидации своих возможностей управления.

Вы можете повторить этот процесс, если управляете ресурсами для нескольких клиентов. Затем, когда полномочный пользователь входит в клиент, он может быть авторизован во всех областях аренды клиента для выполнения операций управления без необходимости входа в каждый отдельный клиент.

Вы можете связать свой идентификатор Microsoft Partner Network (MPN) со встроенными подписками, чтобы отслеживать свое влияние на задействование клиентов и получать соответствующие свидетельства. Дополнительные сведения см. в статье [Link a partner ID to your Azure accounts](https://docs.microsoft.com/azure/billing/billing-partner-admin-link-started) (Привязка идентификатора партнера к учетной записи Azure). Обратите внимание, что эту привязку необходимо выполнять в клиенте поставщика службы.

> [!NOTE]
> Клиенты могут также быть подключены при покупке предложения управляемых служб (общедоступных или частных), которое вы опубликовали в Azure Marketplace. Дополнительные сведения см. в статье [Publish a managed services offer to Azure Marketplace](publish-managed-services-offers.md) (Публикация предложения управляемых служб в Azure Marketplace). Описанный здесь процесс подключения также можно использовать с предложением, опубликованным в Azure Marketplace.

Для процесса подключения требуется, чтобы действия выполнялись в клиенте поставщика услуг и клиенте пользователя. Все эти шаги описаны в данной статье.

> [!IMPORTANT]
> В настоящее время вы не можете подключить подписку (или группу ресурсов в подписке) для системы делегированного управления ресурсами Azure, если в подписке используется Azure Databricks. Аналогичным образом, если подписка зарегистрирована для подключения к поставщику ресурсов **Microsoft.ManagedServices**, вы не сможете на данный момент создать рабочее пространство Databricks для этой подписки.

## <a name="gather-tenant-and-subscription-details"></a>Сбор сведений о клиентах и подписках

Чтобы подключить клиент пользователя, у него должна быть активная подписка Azure. Для этого потребуется следующее:

- идентификатор клиента поставщика услуг (где вы будете управлять ресурсами клиента);
- идентификатор пользовательского клиента (в котором будут ресурсы, управляемые поставщиком услуг);
- идентификаторы подписки для каждой конкретной подписки в клиенте пользователя, которой будет управлять поставщик услуг (или которая содержит группы ресурсов, управляемые поставщиком услуг).

Если у вас еще нет этих данных, вы можете получить их с помощью одного из следующих способов.

### <a name="azure-portal"></a>портала Azure

Идентификатор клиента можно увидеть, наведя указатель мыши на имя учетной записи в правом верхнем углу портала Azure либо выбрав **Переключение каталога**. Чтобы выбрать и скопировать идентификатор клиента, выполните поиск по запросу "Azure Active Directory" на портале, затем выберите параметр **Свойства** и скопируйте значение, отображаемое в поле **Идентификатор каталога**. Чтобы найти идентификатор подписки, выполните поиск по запросу "Подписки", а затем выберите идентификатор соответствующей подписки.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

Select-AzSubscription <subscriptionId>
```

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az account set --subscription <subscriptionId/name>
az account show
```

> [!NOTE]
> При подключении подписки (или одной или нескольких групп ресурсов в подписке) с помощью описанного здесь способа поставщик ресурсов **Microsoft. ManagedServices** будет зарегистрирован для этой подписки.

## <a name="define-roles-and-permissions"></a>Определение ролей и разрешений

Как поставщик услуг вы можете выполнять для одного клиента несколько задач, требующих разных прав доступа для нескольких областей. Вы можете определить столько авторизаций, сколько требуется, чтобы [назначить встроенные роли управления доступом на основе ролей (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) пользователям в арендаторе.

Чтобы упростить управление, для каждой роли рекомендуется использовать группы пользователей Azure AD. Благодаря этому можно добавлять или удалять отдельных пользователей в группе, а не назначать разрешения непосредственно этому пользователю. Вы также можете назначить роли субъекту-службе. Обязательно следуйте принципам минимальных привилегий, чтобы у пользователей были только разрешения, необходимые для выполнения их обязанностей. Рекомендации и сведения о поддерживаемых ролях см. в разделе [Арендаторы, роли и пользователи в сценариях Azure Lighthouse](../concepts/tenants-users-roles.md).

Чтобы определить авторизации, вам необходимо знать значения идентификатора для каждого пользователя, группы пользователей или субъекта-службы, которым требуется предоставить доступ. Вам также потребуется идентификатор определения роли для каждой встроенной роли, которую вы хотите назначить. Если их у вас еще нет, вы можете получить их одним из следующих способов.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

# To retrieve the objectId for an Azure AD group
(Get-AzADGroup -DisplayName '<yourGroupName>').id

# To retrieve the objectId for an Azure AD user
(Get-AzADUser -UserPrincipalName '<yourUPN>').id

# To retrieve the objectId for an SPN
(Get-AzADApplication -DisplayName '<appDisplayName>').objectId

# To retrieve role definition IDs
(Get-AzRoleDefinition -Name '<roleName>').id
```

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

# To retrieve the objectId for an Azure AD group
az ad group list --query "[?displayName == '<yourGroupName>'].objectId" --output tsv

# To retrieve the objectId for an Azure AD user
az ad user show --upn-or-object-id "<yourUPN>" –-query "objectId" --output tsv

# To retrieve the objectId for an SPN
az ad sp list --query "[?displayName == '<spDisplayName>'].objectId" --output tsv

# To retrieve role definition IDs
az role definition list --name "<roleName>" | grep name
```
> [!TIP]
> Рекомендуется назначать роль [Managed Services Registration Assignment Delete](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#managed-services-registration-assignment-delete-role) (Удаление назначенных регистраций управляемых служб) при подключении клиента, чтобы пользователи в арендаторе позже могли при необходимости [удалить доступ к делегированию](#remove-access-to-a-delegation). Если эта роль не назначена, делегированные ресурсы могут быть удалены только пользователем в арендаторе клиента.

## <a name="create-an-azure-resource-manager-template"></a>Создание шаблона Azure Resource Manager

Чтобы подключить клиента, необходимо создать шаблон [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/) вашего предложения, который содержит приведенные ниже сведения. Значения **mspOfferName** и **mspOfferDescription** видимы для клиента при просмотре сведений о предложении на странице [Поставщики услуг](view-manage-service-providers.md) на портале Azure.

|Поле  |Определение  |
|---------|---------|
|**mspOfferName**     |Имя, описывающее это определение. Это значение отображается для клиента в виде заголовка предложения.         |
|**mspOfferDescription**     |Краткое описание предложения (например, "Предложение для управления виртуальной машиной Contoso")      |
|**managedByTenantId**     |Идентификатор клиента         |
|**Авторизации**     |Значения **principalId** для пользователей/групп/имен субъектов-служб из клиента, каждое из которых имеет параметр **principalIdDisplayName**, помогающий клиенту понять назначение авторизации, и сопоставленное со встроенным значением **roleDefinitionId**, определяющее уровень доступа         |

Чтобы подключить подписку клиента, используйте соответствующий шаблон Azure Resource Manager, который предоставляется в [репозитории с примерами](https://github.com/Azure/Azure-Lighthouse-samples/), вместе с соответствующим файлом параметров, который вы затем изменяете в соответствии со своей конфигурацией и определяете свои авторизации. Отдельные шаблоны предоставляются в зависимости от того, подключаете ли вы всю подписку, группу ресурсов или несколько групп ресурсов в рамках подписки. Мы также предоставляем шаблон для клиентов, которые приобрели предложение управляемых служб, опубликованное в Azure Marketplace, если вы предпочитаете таким образом подключать свои подписки.

|Чтобы подключить  |Используйте этот шаблон Azure Resource Manager  |И измените этот файл параметров |
|---------|---------|---------|
|Subscription   |[delegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/delegated-resource-management/delegatedResourceManagement.json)  |[delegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/delegated-resource-management/delegatedResourceManagement.parameters.json)    |
|группа ресурсов.   |[rgDelegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.json)  |[rgDelegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.parameters.json)    |
|Несколько групп ресурсов в рамках подписки   |[multipleRgDelegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management/multipleRgDelegatedResourceManagement.json)  |[multipleRgDelegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management/multipleRgDelegatedResourceManagement.parameters.json)    |
|Подписка (при использовании предложения, опубликованного в Azure Marketplace)   |[marketplaceDelegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/marketplace-delegated-resource-management/marketplaceDelegatedResourceManagement.json)  |[marketplaceDelegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/marketplace-delegated-resource-management/marketplaceDelegatedResourceManagement.parameters.json)    |

> [!IMPORTANT]
> Для описанного здесь процесса требуется отдельное развертывание для каждой подключенной подписки. Требуются также отдельные развертывания, если вы подключаете несколько групп ресурсов в разных подписках. Тем не менее подключение нескольких групп ресурсов в рамках одной подписки может быть выполнено за одно развертывание.
>
> Для нескольких предложений, применяемых к одной подписке (или к группам ресурсов в рамках подписки), также необходимы отдельные развертывания. Каждое применимое предложение должно использовать разные значения **mspOfferName**.

В следующем примере показан файл **delegatedResourceManagement.parameters.json**, который будет использоваться для подключения подписки. Файлы параметров группы ресурсов (расположенные в папке [rg-delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management)) аналогичны, но также содержат параметр **rgName** для определения конкретных групп ресурсов, которые должны быть подключены.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "mspOfferName": {
            "value": "Fabrikam Managed Services - Interstellar"
        },
        "mspOfferDescription": {
            "value": "Fabrikam Managed Services - Interstellar"
        },
        "managedByTenantId": {
            "value": "df4602a3-920c-435f-98c4-49ff031b9ef6"
        },
        "authorizations": {
            "value": [
                {
                    "principalId": "0019bcfb-6d35-48c1-a491-a701cf73b419",
                    "principalIdDisplayName": "Tier 1 Support",
                    "roleDefinitionId": "b24988ac-6180-42a0-ab88-20f7382dd24c"
                },
                {
                    "principalId": "0019bcfb-6d35-48c1-a491-a701cf73b419",
                    "principalIdDisplayName": "Tier 1 Support",
                    "roleDefinitionId": "36243c78-bf99-498c-9df9-86d9f8d28608"
                },
                {
                    "principalId": "0afd8497-7bff-4873-a7ff-b19a6b7b332c",
                    "principalIdDisplayName": "Tier 2 Support",
                    "roleDefinitionId": "acdd72a7-3385-48ef-bd42-f606fba81ae7"
                },
                {
                    "principalId": "9fe47fff-5655-4779-b726-2cf02b07c7c7",
                    "principalIdDisplayName": "Service Automation Account",
                    "roleDefinitionId": "b24988ac-6180-42a0-ab88-20f7382dd24c"
                },
                {
                    "principalId": "3kl47fff-5655-4779-b726-2cf02b05c7c4",
                    "principalIdDisplayName": "Policy Automation Account",
                    "roleDefinitionId": "18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
                    "delegatedRoleDefinitionIds": [
                        "b24988ac-6180-42a0-ab88-20f7382dd24c",
                        "92aaf0da-9dab-42b6-94a3-d43ce8d16293"
                    ]
                }
            ]
        }
    }
}
```
Последняя авторизация в приведенном выше примере добавляет параметр **principalId** с ролью "Администратор доступа пользователя" (18d7d88d-d35e-4fb5-a5c3-7773c20a72d9). При назначении этой роли вы должны включить свойство **delegatedRoleDefinitionIds** и одну или несколько встроенных ролей. Пользователь, созданный с этой авторизацией, сможет назначить эти встроенные роли [управляемым удостоверениям](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview), которые нужны для [развертывания политик, которые можно исправить](deploy-policy-remediation.md). К этому пользователю не будут применяться никакие другие разрешения, обычно связанные с ролью "Администратор доступа пользователей".

## <a name="deploy-the-azure-resource-manager-templates"></a>Развертывание шаблонов Azure Resource Manager

После обновления файла параметров клиент должен развернуть шаблон Azure Resource Manager в своем арендаторе на уровне подписки. Отдельное развертывание необходимо для каждой подписки, которую вы хотите подключить к системе делегированного управления ресурсами Azure (или для каждой подписки, которая содержит группы ресурсов, которые вы хотите подключить).

Так как это развертывание уровня подписки, его невозможно инициировать на портале Azure. Развертывание можно выполнить с помощью PowerShell или Azure CLI, как показано ниже.

> [!IMPORTANT]
> Развертывание должно быть выполнено с помощью не гостевой учетной записи в клиенте пользователя, которая имеет [встроенную роль владельца](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner) для подключаемой подписки (или которая содержит группы ресурсов, которые подключаются). Чтобы найти пользователей, которые могут делегировать подписку, пользователь клиента может выбрать подписку на портале Azure, открыть **управление доступом (IAM)** и [просмотреть всех пользователей с ролью владельца](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal#view-roles-and-permissions).

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

# Deploy Azure Resource Manager template using template and parameter file locally
New-AzDeployment -Name <deploymentName> `
                 -Location <AzureRegion> `
                 -TemplateFile <pathToTemplateFile> `
                 -TemplateParameterFile <pathToParameterFile> `
                 -Verbose

# Deploy Azure Resource Manager template that is located externally
New-AzDeployment -Name <deploymentName> `
                 -Location <AzureRegion> `
                 -TemplateUri <templateUri> `
                 -TemplateParameterUri <parameterUri> `
                 -Verbose
```

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

# Deploy Azure Resource Manager template using template and parameter file locally
az deployment create –-name <deploymentName> \
                     --location <AzureRegion> \
                     --template-file <pathToTemplateFile> \
                     --parameters <parameters/parameterFile> \
                     --verbose

# Deploy external Azure Resource Manager template, with local parameter file
az deployment create –-name <deploymentName \
                     –-location <AzureRegion> \
                     --template-uri <templateUri> \
                     --parameters <parameterFile> \
                     --verbose
```

## <a name="confirm-successful-onboarding"></a>Подтверждение успешного подключения

После того, как подписка клиента была успешно подключена к системе делегированного управления ресурсами Azure, пользователи в клиенте поставщика услуг смогут видеть подписку и ее ресурсы (если им был предоставлен доступ к ней в рамках вышеописанного процесса, индивидуально или в качестве участника группы Azure AD с соответствующими разрешениями). Чтобы подтвердить это, убедитесь, что подписка отображается одним из следующих способов.  

### <a name="azure-portal"></a>портала Azure

В клиенте поставщика услуг выполните приведенные ниже действия.

1. Перейдите на страницу [Мои клиенты](view-manage-customers.md).
2. Выберите **Клиенты**.
3. Убедитесь, что вы видите подписки с именем предложения, которое вы указали в шаблоне Resource Manager.

> [!IMPORTANT]
> Чтобы просмотреть делегированную подписку на странице [Мои клиенты](view-manage-customers.md), пользователям в арендаторе поставщика услуг должна быть предоставлена роль [Читатель](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#reader) (или другая встроенная роль, которая обеспечивает доступ для чтения) при подключении подписки для делегированного управления ресурсами Azure.

В клиенте пользователя выполните следующее.

1. Перейдите на страницу [Поставщики услуг](view-manage-service-providers.md).
2. Выберите **Предложения поставщика услуг**.
3. Убедитесь, что вы видите подписки с именем предложения, которое вы указали в шаблоне Resource Manager.

> [!NOTE]
> После завершения развертывания может пройти несколько минут, прежде чем обновления отобразятся на портале Azure.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

Get-AzContext
```

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az account list
```

## <a name="remove-access-to-a-delegation"></a>Удаление доступа к делегированию

По умолчанию пользователь в арендаторе клиента с соответствующими разрешениями может удалить доступ к ресурсам, которые были делегированы поставщику услуг на странице [Поставщики услуг](view-manage-service-providers.md#add-or-remove-service-provider-offers) на портале Azure.

Если вы добавили роль [Managed Services Registration Assignment Delete](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#managed-services-registration-assignment-delete-role) (Удаление назначенных регистраций управляемых служб) при подключении клиента для делегированного управления ресурсами Azure, то эти пользователи в арендаторе также могут удалить делегирование. При этом пользователи в арендаторе поставщика услуг не больше смогут обращаться к ресурсам, которые ранее были делегированы.

В приведенном ниже примере показано назначение роли **Managed Services Registration Assignment Delete** (Удаление назначенных регистраций управляемых служб), которую можно добавить в файл параметров.

```json
    "authorizations": [ 
        { 
            "principalId": "cfa7496e-a619-4a14-a740-85c5ad2063bb", 
            "principalIdDisplayName": "MSP Operators", 
            "roleDefinitionId": "91c1777a-f3dc-4fae-b103-61d183457e46" 
        } 
    ] 
```

Пользователь с этим разрешением может удалить делегирование одним из следующих способов.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

# Sign in as a user from the managing tenant directory 

Login-AzAccount

# Select the subscription that is delegated - or contains the delegated resource group(s)

Select-AzSubscription -SubscriptionName "<subscriptionName>"

# Get the registration assignment

Get-AzManagedServicesAssignment -Scope "/subscriptions/{delegatedSubscriptionId}"

# Delete the registration assignment

Remove-AzManagedServicesAssignment -ResourceId "/subscriptions/{delegatedSubscriptionId}/providers/Microsoft.ManagedServices/registrationAssignments/{assignmentGuid}"
```

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

# Sign in as a user from the managing tenant directory

az login

# Select the subscription that is delegated – or contains the delegated resource group(s)

az account set -s <subscriptionId/name>

# List registration assignments

az managedservices assignment list

# Delete the registration assignment

az managedservices assignment delete --assignment <id or full resourceId>
```

## <a name="next-steps"></a>Дальнейшие действия

- Узнайте больше об [интерфейсах управления для различных клиентов](../concepts/cross-tenant-management-experience.md).
- [Просматривайте клиентов и управляйте ими](view-manage-customers.md), перейдя в раздел **Мои клиенты** на портале Azure.
