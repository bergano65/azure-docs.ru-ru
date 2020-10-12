---
title: Мониторинг Azure AD B2C с помощью Azure Monitor
titleSuffix: Azure AD B2C
description: Узнайте, как вести журнал Azure AD B2C событий с Azure Monitor с помощью управления делегированными ресурсами.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.author: mimart
ms.subservice: B2C
ms.date: 02/10/2020
ms.openlocfilehash: 3106e5a640ed66828558078e6986979ad7195450
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "85386221"
---
# <a name="monitor-azure-ad-b2c-with-azure-monitor"></a>Мониторинг Azure AD B2C с помощью Azure Monitor

Используйте Azure Monitor для маршрутизации Azure Active Directory B2C (Azure AD B2C) для входа и [аудита](view-audit-logs.md) в различные решения мониторинга. Вы можете хранить журналы для долгосрочного использования или интегрировать их со сторонними средствами управления сведениями о безопасности и событиями (SIEM) для получения ценных сведений о среде.

События журнала можно направить в:

* [Учетная запись хранения](../storage/blobs/storage-blobs-introduction.md)Azure.
* [Рабочая область log Analytics](../azure-monitor/platform/resource-logs-collect-workspace.md) (для анализа данных, создания панелей мониторинга и оповещения о конкретных событиях).
* [Концентратор событий](../event-hubs/event-hubs-about.md) Azure (и интегрируется с экземплярами логики Splunk и Sumo).

![Azure Monitor](./media/azure-monitor/azure-monitor-flow.png)

## <a name="prerequisites"></a>Предварительные требования

Чтобы выполнить действия, описанные в этой статье, необходимо развернуть шаблон Azure Resource Manager с помощью модуля Azure PowerShell.

* [Модуль Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps) версии 6.13.1 или выше

