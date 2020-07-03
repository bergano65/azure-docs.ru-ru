---
title: Подключение клиента к системе делегированного управления ресурсами Azure
description: Узнайте, как подключить клиента к системе делегированного управления ресурсами Azure, предоставив доступ к ресурсам и возможность управления ими через собственный клиент.
ms.date: 04/24/2020
ms.topic: conceptual
ms.openlocfilehash: 2b8bf3125dd97397f83a2a2cbf23090bce41ad40
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/26/2020
ms.locfileid: "82161114"
---
# <a name="onboard-a-customer-to-azure-delegated-resource-management"></a>Подключение клиента к системе делегированного управления ресурсами Azure

В этой статье объясняется, как вы в роли поставщика услуг можете подключить клиента к системе делегированного управления ресурсами Azure, позволяя получать доступ к своим делегированным ресурсам (подпискам и (или) группам ресурсов) и управлять ими через собственный клиент Azure Active Directory (Azure AD). Хотя мы будем называть поставщиков услуг и клиентов здесь, [предприятия, управляющие несколькими клиентами](../concepts/enterprise.md) , могут использовать один и тот же процесс для консолидации своих возможностей управления.

Вы можете повторить этот процесс, если управляете ресурсами для нескольких клиентов. Затем, когда полномочный пользователь входит в клиент, он может быть авторизован во всех областях аренды клиента для выполнения операций управления без необходимости входа в каждый отдельный клиент.

Чтобы отразить влияние на участие клиентов и получить распознавание, свяжите идентификатор Microsoft Partner Network (MPN) с по крайней мере одной учетной записью пользователя, имеющей доступ к каждой из ваших подключенных подписок. Обратите внимание, что эту привязку необходимо выполнять в клиенте поставщика службы. Для простоты рекомендуется создать в клиенте учетную запись субъекта-службы, связанную с ИДЕНТИФИКАТОРом MPN, и предоставить ей доступ для чтения каждому заказчику. Дополнительные сведения см. в статье [связывание идентификатора партнера с учетными записями Azure](../../billing/billing-partner-admin-link-started.md). 

> [!NOTE]
> Клиенты могут также быть подключены при покупке предложения управляемых служб (общедоступных или частных), которое вы опубликовали в Azure Marketplace. Дополнительные сведения см. в статье [Publish a managed services offer to Azure Marketplace](publish-managed-services-offers.md) (Публикация предложения управляемых служб в Azure Marketplace). Вы также можете использовать процесс адаптации, описанный здесь, вместе с предложением, опубликованным в Azure Marketplace.

Для процесса подключения требуется, чтобы действия выполнялись в клиенте поставщика услуг и клиенте пользователя. Все эти шаги описаны в данной статье.

## <a name="gather-tenant-and-subscription-details"></a>Сбор сведений о клиентах и подписках

Чтобы подключить клиент пользователя, у него должна быть активная подписка Azure. Для этого потребуется следующее:

- идентификатор клиента поставщика услуг (где вы будете управлять ресурсами клиента);
- идентификатор пользовательского клиента (в котором будут ресурсы, управляемые поставщиком услуг);
- Идентификаторы подписок для каждой конкретной подписки в клиенте клиента, которые будут управляться поставщиком услуг (или содержат группы ресурсов, которые будут управляться поставщиком услуг).

> [!NOTE]
> Даже если вы хотите подключить одну или несколько групп ресурсов в рамках подписки, развертывание должно выполняться на уровне подписки, поэтому вам потребуется идентификатор подписки.

Если эти значения ИДЕНТИФИКАТОРов еще не установлены, их можно получить одним из следующих способов. Обязательно используйте эти точные значения в развертывании.

### <a name="azure-portal"></a>Портал Azure

