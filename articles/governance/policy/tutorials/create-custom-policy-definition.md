---
title: Создание определения пользовательской политики
description: Создание определения пользовательской политики для Политики Azure с целью соблюдения пользовательских бизнес-правил.
author: DCtheGeek
ms.author: dacoulte
ms.date: 04/23/2019
ms.topic: tutorial
ms.service: azure-policy
ms.openlocfilehash: 240d0fa388fbdfdd3d29d735aed708a096440740
ms.sourcegitcommit: d7689ff43ef1395e61101b718501bab181aca1fa
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/06/2019
ms.locfileid: "71980350"
---
# <a name="tutorial-create-a-custom-policy-definition"></a>Руководство по Создание определения пользовательской политики

Определение пользовательской политики позволяет клиентам устанавливать собственные правила для использования Azure. Эти правила часто применяют для:

- обеспечения безопасности;
- управления затратами;
- соблюдения требований организации (например к именованию или расположению).

Независимо от потребностей в пользовательской политике шаги по ее созданию будут одинаковы для всех.

Прежде чем создавать пользовательскую политику, проверьте, нет ли в [примерах политик](../samples/index.md) готовой политики, соответствующей вашим требованиям.

Процесс создания пользовательской политики включает следующее:

> [!div class="checklist"]
> - определение бизнес-требований;
> - сопоставление каждого требования со свойством ресурса Azure;
> - сопоставление свойства c псевдонимами;
> - определение требуемого эффекта;
> - составление определения политики.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="identify-requirements"></a>Определение требований

Прежде чем создавать определение политики, важно определиться с назначением политики. В этом руководстве мы будем использовать общие требования к безопасности предприятия для иллюстрации необходимых шагов:

- В каждой учетной записи хранения должна быть включена поддержка протокола HTTPS.
- В каждой учетной записи хранения должна быть отключена поддержка протокола HTTP.

Требования должны четко определять состояние ресурса.

