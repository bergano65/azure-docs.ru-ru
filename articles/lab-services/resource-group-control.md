---
title: Выбор группы ресурсов для виртуальных машин в Azure DevTest Labs | Документация Майкрософт
description: Узнайте, как указать группу ресурсов для виртуальных машин в лаборатории Azure DevTest Labs.
services: devtest-lab, lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/05/2019
ms.author: spelluru
ms.openlocfilehash: ddda9ef2b9bb716f7cdd33aa8fe9233f6c7d8e82
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/06/2019
ms.locfileid: "55749006"
---
# <a name="specify-a-resource-group-for-lab-virtual-machines-in-azure-devtest-labs"></a>Выбор группы ресурсов для виртуальных машин лаборатории в Azure DevTest Labs
В определенной группе ресурсов вы можете настроить виртуальные лаборатории в качестве владельца. Используйте эту функцию, чтобы не превышать ограничения группы ресурсов в подписке Azure. Эта функция также позволяет консолидировать все ваши ресурсы лаборатории в рамках одной группы ресурсов. Это также упрощает отслеживание этих ресурсов и применение [политик](../governance/policy/overview.md), чтобы управлять ими на уровне группы ресурсов.

С помощью этой функции вы можете использовать сценарий, чтобы указать новую или существующую группу ресурсов для всех ваших виртуальных машин лаборатории в подписке Azure. Сейчас DevTest Labs поддерживает эту функцию с помощью API. 

## <a name="api-to-configure-a-resource-group-for-labs-vms"></a>Использование API для настройки группы ресурсов в виртуальных машинах лаборатории
Теперь при использовании этого API вы, как владелец лаборатории, можете выполнять следующие действия: 

- выбирать **группы ресурсов лаборатории** для всех виртуальных машин;
- выбирать **существующую группу ресурсов**, которая отличается от группы ресурсов лаборатории для всех виртуальных машин;
- вводить имя **новой группы ресурсов** для всех виртуальных машин;
- продолжать с существующей реакцией на событие, то есть группа ресурсов будет создаваться для каждой виртуальной машины в лаборатории.
 
Этот параметр применяется к новым виртуальным машинам, созданным в лаборатории. Виртуальные машины лаборатории, созданные ранее в их собственных группах ресурсов, остаются неизменными. Тем не менее вы можете перенести эти виртуальные машины из отдельных групп ресурсов в общую группу ресурсов, чтобы все они находились в одном месте. Дополнительные сведения см. в статье [Перемещение ресурсов в новую группу ресурсов или подписку](../azure-resource-manager/resource-group-move-resources.md). Среды, созданные в лаборатории, по-прежнему остаются в собственной группе ресурсов.

### <a name="how-to-use-this-api"></a>Использование этого API.
- Используйте API версии **2018_10_15_preview**. 
- Если вы настраиваете новую группу ресурсов, убедитесь, что у вас есть **разрешения на запись для групп ресурсов** в подписке. Если этих разрешений нет, создание новых виртуальных машин в указанной группе ресурсов приведет к сбою. 
- При использовании API передайте **полный идентификатор группы ресурсов**. Например, `/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroupName>`. Убедитесь, что группа ресурсов находится в той же подписке, что и лаборатория. 

## <a name="use-powershell"></a>Использование PowerShell 
Следующий пример описывает создание всех виртуальных машин лаборатории в новой группе ресурсов с помощью сценария PowerShell.

```PowerShell
[CmdletBinding()]
Param(
    $subId,
    $labRg,
    $labName,
    $vmRg
)

az login | out-null

az account set --subscription $subId | out-null

$rgId = "/subscriptions/"+$subId+"/resourceGroups/"+$vmRg

"Updating lab '$labName' with vm rg '$rgId'..."

az resource update -g $labRg -n $labName --resource-type "Microsoft.DevTestLab/labs" --api-version 2018-10-15-preview --set properties.vmCreationResourceGroupId=$rgId

"Done. New virtual machines will now be created in the resource group '$vmRg'."
```

Вызовите сценарий, используя следующую команду (ResourceGroup.ps1 — это файл, который содержит указанный выше сценарий). 

```PowerShell
.\ResourceGroup.ps1 -subId <subscriptionID> -labRg <labRGNAme> -labName <LanName> -vmRg <RGName> 
```

## <a name="use-azure-resource-manager-template"></a>Использование шаблона Azure Resource Manager
Если вы используете шаблон Azure Resource Manager для создания лаборатории, используйте свойство **vmCreationResourceGroupId** в разделе свойств лаборатории вашего шаблона Resource Manager, как показано в следующем примере.

```json
        {
            "type": "microsoft.devtestlab/labs",
            "name": "[parameters('lab_name')]",
            "apiVersion": "2018_10_15_preview",
            "location": "eastus",
            "tags": {},
            "scale": null,
            "properties": {
                "vmCreationResourceGroupId": "/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>",
                "labStorageType": "Premium",
                "premiumDataDisks": "Disabled",
                "provisioningState": "Succeeded",
                "uniqueIdentifier": "6e6f668f-992b-435c-bac3-d328b745cd25"
            },
            "dependsOn": []
        },
```


## <a name="next-steps"></a>Дополнительная информация
Ознакомьтесь со следующими статьями: 

- [Управление базовыми политиками лаборатории в Azure DevTest Labs](devtest-lab-get-started-with-lab-policies.md)
- [Часто задаваемые вопросы](devtest-lab-faq.md)
