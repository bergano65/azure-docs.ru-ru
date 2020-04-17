---
title: Подключение клиента к системе делегированного управления ресурсами Azure
description: Узнайте, как подключить клиента к системе делегированного управления ресурсами Azure, предоставив доступ к ресурсам и возможность управления ими через собственный клиент.
ms.date: 04/16/2020
ms.topic: conceptual
ms.openlocfilehash: 22c96d43f3d5f284c2cba995eb33f5f8cd238659
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81481698"
---
# <a name="onboard-a-customer-to-azure-delegated-resource-management"></a>Подключение клиента к системе делегированного управления ресурсами Azure

В этой статье объясняется, как вы в роли поставщика услуг можете подключить клиента к системе делегированного управления ресурсами Azure, позволяя получать доступ к своим делегированным ресурсам (подпискам и (или) группам ресурсов) и управлять ими через собственный клиент Azure Active Directory (Azure AD). В то время как мы будем ссылаться на поставщиков услуг и клиентов здесь, [предприятия, управляющие несколькими арендаторами,](../concepts/enterprise.md) могут использовать тот же процесс для консолидации своего управленческого опыта.

Вы можете повторить этот процесс, если управляете ресурсами для нескольких клиентов. Затем, когда полномочный пользователь входит в клиент, он может быть авторизован во всех областях аренды клиента для выполнения операций управления без необходимости входа в каждый отдельный клиент.

Чтобы отслеживать свое влияние по запросам клиентов и получать признание, связывайте свой идентификатор партнерской сети Майкрософт (MPN) с по крайней мере одной учетной записью пользователя, которая имеет доступ к каждой из ваших подписок на борту. Обратите внимание, что эту привязку необходимо выполнять в клиенте поставщика службы. Для простоты мы рекомендуем создать в клиенте основной учетной записи службы, которая связана с вашим идентификатором MPN, и предоставить ему доступ к читателю каждому клиенту, на борту которого вы являетесь. Для получения дополнительной информации см. [Ссылку на идентификатор партнера с вашими учетными записями Azure.](../../billing/billing-partner-admin-link-started.md) 

> [!NOTE]
> Клиенты могут также быть подключены при покупке предложения управляемых служб (общедоступных или частных), которое вы опубликовали в Azure Marketplace. Дополнительные сведения см. в статье [Publish a managed services offer to Azure Marketplace](publish-managed-services-offers.md) (Публикация предложения управляемых служб в Azure Marketplace). Вы также можете использовать процесс посадки, описанный здесь, вместе с предложением, опубликованным на Azure Marketplace.

Для процесса подключения требуется, чтобы действия выполнялись в клиенте поставщика услуг и клиенте пользователя. Все эти шаги описаны в данной статье.

## <a name="gather-tenant-and-subscription-details"></a>Сбор сведений о клиентах и подписках

Чтобы подключить клиент пользователя, у него должна быть активная подписка Azure. Для этого потребуется следующее:

- идентификатор клиента поставщика услуг (где вы будете управлять ресурсами клиента);
- идентификатор пользовательского клиента (в котором будут ресурсы, управляемые поставщиком услуг);
- Идоверки подписки для каждой конкретной подписки в клиенте, которые будут управляться поставщиком услуг (или содержащей группу ресурсов (ы), которая будет управляться поставщиком услуг).

> [!NOTE]
> Даже если вы хотите находиться на борту только одной или нескольких групп ресурсов в рамках подписки, развертывание должно быть выполнено на уровне подписки, поэтому идентификатор подписки вам понадобится.

Если эти значения идентификаторов уже не имеют, вы можете получить их одним из следующих способов. Будьте уверены и используйте эти точные значения в развертывании.

### <a name="azure-portal"></a>Портал Azure

Идентификатор клиента можно увидеть, наведя указатель мыши на имя учетной записи в правом верхнем углу портала Azure либо выбрав **Переключение каталога**. Чтобы выбрать и скопировать идентификатор клиента, выполните поиск по запросу "Azure Active Directory" на портале, затем выберите параметр **Свойства** и скопируйте значение, отображаемое в поле **Идентификатор каталога**. Чтобы найти идентификатор подписки в клиенте-арендаторе, выберите "Подписку", а затем выберите соответствующий идентификатор подписки.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