Хотя мы определили требуемое состояние, мы пока не выяснили, что делать с ресурсами, которые не соответствуют требованиям. Политика Azure имеет ряд [эффектов](../concepts/effects.md). В этом руководстве наше бизнес-требование будет заключаться в запрете создавать ресурсы, которые не соответствуют бизнес-правилам. Для достижения этой цели мы воспользуемся эффектом [Deny](../concepts/effects.md#deny). Нам также нужна возможность блокирования политики для определенных назначений. Поэтому мы воспользуемся эффектом [Disabled](../concepts/effects.md#disabled) и назначим его [параметром](../concepts/definition-structure.md#parameters) в определении политики.

## <a name="determine-resource-properties"></a>Определение свойств ресурсов

Исходя из нашего бизнес-требования, с помощью Политики Azure будет проводится аудит такого ресурса Azure, как учетная запись хранения. Но нам неизвестно, какие свойства использовать в определении политики. Политика Azure оценивает соответствие по JSON-представлению ресурса, поэтому нам нужно определить свойства, доступные для этого ресурса.

Есть много способов определить свойства ресурса Azure. В этом руководстве мы рассмотрим каждый способ:

- Шаблоны Resource Manager
  - экспорт существующего ресурса;
  - процедура создания;
  - шаблоны быстрого запуска (GitHub);
  - справочники по шаблонам.
- Обозреватель ресурсов Azure

### <a name="resource-manager-templates"></a>Шаблоны Resource Manager

Существует несколько способов просмотреть [шаблон Resource Manager](../../../azure-resource-manager/resource-manager-tutorial-create-encrypted-storage-accounts.md), чтобы узнать свойство, которым мы хотим управлять.

#### <a name="existing-resource-in-the-portal"></a>Существующий ресурс на портале

Простейший способ узнать свойства ресурса — изучить существующий ресурс аналогичного типа. Ресурсы, которые уже настроены на соответствие политике, также подойдут для этой цели.
Откроем страницу **Экспорт шаблона** такого ресурса (раздел **Параметры** на портале Azure).

![Страница "Экспорт шаблона" на существующем ресурсе](../media/create-custom-policy-definition/export-template.png)

Если открыть эту страницу для учетной записи хранения, отобразится примерно такой шаблон:

```json
...
"resources": [{
    "comments": "Generalized from resource: '/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount'.",
    "type": "Microsoft.Storage/storageAccounts",
    "sku": {
        "name": "Standard_LRS",
        "tier": "Standard"
    },
    "kind": "Storage",
    "name": "[parameters('storageAccounts_mystorageaccount_name')]",
    "apiVersion": "2018-07-01",
    "location": "westus",
    "tags": {
        "ms-resource-usage": "azure-cloud-shell"
    },
    "scale": null,
    "properties": {
        "networkAcls": {
            "bypass": "AzureServices",
            "virtualNetworkRules": [],
            "ipRules": [],
            "defaultAction": "Allow"
        },
        "supportsHttpsTrafficOnly": false,
        "encryption": {
            "services": {
                "file": {
                    "enabled": true
                },
                "blob": {
                    "enabled": true
                }
            },
            "keySource": "Microsoft.Storage"
        }
    },
    "dependsOn": []
}]
...
```

В разделе **properties** присутствует параметр **supportsHttpsTrafficOnly**, которому задано значение **false**. Это свойство похоже на то, которое нам требуется. Типу ресурса, **type**, присвоено значение **Microsoft.Storage/storageAccounts**. Тип позволяет ограничить применение политики для ресурсов только этого типа.

#### <a name="create-a-resource-in-the-portal"></a>Создание ресурса на портале

Другой способ — создание ресурса на портале. При создании учетной записи хранения на портале на вкладке **Дополнительно** можно найти параметр **Требуется безопасное перемещение**. Возможные его состояния — _Отключено_ и _Включено_. Значок сведений отображает текст, подтверждающий, что этот параметр — это, скорее всего, то свойство, которое нам требуется. Но на портале нет сведений об имени этого свойства.

На вкладке **Просмотр и создание**  внизу есть ссылка **Скачать шаблон для автоматизации**. Ссылка открывает шаблон для создания ресурса, который мы настроили. В этом случае нас интересуют следующие сведения:

```json
...
"supportsHttpsTrafficOnly": {
    "type": "bool"
}
...
"properties": {
    "accessTier": "[parameters('accessTier')]",
    "supportsHttpsTrafficOnly": "[parameters('supportsHttpsTrafficOnly')]"
}
...
```

Теперь мы знаем тип свойства и то, что нам нужно свойство **supportsHttpsTrafficOnly**.

#### <a name="quickstart-templates-on-github"></a>Шаблоны быстрого запуска на GitHub

В разделе [шаблонов быстрого запуска Azure](https://github.com/Azure/azure-quickstart-templates) на сайте GitHub доступны сотни шаблонов Resource Manager для различных ресурсов. Эти шаблоны обеспечивают отличный способ отыскать требуемое свойство ресурса. Некоторые свойства могут только казаться требуемыми, а в действительности отвечать за что-нибудь другое.

#### <a name="resource-reference-docs"></a>Справочная документация по ресурсам

Чтобы убедиться в том, что **supportsHttpsTrafficOnly** — это требуемое нам свойство, сверьтесь со справочником по шаблонам Resource Manager для [ресурса учетной записи хранения](/azure/templates/microsoft.storage/2018-07-01/storageaccounts) у поставщика хранилища.
Объект свойств содержит список допустимых параметров. Перейдя по ссылке [StorageAccountPropertiesCreateParameters-object](/azure/templates/microsoft.storage/2018-07-01/storageaccounts#storageaccountpropertiescreateparameters-object), можно просмотреть таблицу допустимых свойств. Свойство **supportsHttpsTrafficOnly** там присутствует, а его описание подтверждает, что это то свойство, которое нам требуется для обеспечения соответствия бизнес-требованиям.

### <a name="azure-resource-explorer"></a>Обозреватель ресурсов Azure

Еще одну возможность оценить ресурсы Azure предоставляет [обозреватель ресурсов Azure](https://resources.azure.com) (предварительная версия). Это средство использует контекст подписки, поэтому вам необходимо пройти проверку подлинности на веб-сайте с помощью учетных данных Azure. Пройдя проверку подлинности, можно просмотреть сведения о поставщиках, подписках, группах ресурсов и ресурсах.

Найдите ресурс учетной записи хранения и просмотрите его свойства. Свойство **supportsHttpsTrafficOnly** здесь также присутствует. На вкладке **Документация** видно, что описание свойства соответствует сведениям из справочника, полученным нами ранее.

## <a name="find-the-property-alias"></a>Поиск псевдонима свойства

Мы определили свойство ресурса, но нам нужно сопоставить это свойство с [псевдонимом](../concepts/definition-structure.md#aliases).

Есть несколько способов определить псевдонимы ресурса Azure. В этом руководстве мы рассмотрим каждый из них:

- Инфраструктура CLI Azure
- Azure PowerShell
- Azure Resource Graph

### <a name="azure-cli"></a>Инфраструктура CLI Azure

Для поиска псевдонимов ресурса в Azure CLI используется группа команд `az provider`. Выполним поиск по запросу "пространство имен **Microsoft.Storage**", руководствуясь ранее полученными сведениями об этом ресурсе Azure.

```azurecli-interactive
# Login first with az login if not using Cloud Shell

# Get Azure Policy aliases for type Microsoft.Storage
az provider show --namespace Microsoft.Storage --expand "resourceTypes/aliases" --query "resourceTypes[].aliases[].name"
```

В результатах мы видим псевдоним, поддерживаемый учетными записями хранения, с именем **supportsHttpsTrafficOnly**. Наличие такого псевдонима позволяет нам создать политику для соблюдения бизнес-требований.

### <a name="azure-powershell"></a>Azure PowerShell

Для поиска псевдонимов ресурса в Azure PowerShell используется командлет `Get-AzPolicyAlias`. Выполним поиск по запросу "пространство имен **Microsoft.Storage**", руководствуясь ранее полученными сведениями об этом ресурсе Azure.

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Use Get-AzPolicyAlias to list aliases for Microsoft.Storage
(Get-AzPolicyAlias -NamespaceMatch 'Microsoft.Storage').Aliases
```

Как и в Azure CLI, в результатах отобразится псевдоним, поддерживаемый учетными записями хранения, с именем **supportsHttpsTrafficOnly**.

### <a name="azure-resource-graph"></a>Azure Resource Graph

[Azure Resource Graph](../../resource-graph/overview.md) — это новая служба, доступная в предварительной версии. Она предлагает еще один способ выяснить свойства ресурсов Azure. Вот пример запроса в Resource Graph для просмотра сведений об отдельной учетной записи хранения.

```kusto
where type=~'microsoft.storage/storageaccounts'
| limit 1
```

```azurecli-interactive
az graph query -q "where type=~'microsoft.storage/storageaccounts' | limit 1"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type=~'microsoft.storage/storageaccounts' | limit 1"
```

Результат похож на тот, который был получен нами с использованием шаблонов Resource Manager и обозревателя ресурсов Azure. Однако результаты Azure Resource Graph могут включать сведения о [псевдониме](../concepts/definition-structure.md#aliases), _проецируя_ массив _псевдонимов_:

```kusto
where type=~'microsoft.storage/storageaccounts'
| limit 1
| project aliases
```

```azurecli-interactive
az graph query -q "where type=~'microsoft.storage/storageaccounts' | limit 1 | project aliases"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type=~'microsoft.storage/storageaccounts' | limit 1 | project aliases"
```

Вот пример выходных данных от учетной записи хранения для псевдонимов.

```json
"aliases": {
    "Microsoft.Storage/storageAccounts/accessTier": null,
    "Microsoft.Storage/storageAccounts/accountType": "Standard_LRS",
    "Microsoft.Storage/storageAccounts/enableBlobEncryption": true,
    "Microsoft.Storage/storageAccounts/enableFileEncryption": true,
    "Microsoft.Storage/storageAccounts/encryption": {
        "keySource": "Microsoft.Storage",
        "services": {
            "blob": {
                "enabled": true,
                "lastEnabledTime": "2018-06-04T17:59:14.4970000Z"
            },
            "file": {
                "enabled": true,
                "lastEnabledTime": "2018-06-04T17:59:14.4970000Z"
            }
        }
    },
    "Microsoft.Storage/storageAccounts/encryption.keySource": "Microsoft.Storage",
    "Microsoft.Storage/storageAccounts/encryption.keyvaultproperties.keyname": null,
    "Microsoft.Storage/storageAccounts/encryption.keyvaultproperties.keyvaulturi": null,
    "Microsoft.Storage/storageAccounts/encryption.keyvaultproperties.keyversion": null,
    "Microsoft.Storage/storageAccounts/encryption.services": {
        "blob": {
            "enabled": true,
            "lastEnabledTime": "2018-06-04T17:59:14.4970000Z"
        },
        "file": {
            "enabled": true,
            "lastEnabledTime": "2018-06-04T17:59:14.4970000Z"
        }
    },
    "Microsoft.Storage/storageAccounts/encryption.services.blob": {
        "enabled": true,
        "lastEnabledTime": "2018-06-04T17:59:14.4970000Z"
    },
    "Microsoft.Storage/storageAccounts/encryption.services.blob.enabled": true,
    "Microsoft.Storage/storageAccounts/encryption.services.file": {
        "enabled": true,
        "lastEnabledTime": "2018-06-04T17:59:14.4970000Z"
    },
    "Microsoft.Storage/storageAccounts/encryption.services.file.enabled": true,
    "Microsoft.Storage/storageAccounts/networkAcls": {
        "bypass": "AzureServices",
        "defaultAction": "Allow",
        "ipRules": [],
        "virtualNetworkRules": []
    },
    "Microsoft.Storage/storageAccounts/networkAcls.bypass": "AzureServices",
    "Microsoft.Storage/storageAccounts/networkAcls.defaultAction": "Allow",
    "Microsoft.Storage/storageAccounts/networkAcls.ipRules": [],
    "Microsoft.Storage/storageAccounts/networkAcls.ipRules[*]": [],
    "Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].action": [],
    "Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].value": [],
    "Microsoft.Storage/storageAccounts/networkAcls.virtualNetworkRules": [],
    "Microsoft.Storage/storageAccounts/networkAcls.virtualNetworkRules[*]": [],
    "Microsoft.Storage/storageAccounts/networkAcls.virtualNetworkRules[*].action": [],
    "Microsoft.Storage/storageAccounts/networkAcls.virtualNetworkRules[*].id": [],
    "Microsoft.Storage/storageAccounts/networkAcls.virtualNetworkRules[*].state": [],
    "Microsoft.Storage/storageAccounts/primaryEndpoints": {
        "blob": "https://mystorageaccount.blob.core.windows.net/",
        "file": "https://mystorageaccount.file.core.windows.net/",
        "queue": "https://mystorageaccount.queue.core.windows.net/",
        "table": "https://mystorageaccount.table.core.windows.net/"
    },
    "Microsoft.Storage/storageAccounts/primaryEndpoints.blob": "https://mystorageaccount.blob.core.windows.net/",
    "Microsoft.Storage/storageAccounts/primaryEndpoints.file": "https://mystorageaccount.file.core.windows.net/",
    "Microsoft.Storage/storageAccounts/primaryEndpoints.queue": "https://mystorageaccount.queue.core.windows.net/",
    "Microsoft.Storage/storageAccounts/primaryEndpoints.table": "https://mystorageaccount.table.core.windows.net/",
    "Microsoft.Storage/storageAccounts/primaryEndpoints.web": null,
    "Microsoft.Storage/storageAccounts/primaryLocation": "eastus2",
    "Microsoft.Storage/storageAccounts/provisioningState": "Succeeded",
    "Microsoft.Storage/storageAccounts/sku.name": "Standard_LRS",
    "Microsoft.Storage/storageAccounts/sku.tier": "Standard",
    "Microsoft.Storage/storageAccounts/statusOfPrimary": "available",
    "Microsoft.Storage/storageAccounts/supportsHttpsTrafficOnly": false
}
```

Azure Resource Graph (предварительная версия) можно использовать в [Cloud Shell](https://shell.azure.com), чтобы быстро и удобно просматривать свойства ресурсов.

## <a name="determine-the-effect-to-use"></a>Определение требуемого эффекта

Решение о том, что делать с ресурсами, которые не отвечают требованиям, не менее важно, чем решение о том, что оценивать в первую очередь. Каждый возможный ответ такому ресурсу, называется [эффектом](../concepts/effects.md).
Эффект отвечает за регистрацию ресурсов, которые не отвечают требованиям, их блокировку, добавление в них данных и наличие у них зависимых развертываний, а также приведение их в состояние соответствия требованиям.

В нашем примере мы используем эффект Deny, чтобы запретить создание в среде Azure ресурсов, не отвечающих требованиям. Audit — это оптимальный первоначальный вариант, позволяющий определить последствия применения политики до применения эффекта Deny. Один из способов упростить изменение эффекта — задать ему параметры. Дополнительные сведения об этом см. в разделе [Параметры](#parameters) ниже.

## <a name="compose-the-definition"></a>Создание определения

Теперь у нас есть подробные сведения о свойстве, которым мы хотим управлять, включая его псевдоним. В заключение мы создадим само правило политики. Если вы не знакомы с языком политики, изучите [структуру определения политики](../concepts/definition-structure.md), чтобы понять, как структурировать определение политики. Вот пустой шаблон, иллюстрирующий определение политики.

```json
{
    "properties": {
        "displayName": "<displayName>",
        "description": "<description>",
        "mode": "<mode>",
        "parameters": {
                <parameters>
        },
        "policyRule": {
            "if": {
                <rule>
            },
            "then": {
                "effect": "<effect>"
            }
        }
    }
}
```

### <a name="metadata"></a>Метаданные

Первые три компонента — это метаданные политики. Этим компонентам мы можем быстро задать значения, так как знаем, для чего создаем правило. Параметр [mode](../concepts/definition-structure.md#mode) касается главным образом тегов и расположения ресурсов. Так как нам не нужно ограничивать оценку только ресурсами, поддерживающими теги, зададим параметру **mode** значение _all_.

```json
"displayName": "Deny storage accounts not using only HTTPS",
"description": "Deny storage accounts not using only HTTPS. Checks the supportsHttpsTrafficOnly property on StorageAccounts.",
"mode": "all",
```

### <a name="parameters"></a>Параметры

Хотя мы не использовали параметр для изменения оценки, нам нужен параметр, допускающий изменение **эффекта** для устранения неполадок. Зададим параметр **effectType** и ограничим его значениями **Deny** и **Disabled**. Эти два варианта обеспечивают соответствие нашим бизнес-требованиям. Готовый блок параметров выглядит следующим образом:

```json
"parameters": {
    "effectType": {
        "type": "string",
        "defaultValue": "Deny",
        "allowedValues": [
            "Deny",
            "Disabled"
        ],
        "metadata": {
            "displayName": "Effect",
            "description": "Enable or disable the execution of the policy"
        }
    }
},
```

### <a name="policy-rule"></a>Правило политики

Составление [правила политики](../concepts/definition-structure.md#policy-rule) является последним шагом в создании определения пользовательской политики. Мы установили два критерия проверки:

- Тип ресурса, **type** —**Microsoft.Storage/storageAccounts**.
- Параметру **supportsHttpsTrafficOnly** учетной записи хранения не задано значение **true**.

Так как оба критерия должны удовлетворяться, воспользуемся [логическим оператором](../concepts/definition-structure.md#logical-operators) **allOf**. Параметр **effectType** мы передадим эффекту, чтобы не делать статичное объявление. Готовое правило имеет следующий вид:

```json
"if": {
    "allOf": [
        {
            "field": "type",
            "equals": "Microsoft.Storage/storageAccounts"
        },
        {
            "field": "Microsoft.Storage/storageAccounts/supportsHttpsTrafficOnly",
            "notEquals": "true"
        }
    ]
},
"then": {
    "effect": "[parameters('effectType')]"
}
```

### <a name="completed-definition"></a>Завершенное определение

Определив все три части политики, мы получили такое завершенное определение:

```json
{
    "properties": {
        "displayName": "Deny storage accounts not using only HTTPS",
        "description": "Deny storage accounts not using only HTTPS. Checks the supportsHttpsTrafficOnly property on StorageAccounts.",
        "mode": "all",
        "parameters": {
            "effectType": {
                "type": "string",
                "defaultValue": "Deny",
                "allowedValues": [
                    "Deny",
                    "Disabled"
                ],
                "metadata": {
                    "displayName": "Effect",
                    "description": "Enable or disable the execution of the policy"
                }
            }
        },
        "policyRule": {
            "if": {
                "allOf": [
                    {
                        "field": "type",
                        "equals": "Microsoft.Storage/storageAccounts"
                    },
                    {
                        "field": "Microsoft.Storage/storageAccounts/supportsHttpsTrafficOnly",
                        "notEquals": "true"
                    }
                ]
            },
            "then": {
                "effect": "[parameters('effectType')]"
            }
        }
    }
}
```

Завершенное определение можно использовать для создания новой политики. Портал Azure и все пакеты SDK (для Azure CLI, Azure PowerShell и REST API) по-разному принимают определения, поэтому просмотрите команды для каждого средства, чтобы правильно использовать определения. Затем, используя эффект с заданными параметрами, назначьте определение требуемым ресурсам для управления безопасностью своих учетных записей хранения.

## <a name="review"></a>Проверка

В этом руководстве вы успешно выполнили следующие действия:

> [!div class="checklist"]
> - определили бизнес-требования;
> - сопоставили каждое требование со свойством ресурса Azure;
> - сопоставили свойства c псевдонимами;
> - определили требуемый эффект;
> - составили определение политики.

## <a name="next-steps"></a>Дополнительная информация

Используйте определение пользовательской политики для создания и назначения политики, как описано в следующей статье:

> [!div class="nextstepaction"]
> [Create and assign a policy definition](../how-to/programmatically-create.md#create-and-assign-a-policy-definition) (Создание и назначение определения политики).