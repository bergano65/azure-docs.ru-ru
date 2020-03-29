---
title: Мониторинг Azure AD B2C с помощью Azure Monitor
titleSuffix: Azure AD B2C
description: Узнайте, как войти в события Azure AD B2C с помощью делегированного управления ресурсами.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.author: mimart
ms.subservice: B2C
ms.date: 02/10/2020
ms.openlocfilehash: acba378badb41324b2124b84833407da920a0e00
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78190064"
---
# <a name="monitor-azure-ad-b2c-with-azure-monitor"></a>Мониторинг Azure AD B2C с помощью Azure Monitor

Используйте Azure Monitor для маршрутизации журналов По входе в Azure Active Directory B2C (Azure AD B2C) [в](view-audit-logs.md) различных решениях мониторинга. Вы можете сохранить журналы для долгосрочного использования или интегрироваться с сторонними инструментами управления безопасностью и событиями (SIEM) для получения информации о вашей среде.

Вы можете маршрутизать события журнала до:

* [Учетная запись хранения](../storage/blobs/storage-blobs-introduction.md)Azure .
* [Концентратор событий](../event-hubs/event-hubs-about.md) Azure (и интегрируется с экземплярами Splunk и Sumo Logic).
* [Рабочее пространство analytics log](../azure-monitor/platform/resource-logs-collect-workspace.md) (для анализа данных, создания панелей мониторинга и оповещения о конкретных событиях).

![Azure Monitor](./media/azure-monitor/azure-monitor-flow.png)

## <a name="prerequisites"></a>Предварительные требования

Чтобы завершить этапы в этой статье, вы развернете шаблон менеджера ресурсов Azure с помощью модуля Azure PowerShell.

* [Версия модуля Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps) 6.13.1 или выше

