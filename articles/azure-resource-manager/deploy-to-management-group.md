---
title: Создание ресурсов в группе управления — шаблон Azure Resource Manager
description: Описывает развертывание ресурсов в области группы управления в шаблоне Azure Resource Manager.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 11/07/2019
ms.author: tomfitz
ms.openlocfilehash: 82990ab7baa8ea9f2dfc3c2d2b346e348e6bd490
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73840162"
---
# <a name="create-resources-at-the-management-group-level"></a>Создание ресурсов на уровне группы управления

Как правило, вы развертываете ресурсы Azure в группу ресурсов в подписке Azure. Однако вы также можете создавать ресурсы на уровне группы управления. Развертывания на уровне группы управления используются для выполнения действий, имеющих смысл на этом уровне, таких как назначение [управления доступом на основе ролей](../role-based-access-control/overview.md) или применение [политик](../governance/policy/overview.md).

В настоящее время для развертывания шаблонов на уровне группы управления необходимо использовать REST API.

## <a name="supported-resources"></a>Поддерживаемые ресурсы

На уровне группы управления можно развернуть следующие типы ресурсов:

* [размещения](/azure/templates/microsoft.resources/deployments)
* [полициассигнментс](/azure/templates/microsoft.authorization/policyassignments)
* [policyDefinitions](/azure/templates/microsoft.authorization/policydefinitions)
* [полицисетдефинитионс](/azure/templates/microsoft.authorization/policysetdefinitions)
* [roleAssignments](/azure/templates/microsoft.authorization/roleassignments)
* [roleDefinitions](/azure/templates/microsoft.authorization/roledefinitions)

### <a name="schema"></a>Схема

Схема, используемая для развертываний группы управления, отличается от схемы развертываний группы ресурсов.

Для шаблонов используйте:

```json
https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#
```

Для файлов параметров используйте:

```json
https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentParameters.json#
```

## <a name="deployment-commands"></a>Команды развертывания

Команда для развертываний группы управления отличается от команды для развертываний группы ресурсов.

Для REST API используйте [развертывания — создание в области группы управления](/rest/api/resources/deployments/createorupdateatmanagementgroupscope).

## <a name="deployment-location-and-name"></a>Расположение и имя развертывания

Для развертываний на уровне группы управления необходимо указать расположение для развертывания. Расположение развертывания отделено от расположения развертываемых ресурсов. В расположении развертывания указывается место хранения данных развертывания.

Можно указать имя развертывания или использовать имя развертывания по умолчанию. Имя по умолчанию — это имя файла шаблона. Например, развернув шаблон с именем **azuredeploy.json** создается имя развертывания по умолчанию **azuredeploy**.

Для каждого имени развертывания расположение является неизменяемым. Нельзя создать развертывание в одном месте, если существует развертывание с тем же именем в другом расположении. Если появится код ошибки `InvalidDeploymentLocation`, используйте другое имя или то же расположение, что и для предыдущего развертывания с этим именем.

## <a name="use-template-functions"></a>Использование функций шаблонов

При развертывании группы управления при использовании функций шаблона необходимо учитывать некоторые важные моменты.

* Функция [resourceGroup()](resource-group-template-functions-resource.md#resourcegroup) **не** поддерживается.
* Функция [Subscription ()](resource-group-template-functions-resource.md#subscription) **не** поддерживается.
* Функция [resourceId()](resource-group-template-functions-resource.md#resourceid) поддерживается. Используйте его, чтобы получить идентификатор ресурса для ресурсов, используемых при развертывании на уровне группы управления. Например, получите идентификатор ресурса для определения политики с `resourceId('Microsoft.Authorization/roleDefinitions/', parameters('roleDefinition'))`. Он возвращает идентификатор ресурса в формате `/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}`.
* Функции [reference()](resource-group-template-functions-resource.md#reference) и [list()](resource-group-template-functions-resource.md#list) поддерживаются.

## <a name="create-policies"></a>Создайте политики.

### <a name="define-policy"></a>Определение политики

В следующем примере показано, как [определить](../governance/policy/concepts/definition-structure.md) политику на уровне группы управления.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Authorization/policyDefinitions",
            "name": "locationpolicy",
            "apiVersion": "2018-05-01",
            "properties": {
                "policyType": "Custom",
                "parameters": {},
                "policyRule": {
                    "if": {
                        "field": "location",
                        "equals": "northeurope"
                    },
                    "then": {
                        "effect": "deny"
                    }
                }
            }
        }
    ]
}
```

### <a name="assign-policy"></a>Назначение политики

В следующем примере существующее определение политики назначается группе управления. Предоставьте параметры в качестве объекта, если политика принимает их. Используйте пустой объект по умолчанию, если политика не принимает параметры.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "policyDefinitionID": {
            "type": "string"
        },
        "policyName": {
            "type": "string"
        },
        "policyParameters": {
            "type": "object",
            "defaultValue": {}
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Authorization/policyAssignments",
            "name": "[parameters('policyName')]",
            "apiVersion": "2018-03-01",
            "properties": {
                "policyDefinitionId": "[parameters('policyDefinitionID')]",
                "parameters": "[parameters('policyParameters')]"
            }
        }
    ]
}
```



## <a name="next-steps"></a>Дальнейшие действия

* Дополнительные сведения о назначении ролей см. в статье [Управление доступом к ресурсам Azure с помощью RBAC и шаблонов Azure Resource Manager](../role-based-access-control/role-assignments-template.md).
* Пример развертывания параметров рабочей области для центра безопасности Azure см. в разделе о [deployASCwithWorkspaceSettings.json](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/deployASCwithWorkspaceSettings.json).
* Сведения о создании шаблонов диспетчера ресурсов Azure см. в статье о [создании шаблонов](resource-group-authoring-templates.md). 
* Список доступных в шаблоне функций см. в статье о [функциях шаблонов](resource-group-template-functions.md).