Идентификатор клиента можно увидеть, наведя указатель мыши на имя учетной записи в правом верхнем углу портала Azure либо выбрав **Переключение каталога**. Чтобы выбрать и скопировать идентификатор клиента, выполните поиск по запросу "Azure Active Directory" на портале, затем выберите параметр **Свойства** и скопируйте значение, отображаемое в поле **Идентификатор каталога**. Чтобы найти идентификатор подписки в клиенте клиента, выполните поиск по фразе "подписки", а затем выберите соответствующий идентификатор подписки.

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
> Чтобы добавить разрешения для группы Azure AD, **Тип группы** должен быть " **Безопасность** ", а не **Office 365**. Этот параметр выбирается при создании группы. Дополнительные сведения см. в разделе [Создание базовой группы и добавление членов с помощью Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

Чтобы определить разрешения, необходимо знать значения ИДЕНТИФИКАТОРов для каждого пользователя, группы пользователей или субъекта-службы в клиенте поставщика услуг, которому требуется предоставить доступ. Вам также потребуется идентификатор определения роли для каждой встроенной роли, которую вы хотите назначить. Если у вас их еще нет, их можно получить, выполнив приведенные ниже команды в клиенте поставщика услуг.

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
> Рекомендуется назначать роль [Managed Services Registration Assignment Delete](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) (Удаление назначенных регистраций управляемых служб) при подключении клиента, чтобы пользователи в арендаторе позже могли при необходимости [удалить доступ к делегированию](remove-delegation.md). Если эта роль не назначена, делегированные ресурсы могут быть удалены только пользователем в арендаторе клиента.

## <a name="create-an-azure-resource-manager-template"></a>Создание шаблона Azure Resource Manager

Чтобы подключить клиента, необходимо создать шаблон [Azure Resource Manager](../../azure-resource-manager/index.yml) вашего предложения, который содержит приведенные ниже сведения. Значения **мспоффернаме** и **мспоффердескриптион** будут видны клиенту при просмотре сведений о предложении на [странице поставщики услуг](view-manage-service-providers.md) портал Azure.

|Поле  |Определение  |
|---------|---------|
|**mspOfferName**     |Имя, описывающее это определение. Это значение отображается для клиента в виде заголовка предложения.         |
|**mspOfferDescription**     |Краткое описание предложения (например, "Contoso VM Management предложение").      |
|**managedByTenantId**     |Идентификатор клиента.          |
|**авторизации**     |Значения **principalId** для пользователей/групп или имен участников-служб из клиента, каждый из которых имеет **принЦипалиддисплайнаме** , чтобы помочь клиенту понять назначение авторизации и сопоставить его со встроенным значением **roleDefinitionId** , чтобы указать уровень доступа.      |

Для процесса адаптации требуется шаблон Azure Resource Manager (в нашем [репозитории примеров](https://github.com/Azure/Azure-Lighthouse-samples/)) и соответствующий файл параметров, который изменяется в соответствии с конфигурацией, и определяются разрешения.

Выбранный шаблон будет зависеть от того, выполняется ли подключение ко всей подписке, группе ресурсов или нескольким группам ресурсов в рамках подписки. Мы также предоставляем шаблон для клиентов, которые приобрели предложение управляемых служб, опубликованное в Azure Marketplace, если вы предпочитаете таким образом подключать свои подписки.

|Чтобы подключить  |Используйте этот шаблон Azure Resource Manager  |И измените этот файл параметров |
|---------|---------|---------|
|Подписка   |[delegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/delegated-resource-management/delegatedResourceManagement.json)  |[delegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/delegated-resource-management/delegatedResourceManagement.parameters.json)    |
|Группа ресурсов   |[rgDelegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.json)  |[rgDelegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.parameters.json)    |
|Несколько групп ресурсов в рамках подписки   |[multipleRgDelegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/rg-delegated-resource-management/multipleRgDelegatedResourceManagement.json)  |[multipleRgDelegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/rg-delegated-resource-management/multipleRgDelegatedResourceManagement.parameters.json)    |
|Подписка (при использовании предложения, опубликованного в Azure Marketplace)   |[marketplaceDelegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/marketplace-delegated-resource-management/marketplaceDelegatedResourceManagement.json)  |[marketplaceDelegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/marketplace-delegated-resource-management/marketplaceDelegatedResourceManagement.parameters.json)    |

> [!IMPORTANT]
> Описанный здесь процесс требует отдельного развертывания на уровне подписки для каждой подключенной подписки, даже если подписки направляются в одном клиенте клиента. Отдельные развертывания также требуются при подключении нескольких групп ресурсов в разных подписках одного клиента. Однако подключение нескольких групп ресурсов в одной подписке может выполняться в одном развертывании на уровне подписки.
>
> Для нескольких предложений, применяемых к одной подписке (или к группам ресурсов в рамках подписки), также необходимы отдельные развертывания. Каждое применимое предложение должно использовать разные значения **mspOfferName**.

В следующем примере показан измененный файл **делегатедресаурцеманажемент. parameters. JSON** , который можно использовать для подключения подписки. Файлы параметров группы ресурсов (расположенные в папке [rg-delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/rg-delegated-resource-management)) аналогичны, но также содержат параметр **rgName** для определения конкретных групп ресурсов, которые должны быть подключены.

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

Последняя авторизация в приведенном выше примере добавляет параметр **principalId** с ролью "Администратор доступа пользователя" (18d7d88d-d35e-4fb5-a5c3-7773c20a72d9). При назначении этой роли вы должны включить свойство **delegatedRoleDefinitionIds** и одну или несколько встроенных ролей. Пользователь, созданный в этой авторизации, сможет назначить эти встроенные роли [управляемым удостоверениям](../../active-directory/managed-identities-azure-resources/overview.md) в клиенте клиента, который необходим для [развертывания политик, которые можно исправить](deploy-policy-remediation.md). К этому пользователю не будут применяться никакие другие разрешения, обычно связанные с ролью "Администратор доступа пользователей".

## <a name="deploy-the-azure-resource-manager-templates"></a>Развертывание шаблонов Azure Resource Manager

После обновления файла параметров пользователь в клиенте клиента должен развернуть шаблон Azure Resource Manager в своем клиенте в качестве развертывания на уровне подписки. Отдельное развертывание необходимо для каждой подписки, которую вы хотите подключить к системе делегированного управления ресурсами Azure (или для каждой подписки, которая содержит группы ресурсов, которые вы хотите подключить).

Так как это развертывание уровня подписки, его невозможно инициировать на портале Azure. Развертывание можно выполнить с помощью PowerShell или Azure CLI, как показано ниже.

> [!IMPORTANT]
> Это развертывание на уровне подписки должно выполняться учетной записью, отличной от гостевой, в клиенте клиента, у которого есть [Встроенная роль владельца](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner) подписки (или содержащая группы ресурсов, для которых выполняется подключение). Чтобы найти пользователей, которые могут делегировать подписку, пользователь клиента может выбрать подписку на портале Azure, открыть **управление доступом (IAM)** и [просмотреть всех пользователей с ролью владельца](../../role-based-access-control/role-assignments-list-portal.md#list-owners-of-a-subscription).
>
> Если подписка была создана с помощью [программы поставщика облачных решений (CSP)](../concepts/cloud-solution-provider.md), любой пользователь, имеющий роль [агента администратора](https://docs.microsoft.com/partner-center/permissions-overview#manage-commercial-transactions-in-partner-center-azure-ad-and-csp-roles) в клиенте поставщика услуг, может выполнить развертывание.

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

## <a name="next-steps"></a>Следующие шаги

- Узнайте больше об [интерфейсах управления для различных клиентов](../concepts/cross-tenant-management-experience.md).
- [Просматривайте клиентов и управляйте ими](view-manage-customers.md), перейдя в раздел **Мои клиенты** на портале Azure.
- Узнайте, как [Удалить доступ к делегированию](remove-delegation.md) , которое ранее было подключено.