Вы также можете использовать [облачную оболочку Azure,](https://shell.azure.com)которая включает последнюю версию модуля Azure PowerShell.

## <a name="delegated-resource-management"></a>Делегированное управление ресурсами

Azure AD B2C использует [мониторинг активного каталога Azure.](../active-directory/reports-monitoring/overview-monitoring.md) Для включения *диагностических настроек* в Active Directory Azure в вашем наемщике Azure AD B2C используется [делегированное управление ресурсами.](../lighthouse/concepts/azure-delegated-resource-management.md)

Вы разрешаете пользователю или группе в каталоге Azure AD B2C **(поставщик услуг)** настроить экземпляр Azure Monitor в пределах клиента, содержащего подписку Azure **(Клиент).** Чтобы создать авторизацию, развертывайте шаблон [управления ресурсами Azure](../azure-resource-manager/index.yml) в наниматель Azure AD, содержащий подписку. Следующие разделы провести вас через этот процесс.

## <a name="create-or-choose-resource-group"></a>Создание или выбор группы ресурсов

Это группа ресурсов, содержащая учетную запись хранения данных назначения Azure, концентратор событий или рабочее пространство log Analytics для получения данных из Azure Monitor. При развертывании шаблона управления ресурсами Azure Manager указано имя группы ресурсов.

[Создайте группу ресурсов](../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups) или выберите существующую в нанимателеи Active Directory (Azure AD), которая содержит подписку Azure, а *не* каталог, содержащий ваш клиент Azure AD B2C.

В этом примере используется группа ресурсов под названием *azure-ad-b2c-монитор* в центральном регионе *США.*

## <a name="delegate-resource-management"></a>Управление ресурсами делегата

Далее соберите следующую информацию:

**Идентификатор каталога** Azure AD B2C (также известный как идентификатор клиента).

1. Войти на [портал Azure](https://portal.azure.com/) в качестве пользователя с ролью *администратора пользователя* (или выше).
1. Выберите **значок каталога и подписки** в панели инструментов портала, а затем выберите каталог, содержащий ваш клиент Azure AD B2C.
1. Выберите **активный каталог Azure,** выберите **Свойства**.
1. Запись **идентификатора каталога**.

**Идентификатор объекта** группы Azure AD B2C или пользователя, который вы хотите *дать* автору разрешения группе ресурсов, созданной ранее в каталоге, содержащей подписку.

Чтобы упростить управление, мы рекомендуем использовать *группы* пользователей Azure AD для каждой роли, позволяя добавлять или удалять отдельных пользователей в группу, а не назначать разрешения непосредственно этому пользователю. В этом пошаговом шаге вы добавляете пользователя.

1. С **помощью Active Directory Azure,** выбранного на портале Azure, выберите **пользователей,** а затем выберите пользователя.
1. Запись **идентификатора объекта**пользователя.

### <a name="create-an-azure-resource-manager-template"></a>Создание шаблона Azure Resource Manager

Чтобы на борту ad-находивщика Azure AD **(заказчика),** создайте [шаблон менеджера ресурсов Azure](../lighthouse/how-to/onboard-customer.md) для вашего предложения со следующей информацией. Значения `mspOfferName` `mspOfferDescription` и значения видны при просмотре информации о [поставщиках услуг](../lighthouse/how-to/view-manage-service-providers.md) на странице портала Azure.

| Поле   | Определение |
|---------|------------|
| `mspOfferName`                     | Имя, описывающее это определение. Например, *управляемые службы Azure AD B2C.* Это значение отображается для клиента в виде заголовка предложения. |
| `mspOfferDescription`              | Краткое описание вашего предложения. Например, *включает монитор Azure в Azure AD B2C.*|
| `rgName`                           | Имя группы ресурсов, создаваемых ранее в вашем атакжеме Azure AD. Например, *azure-ad-b2c-монитор*. |
| `managedByTenantId`                | **Идентификатор каталога** вашего клиента Azure AD B2C (также известный как идентификатор клиента). |
| `authorizations.value.principalId` | **Идентификатор объекта** группы B2C или пользователя, который будет иметь доступ к ресурсам в этой подписке Azure. Для этого пошагового запроса укажите идентификатор объекта пользователя, записанный ранее. |

Загрузите шаблон и файлы параметров менеджера ресурсов Azure:

- [rgDelegatedResourceManagement.json](https://raw.githubusercontent.com/Azure/Azure-Lighthouse-samples/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.json)
- [rgDelegatedResourceManagement.parameters.json](https://raw.githubusercontent.com/Azure/Azure-Lighthouse-samples/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.parameters.json)

Затем обновите файл параметров с значениями, записанными ранее. На следующем фрагменте JSON показан пример файла параметров параметров шаблона ресурсов Azure Manager. Для `authorizations.value.roleDefinitionId`использования [встроенного значения роли](../role-based-access-control/built-in-roles.md) для `b24988ac-6180-42a0-ab88-20f7382dd24c`роли *вкладчика*.

```JSON
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "mspOfferName": {
            "value": "Azure AD B2C Managed Services"
        },
        "mspOfferDescription": {
            "value": "Enables Azure Monitor in Azure AD B2C"
        },
        "rgName": {
            "value": "azure-ad-b2c-monitor"
        },
        "managedByTenantId": {
            "value": "<Replace with DIRECTORY ID of Azure AD B2C tenant (tenant ID)>"
        },
        "authorizations": {
            "value": [
                {
                    "principalId": "<Replace with user's OBJECT ID>",
                    "principalIdDisplayName": "Azure AD B2C tenant administrator",
                    "roleDefinitionId": "b24988ac-6180-42a0-ab88-20f7382dd24c"
                }
            ]
        }
    }
}
```

### <a name="deploy-the-azure-resource-manager-templates"></a>Развертывание шаблонов Azure Resource Manager

После обновления файла параметров разместите шаблон управления ресурсами Azure в наниматель Azure в качестве развертывания на уровне подписки. Так как это развертывание уровня подписки, его невозможно инициировать на портале Azure. Развертывание можно с помощью модуля Azure PowerShell или Azure CLI. Ниже показан метод Azure PowerShell.

Войти в каталог, содержащий подписку, с помощью [Connect-AzAccount](/powershell/azure/authenticate-azureps). Используйте `-tenant` флаг, чтобы заставить аутентификацию к правильному каталогу.

```PowerShell
Connect-AzAccount -tenant contoso.onmicrosoft.com
```

Воспользуйтесь cmdlet [Get-AzSubscription,](/powershell/module/az.accounts/get-azsubscription) чтобы перечислить подписки, к которым может получить доступ к текущей учетной записи под арендатором Azure AD. Запишите идентификатор подписки, который вы хотите проецировать на клиента Azure AD B2C.

```PowerShell
Get-AzSubscription
```

Затем переключитесь на подписку, проецируемую на клиента Azure AD B2C:

``` PowerShell
Select-AzSubscription <subscription ID>
```

Наконец, развернуть шаблон и параметры Azure Resource Manager и файлы параметров, которые вы загрузили и обновили ранее. Заменить `Location` `TemplateFile`, `TemplateParameterFile` и значения соответственно.

```PowerShell
New-AzDeployment -Name "AzureADB2C" `
                 -Location "centralus" `
                 -TemplateFile "C:\Users\azureuser\Documents\rgDelegatedResourceManagement.json" `
                 -TemplateParameterFile "C:\Users\azureuser\Documents\rgDelegatedResourceManagement.parameters.json" `
                 -Verbose
```

Успешное развертывание шаблона производит выход, аналогичный следующему (выход усечен для краткости):

```Console
PS /usr/csuser/clouddrive> New-AzDeployment -Name "AzureADB2C" `
>>                  -Location "centralus" `
>>                  -TemplateFile "rgDelegatedResourceManagement.json" `
>>                  -TemplateParameterFile "rgDelegatedResourceManagement.parameters.json" `
>>                  -Verbose
WARNING: Breaking changes in the cmdlet 'New-AzDeployment' :
WARNING:  - The cmdlet 'New-AzSubscriptionDeployment' is replacing this cmdlet.


WARNING: NOTE : Go to https://aka.ms/azps-changewarnings for steps to suppress this breaking change warning, and other information on breaking changes in Azure PowerShell.
VERBOSE: 7:25:14 PM - Template is valid.
VERBOSE: 7:25:15 PM - Create template deployment 'AzureADB2C'
VERBOSE: 7:25:15 PM - Checking deployment status in 5 seconds
VERBOSE: 7:25:42 PM - Resource Microsoft.ManagedServices/registrationDefinitions '44444444-4444-4444-4444-444444444444' provisioning status is succeeded
VERBOSE: 7:25:48 PM - Checking deployment status in 5 seconds
VERBOSE: 7:25:53 PM - Resource Microsoft.Resources/deployments 'rgAssignment' provisioning status is running
VERBOSE: 7:25:53 PM - Checking deployment status in 5 seconds
VERBOSE: 7:25:59 PM - Resource Microsoft.ManagedServices/registrationAssignments '11111111-1111-1111-1111-111111111111' provisioning status is running
VERBOSE: 7:26:17 PM - Checking deployment status in 5 seconds
VERBOSE: 7:26:23 PM - Resource Microsoft.ManagedServices/registrationAssignments '11111111-1111-1111-1111-111111111111' provisioning status is succeeded
VERBOSE: 7:26:23 PM - Checking deployment status in 5 seconds
VERBOSE: 7:26:29 PM - Resource Microsoft.Resources/deployments 'rgAssignment' provisioning status is succeeded

DeploymentName          : AzureADB2C
Location                : centralus
ProvisioningState       : Succeeded
Timestamp               : 1/31/20 7:26:24 PM
Mode                    : Incremental
TemplateLink            :
Parameters              :
                          Name                   Type                       Value
                          =====================  =========================  ==========
                          mspOfferName           String                     Azure AD B2C Managed Services
                          mspOfferDescription    String                     Enables Azure Monitor in Azure AD B2C
...
```

После развертывания шаблона проекция ресурса может занять несколько минут. Возможно, вам придется подождать несколько минут (обычно не более пяти), прежде чем перейти к следующему разделу, чтобы выбрать подписку.

## <a name="select-your-subscription"></a>Выберите свою подписку.

После того, как вы развернули шаблон и подождали несколько минут для завершения проекции ресурса, свяжете подписку с каталогом Azure AD B2C со следующими шагами.

1. **Выйти из** портала Azure, если вы в настоящее время подписаны. Этот и следующий шаг сделандля для обновления учетных данных в сессии портала.
1. Вопийте на [порталaz](https://portal.azure.com) с помощью административной учетной записи Azure AD B2C.
1. Выберите значок **каталога и подписки** в панели инструментов портала.
1. Выберите каталог, содержащий подписку.

    ![Переключить каталог](./media/azure-monitor/azure-monitor-portal-03-select-subscription.png)
1. Убедитесь, что вы выбрали правильный каталог и подписку. В этом примере выбираются все каталоги и подписки.

    ![Все каталоги, выбранные в фильтре подписки & каталога](./media/azure-monitor/azure-monitor-portal-04-subscriptions-selected.png)

## <a name="configure-diagnostic-settings"></a>Настройка параметров диагностики

Диагностические настройки определяют, куда должны отправляться журналы и метрики ресурса. Возможные направления:

- [Учетная запись хранения Azure](../azure-monitor/platform/resource-logs-collect-storage.md)
- [Решения концентратов событий.](../azure-monitor/platform/resource-logs-stream-event-hubs.md)
- [Рабочее пространство аналитики журналов](../azure-monitor/platform/resource-logs-collect-workspace.md)

Если вы еще не сделали этого, создайте экземпляр выбранного типа назначения в группе ресурсов, указанной в [шаблоне управления ресурсами Azure.](#create-an-azure-resource-manager-template)

### <a name="create-diagnostic-settings"></a>Создание диагностических настроек

Вы готовы [создать диагностические настройки](../active-directory/reports-monitoring/overview-monitoring.md) на портале Azure.

Настройка параметров мониторинга для журналов активности Azure AD B2C:

1. Войдите на [портал Azure](https://portal.azure.com/).
1. Выберите **значок каталога и подписки** в панели инструментов портала, а затем выберите каталог, содержащий ваш клиент Azure AD B2C.
1. Выберите **активный каталог Azure**
1. В разделе **Мониторинг** выберите **Параметры диагностики**.
1. При наличии существующих параметров на ресурсе будет увидеть уже настроенный список настроек. Выберите **либо добавить диагностическую настройку,** чтобы добавить новую настройку, либо **отсеивайте** настройки для отсвагиваем существующую. Каждый параметр может иметь не более одного из каждого типа назначения.

    ![Настройки диагностики на портале Azure](./media/azure-monitor/azure-monitor-portal-05-diagnostic-settings-pane-enabled.png)

1. Дайте параметру имя, если оно еще не имеет.
1. Проверьте поле для каждого пункта назначения, чтобы отправить журналы. Выберите **настройку,** чтобы указать их параметры, описанные в следующей таблице.

    | Параметр | Описание |
    |:---|:---|
    | "Архивировать в учетной записи хранения"; | Имя учетной записи хранилища. |
    | "Передать в концентратор событий"; | Пространство имен, в котором создается концентратор событий (если это ваш первый раз потоковое журналы) или потоковое (если уже есть ресурсы, которые потоковой передачи этой категории журнала в это пространство имен).
    | Отправка в Log Analytics | Название рабочего пространства. |

1. Выберите **AuditLogs** и **SignInLogs**.
1. Нажмите кнопку **Сохранить**.

## <a name="next-steps"></a>Дальнейшие действия

Для получения дополнительной информации о добавлении и настройке диагностических параметров в Azure Monitor [см.](../azure-monitor/insights/monitor-azure-resource.md)

Для получения информации о потоке журналов Azure AD в концентратор событий [см.](../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md)