Select-AzSubscription <subscriptionId>
```

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az account set --subscription <subscriptionId/name>
az account show
```

> [!NOTE]
> При подключении подписки (или одной или нескольких групп ресурсов в подписке) с помощью описанного здесь способа поставщик ресурсов **Microsoft. ManagedServices** будет зарегистрирован для этой подписки.

## <a name="define-roles-and-permissions"></a>Определение ролей и разрешений

Как поставщик услуг вы можете выполнять для одного клиента несколько задач, требующих разных прав доступа для нескольких областей. Вы можете определить столько авторизаций, сколько требуется, чтобы [назначить встроенные роли управления доступом на основе ролей (RBAC)](../../role-based-access-control/built-in-roles.md) пользователям в арендаторе.

Чтобы упростить управление, для каждой роли рекомендуется использовать группы пользователей Azure AD. Благодаря этому можно добавлять или удалять отдельных пользователей в группе, а не назначать разрешения непосредственно этому пользователю. Вы также можете назначить роли субъекту-службе. Обязательно следуйте принципам минимальных привилегий, чтобы у пользователей были только разрешения, необходимые для выполнения их обязанностей. Рекомендации и сведения о поддерживаемых ролях см. в разделе [Арендаторы, роли и пользователи в сценариях Azure Lighthouse](../concepts/tenants-users-roles.md).