Можно также использовать [Azure Cloud Shell](https://shell.azure.com), который включает последнюю версию модуля Azure PowerShell.

## <a name="delegated-resource-management"></a>Управление делегированными ресурсами

Azure AD B2C использует [мониторинг Azure Active Directory](../active-directory/reports-monitoring/overview-monitoring.md). Чтобы включить *параметры диагностики* в Azure Active Directory в клиенте Azure AD B2C, используйте [Управление делегированными ресурсами](../lighthouse/concepts/azure-delegated-resource-management.md).

Вы уполномочены пользователю или группе в каталоге Azure AD B2C ( **поставщик услуг**) настроить экземпляр Azure Monitor в клиенте, который содержит подписку Azure ( **клиент**). Чтобы создать авторизацию, необходимо развернуть шаблон [Azure Resource Manager](../azure-resource-manager/index.yml) в клиенте Azure AD, который содержит подписку. В следующих разделах описывается процесс.

## <a name="create-or-choose-resource-group"></a>Создание или выбор группы ресурсов

Это группа ресурсов, содержащая целевую учетную запись хранения Azure, концентратор событий или рабочую область Log Analytics для получения данных от Azure Monitor. Имя группы ресурсов указывается при развертывании шаблона Azure Resource Manager.

[Создайте группу ресурсов](../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups) или выберите существующую в клиенте Azure Active Directory (Azure AD), который содержит подписку Azure, а *не* в каталоге, содержащем клиент Azure AD B2C.

В этом примере используется группа ресурсов с именем *Azure-AD-B2C-Monitor* в *центральном регионе США* .

## <a name="delegate-resource-management"></a>Делегирование управления ресурсами

Затем соберите следующие сведения:

**Идентификатор** каталога Azure AD B2C каталога (также известного как идентификатор клиента).

1. Войдите в [портал Azure](https://portal.azure.com/) в качестве пользователя с ролью *администратора пользователей* (или выше).
1. Выберите значок **Каталог и подписка** в верхней панели инструментов портала, а затем выберите каталог, содержащий клиент Azure AD B2C.
1. Выберите **Azure Active Directory**, а затем — **свойства**.
1. Запишите **идентификатор каталога**.

**Идентификатор объекта** Azure AD B2C группы или пользователя, которым вы хотите предоставить разрешение *участнику* для группы ресурсов, созданной ранее в каталоге, содержащем подписку.

Чтобы упростить управление, мы рекомендуем использовать *группы* пользователей Azure AD для каждой роли, позволяя добавлять или удалять отдельных пользователей в группе, а не назначать разрешения непосредственно этому пользователю. В этом пошаговом руководстве вы добавите пользователя.

1. Выбрав **Azure Active Directory** все еще выбрано в портал Azure, выберите **Пользователи**, а затем выберите пользователя.
1. Запишите **идентификатор объекта**пользователя.

### <a name="create-an-azure-resource-manager-template"></a>Создание шаблона Azure Resource Manager

Чтобы подключить клиент Azure AD ( **клиент**), создайте [шаблон Azure Resource Manager](../lighthouse/how-to/onboard-customer.md) для вашего предложения со следующими сведениями. `mspOfferName`Значения и `mspOfferDescription` отображаются при просмотре сведений о предложении на [странице поставщики услуг](../lighthouse/how-to/view-manage-service-providers.md) портал Azure.

| Поле   | Определение |
|---------|------------|
| `mspOfferName`                     | Имя, описывающее это определение. Например, *Azure AD B2C управляемые службы*. Это значение отображается для клиента в виде заголовка предложения. |
| `mspOfferDescription`              | Краткое описание предложения. Например, *включает Azure Monitor в Azure AD B2C*.|
| `rgName`                           | Имя группы ресурсов, созданной ранее в клиенте Azure AD. Например, *Azure-AD-B2C-Monitor*. |
| `managedByTenantId`                | **Идентификатор каталога** клиента Azure AD B2C (также известный как идентификатор клиента). |
| `authorizations.value.principalId` | **Идентификатор объекта** группы B2C или пользователя, который будет иметь доступ к ресурсам в этой подписке Azure. В этом пошаговом руководстве укажите идентификатор объекта пользователя, записанный ранее. |

Скачайте шаблон Azure Resource Manager и файлы параметров:

- [rgDelegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.json)
- [rgDelegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.parameters.json)

Затем обновите файл параметров, указав значения, записанные ранее. В следующем фрагменте кода JSON показан пример файла параметров шаблона Azure Resource Manager. Для `authorizations.value.roleDefinitionId` Используйте [встроенное значение роли](../role-based-access-control/built-in-roles.md) для *роли участника* `b24988ac-6180-42a0-ab88-20f7382dd24c` .

```json
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

После обновления файла параметров разверните шаблон Azure Resource Manager в клиенте Azure в качестве развертывания на уровне подписки. Так как это развертывание уровня подписки, его невозможно инициировать на портале Azure. Развертывание можно выполнить с помощью модуля Azure PowerShell или Azure CLI. Ниже показан метод Azure PowerShell.

Войдите в каталог, содержащий подписку, с помощью [Connect-азаккаунт](/powershell/azure/authenticate-azureps). Используйте `-tenant` флаг для принудительной проверки подлинности в правильном каталоге.

```PowerShell
Connect-AzAccount -tenant contoso.onmicrosoft.com
```

Используйте командлет [Get-азсубскриптион](/powershell/module/az.accounts/get-azsubscription) , чтобы получить список подписок, к которым текущая учетная запись может получить доступ в клиенте Azure AD. Запишите идентификатор подписки, которую вы хотите проецировать в клиент Azure AD B2C.

```PowerShell
Get-AzSubscription
```

Затем переключитесь на подписку, которую вы хотите проецировать, в клиент Azure AD B2C:

``` PowerShell
Select-AzSubscription <subscription ID>
```

Наконец, разверните файл шаблона Azure Resource Manager и файлы параметров, которые вы скачали и обновили ранее. Замените `Location` значения, `TemplateFile` и `TemplateParameterFile` соответствующим образом.

```PowerShell
New-AzDeployment -Name "AzureADB2C" `
                 -Location "centralus" `
                 -TemplateFile "C:\Users\azureuser\Documents\rgDelegatedResourceManagement.json" `
                 -TemplateParameterFile "C:\Users\azureuser\Documents\rgDelegatedResourceManagement.parameters.json" `
                 -Verbose
```

При успешном развертывании шаблона создаются выходные данные, аналогичные приведенным ниже (для краткости результат сокращен):

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

После развертывания шаблона выполнение проекции ресурсов может занять несколько минут. Возможно, потребуется подождать несколько минут (обычно не более пяти), прежде чем переходить к следующему разделу, чтобы выбрать подписку.

## <a name="select-your-subscription"></a>Выберите свою подписку.

После развертывания шаблона и ожидания завершения проекции ресурсов через несколько минут свяжите подписку с Azure AD B2C каталогом, выполнив следующие действия.

1. Выйдите из портал Azure, если вы вошли в **систему** . На этом и следующем шаге выполняется обновление учетных данных в сеансе портала.
1. Войдите в [портал Azure](https://portal.azure.com) с помощью учетной записи администратора Azure AD B2C.
1. На панели инструментов портала выберите значок **Каталог + подписка**.
1. Выберите каталог, который содержит нужную подписку.

    ![Переключить каталог](./media/azure-monitor/azure-monitor-portal-03-select-subscription.png)
1. Убедитесь, что выбран правильный каталог и подписка. В этом примере выбраны все каталоги и подписки.

    ![Все каталоги, выбранные в & фильтра подписки на каталог](./media/azure-monitor/azure-monitor-portal-04-subscriptions-selected.png)

## <a name="configure-diagnostic-settings"></a>Настройка параметров диагностики

Параметры диагностики определяют, где должны отправляться журналы и метрики для ресурса. Возможные места назначения

- [Учетная запись хранения Azure](../azure-monitor/platform/resource-logs-collect-storage.md)
- Решения для [концентраторов событий](../azure-monitor/platform/resource-logs-stream-event-hubs.md) .
- [Рабочая область Log Analytics](../azure-monitor/platform/resource-logs-collect-workspace.md)

Если вы этого еще не сделали, создайте экземпляр выбранного типа назначения в группе ресурсов, указанной в [шаблоне Azure Resource Manager](#create-an-azure-resource-manager-template).

### <a name="create-diagnostic-settings"></a>Создание параметров диагностики для отправки журналов платформы и метрик в разные целевые объекты

Вы можете [создавать параметры диагностики](../active-directory/reports-monitoring/overview-monitoring.md) в портал Azure.

Чтобы настроить параметры мониторинга для журналов действий Azure AD B2C:

1. Войдите на [портал Azure](https://portal.azure.com/).
1. Выберите значок **Каталог и подписка** в верхней панели инструментов портала, а затем выберите каталог, содержащий клиент Azure AD B2C.
1. Выберите **Azure Active Directory**
1. В разделе **Мониторинг** выберите **Параметры диагностики**.
1. Если в ресурсе есть параметры, вы увидите список уже настроенных параметров. Либо выберите **Добавить параметр диагностики** , чтобы добавить новый параметр, либо **измените** параметр, чтобы изменить существующий. Каждый параметр может иметь не более одного из целевых типов.

    ![Панель "параметры диагностики" в портал Azure](./media/azure-monitor/azure-monitor-portal-05-diagnostic-settings-pane-enabled.png)

1. Присвойте параметру имя, если его еще нет.
1. Установите флажок для каждого назначения, чтобы отправить журналы. Выберите **настроить** , чтобы указать их параметры, как описано в следующей таблице.

    | Параметр | Описание |
    |:---|:---|
    | "Архивировать в учетной записи хранения"; | Имя учетной записи хранения. |
    | "Передать в концентратор событий"; | Пространство имен, в котором создается концентратор событий (если это первый журнал потоковой передачи) или потоковая передача (если уже есть ресурсы, которые используют потоковую передачу категории журнала в это пространство имен).
    | Отправка в Log Analytics | Имя рабочей области. |

1. Выберите **AuditLogs** и **сигнинлогс**.
1. Щелкните **Сохранить**.

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о добавлении и настройке параметров диагностики в Azure Monitor см. в разделе [учебник. сбор и анализ журналов ресурсов из ресурса Azure](../azure-monitor/insights/monitor-azure-resource.md).

Сведения о потоковой передаче журналов Azure AD в концентратор событий см. в статье [учебник. потоковая Azure Active Directory журналов в концентратор событий Azure](../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md).