> [!IMPORTANT]
> Для добавления разрешений для группы Azure AD **тип группы** должен быть **безопасности,** а не **Office 365.** Эта опция выбирается при создании группы. Дополнительные сведения см. в разделе [Создание базовой группы и добавление членов с помощью Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

Для определения авторизаций необходимо знать значения идентификатора для каждого пользователя, группы пользователей или директора службы в арендаторе поставщика услуг, к которому вы хотите предоставить доступ. Вам также потребуется идентификатор определения роли для каждой встроенной роли, которую вы хотите назначить. Если их у вас еще нет, вы можете получить их, запустив команды ниже из арендатора поставщика услуг.

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

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

# To retrieve the objectId for an Azure AD group
az ad group list --query "[?displayName == '<yourGroupName>'].objectId" --output tsv

# To retrieve the objectId for an Azure AD user
az ad user show --id "<yourUPN>" --query "objectId" --output tsv

# To retrieve the objectId for an SPN
az ad sp list --query "[?displayName == '<spDisplayName>'].objectId" --output tsv

# To retrieve role definition IDs
az role definition list --name "<roleName>" | grep name
```
> [!TIP]
> Рекомендуется назначать роль [Managed Services Registration Assignment Delete](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) (Удаление назначенных регистраций управляемых служб) при подключении клиента, чтобы пользователи в арендаторе позже могли при необходимости [удалить доступ к делегированию](#remove-access-to-a-delegation). Если эта роль не назначена, делегированные ресурсы могут быть удалены только пользователем в арендаторе клиента.

## <a name="create-an-azure-resource-manager-template"></a>Создание шаблона Azure Resource Manager

Чтобы подключить клиента, необходимо создать шаблон [Azure Resource Manager](../../azure-resource-manager/index.yml) вашего предложения, который содержит приведенные ниже сведения. Значения **mspOfferName** и **mspOfferDescription** будут видны клиенту при просмотре информации о предложениях на [странице поставщиков услуг](view-manage-service-providers.md) портала Azure.

|Поле  |Определение  |
|---------|---------|
|**mspOfferName**     |Имя, описывающее это определение. Это значение отображается для клиента в виде заголовка предложения.         |
|**mspOfferDescription**     |Краткое описание вашего предложения (например, "Contoso VM управления предложением").      |
|**managedByTenantId**     |Идентификатор клиента.          |
|**Разрешений**     |**Значения principalId** для пользователей/групп/SPN от вашего арендатора, каждый из которых имеет **principalIdDisplayName,** чтобы помочь клиенту понять цель авторизации, и отображены в встроенной **ролевойdefinitionId** значение, чтобы указать уровень доступа.      |

Процесс посадки требует шаблона менеджера ресурсов Azure (представленного в [репо образцы)](https://github.com/Azure/Azure-Lighthouse-samples/)и соответствующего файла параметров, который вы изменяете в соответствии с конфигурацией и определяют ваши авторизации.

Выбираемый шаблон будет зависеть от того, работаете ли вы на всей подписке, группе ресурсов или нескольких группах ресурсов в рамках подписки. Мы также предоставляем шаблон для клиентов, которые приобрели предложение управляемых служб, опубликованное в Azure Marketplace, если вы предпочитаете таким образом подключать свои подписки.

|Чтобы подключить  |Используйте этот шаблон Azure Resource Manager  |И измените этот файл параметров |
|---------|---------|---------|
|Подписка   |[delegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/delegated-resource-management/delegatedResourceManagement.json)  |[delegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/delegated-resource-management/delegatedResourceManagement.parameters.json)    |
|Группа ресурсов   |[rgDelegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.json)  |[rgDelegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.parameters.json)    |
|Несколько групп ресурсов в рамках подписки   |[multipleRgDelegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/rg-delegated-resource-management/multipleRgDelegatedResourceManagement.json)  |[multipleRgDelegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/rg-delegated-resource-management/multipleRgDelegatedResourceManagement.parameters.json)    |
|Подписка (при использовании предложения, опубликованного в Azure Marketplace)   |[marketplaceDelegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/marketplace-delegated-resource-management/marketplaceDelegatedResourceManagement.json)  |[marketplaceDelegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/marketplace-delegated-resource-management/marketplaceDelegatedResourceManagement.parameters.json)    |

> [!IMPORTANT]
> Описанный здесь процесс требует отдельного развертывания на уровне подписки для каждой подписки, направиваемых на борт, даже если вы находитесь в подписке в одном и том же клиенте. Отдельные развертывания также необходимы, если вы внедляете в несколько групп ресурсов в рамках разных подписок в одном и том же клиенте-арендаторе. Однако развертывание нескольких групп ресурсов в рамках одной подписки может осуществляться в рамках одного развертывания на уровне подписки.
>
> Для нескольких предложений, применяемых к одной подписке (или к группам ресурсов в рамках подписки), также необходимы отдельные развертывания. Каждое применимое предложение должно использовать разные значения **mspOfferName**.

В следующем примере показан модифицированный **делегированныйфайлуResourceManagement.parameters.json** файл, который можно использовать для посадки подписки. Файлы параметров группы ресурсов (расположенные в папке [rg-delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/rg-delegated-resource-management)) аналогичны, но также содержат параметр **rgName** для определения конкретных групп ресурсов, которые должны быть подключены.

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

Последняя авторизация в приведенном выше примере добавляет параметр **principalId** с ролью "Администратор доступа пользователя" (18d7d88d-d35e-4fb5-a5c3-7773c20a72d9). При назначении этой роли вы должны включить свойство **delegatedRoleDefinitionIds** и одну или несколько встроенных ролей. Пользователь, созданный в этой авторизации, сможет назначить эти встроенные роли [управляемым идентификаторам](../../active-directory/managed-identities-azure-resources/overview.md) в клиенте-арендаторе, что необходимо для [развертывания политик, которые могут быть исправлены.](deploy-policy-remediation.md) К этому пользователю не будут применяться никакие другие разрешения, обычно связанные с ролью "Администратор доступа пользователей".

## <a name="deploy-the-azure-resource-manager-templates"></a>Развертывание шаблонов Azure Resource Manager

После обновления файла параметров пользователь в клиенте должен развернуть шаблон Azure Resource Manager в своем клиенте в качестве развертывания на уровне подписки. Отдельное развертывание необходимо для каждой подписки, которую вы хотите подключить к системе делегированного управления ресурсами Azure (или для каждой подписки, которая содержит группы ресурсов, которые вы хотите подключить).

Так как это развертывание уровня подписки, его невозможно инициировать на портале Azure. Развертывание можно выполнить с помощью PowerShell или Azure CLI, как показано ниже.

> [!IMPORTANT]
> Развертывание на уровне подписки должно осуществляться учетной записью клиента, не являющейся гостем, в которой [встроенная роль владельца](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner) для подписки находится на борту (или содержащей группы ресурсов, находящихся на борту). Чтобы найти пользователей, которые могут делегировать подписку, пользователь клиента может выбрать подписку на портале Azure, открыть **управление доступом (IAM)** и [просмотреть всех пользователей с ролью владельца](../../role-based-access-control/role-assignments-list-portal.md#list-owners-of-a-subscription).

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

# Deploy Azure Resource Manager template using template and parameter file locally
New-AzSubscriptionDeployment -Name <deploymentName> `
                 -Location <AzureRegion> `
                 -TemplateFile <pathToTemplateFile> `
                 -TemplateParameterFile <pathToParameterFile> `
                 -Verbose

# Deploy Azure Resource Manager template that is located externally
New-AzSubscriptionDeployment -Name <deploymentName> `
                 -Location <AzureRegion> `
                 -TemplateUri <templateUri> `
                 -TemplateParameterUri <parameterUri> `
                 -Verbose
```

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

# Deploy Azure Resource Manager template using template and parameter file locally
az deployment create --name <deploymentName> \
                     --location <AzureRegion> \
                     --template-file <pathToTemplateFile> \
                     --parameters <parameters/parameterFile> \
                     --verbose

# Deploy external Azure Resource Manager template, with local parameter file
az deployment create --name <deploymentName> \
                     --location <AzureRegion> \
                     --template-uri <templateUri> \
                     --parameters <parameterFile> \
                     --verbose
```

## <a name="confirm-successful-onboarding"></a>Подтверждение успешного подключения

После того, как подписка клиента была успешно подключена к системе делегированного управления ресурсами Azure, пользователи в клиенте поставщика услуг смогут видеть подписку и ее ресурсы (если им был предоставлен доступ к ней в рамках вышеописанного процесса, индивидуально или в качестве участника группы Azure AD с соответствующими разрешениями). Чтобы подтвердить это, убедитесь, что подписка отображается одним из следующих способов.  

### <a name="azure-portal"></a>Портал Azure

В клиенте поставщика услуг выполните приведенные ниже действия.

1. Перейдите на страницу [Мои клиенты](view-manage-customers.md).
2. Выберите **Клиенты**.
3. Убедитесь, что вы видите подписки с именем предложения, которое вы указали в шаблоне Resource Manager.

> [!IMPORTANT]
> Чтобы просмотреть делегированную подписку на странице [Мои клиенты](view-manage-customers.md), пользователям в арендаторе поставщика услуг должна быть предоставлена роль [Читатель](../../role-based-access-control/built-in-roles.md#reader) (или другая встроенная роль, которая обеспечивает доступ для чтения) при подключении подписки для делегированного управления ресурсами Azure.

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

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az account list
```

## <a name="remove-access-to-a-delegation"></a>Удаление доступа к делегированию

По умолчанию пользователи клиента,имеющие соответствующие разрешения, могут удалить доступ поставщика услуг к делегированным ресурсам на [странице поставщиков услуг](view-manage-service-providers.md#add-or-remove-service-provider-offers) портала Azure. При этом ни один пользователь в арендаторе поставщика услуг не сможет получить доступ к ресурсам, которые были ранее делегированы.

Если у вас есть пользователи с помощью роли [назначения за регистрации управляемых служб](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) при посадке клиента для управления ресурсами Azure, эти пользователи также смогут удалить делегацию.

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

### <a name="azure-portal"></a>Портал Azure

1. Перейдите на страницу [Мои клиенты](view-manage-customers.md).
2. Выберите **делегации**.
3. Найдите делегацию, которую вы хотите удалить, а затем выберите значок мусорного бака, который отображается в его строке.

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

### <a name="azure-cli"></a>Azure CLI

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

## <a name="next-steps"></a>Следующие шаги

- Узнайте больше об [интерфейсах управления для различных клиентов](../concepts/cross-tenant-management-experience.md).
- [Просматривайте клиентов и управляйте ими](view-manage-customers.md), перейдя в раздел **Мои клиенты** на портале Azure.
