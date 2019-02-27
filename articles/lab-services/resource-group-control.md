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
ms.date: 02/15/2019
ms.author: spelluru
ms.openlocfilehash: 94e5f5b29e93409df2373cf6c56e8185dc5373a2
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/15/2019
ms.locfileid: "56312980"
---
# <a name="specify-a-resource-group-for-lab-virtual-machines-in-azure-devtest-labs"></a>Выбор группы ресурсов для виртуальных машин лаборатории в Azure DevTest Labs
В определенной группе ресурсов вы можете настроить виртуальные лаборатории в качестве владельца. Эта функция поможет вам в следующих случаях. 

- Нужно меньше групп ресурсов, созданных заданиями в вашей подписке.
- Нужно, чтобы ваши задания работали только с фиксированным, настроенным вами, набором групп ресурсов.
- Нужно обойти необходимые ограничения и одобрения для создания групп ресурсов в вашей подписке Azure.
- Объедините все ваши ресурсы задания в одну группу ресурсов для упрощения отслеживания этих ресурсов и применения [политик](../governance/policy/overview.md), чтобы управлять ими на уровне группы ресурсов.

С помощью этой функции вы можете использовать сценарий, чтобы указать новую или существующую группу ресурсов для всех ваших виртуальных машин лаборатории в подписке Azure. Сейчас DevTest Labs поддерживает эту функцию с помощью API. 

## <a name="api-to-configure-a-resource-group-for-lab-virtual-machines"></a>Использование API для настройки группы ресурсов в виртуальных машинах лаборатории
Теперь при использовании этого API вы, как владелец лаборатории, можете выполнять следующие действия: 

- выбирать **группы ресурсов лаборатории** для всех виртуальных машин;
- выбирать **существующую группу ресурсов**, которая отличается от группы ресурсов лаборатории для всех виртуальных машин;
- вводить имя **новой группы ресурсов** для всех виртуальных машин;
- продолжать с существующей реакцией на событие, то есть группа ресурсов будет создаваться для каждой виртуальной машины в лаборатории.
 
Этот параметр применяется к новым виртуальным машинам, созданным в лаборатории. Виртуальные машины лаборатории, созданные ранее в их собственных группах ресурсов, остаются неизменными. Среды, созданные в лаборатории, по-прежнему остаются в собственной группе ресурсов.

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
                "uniqueIdentifier": "000000000f-0000-0000-0000-00000000000000"
            },
            "dependsOn": []
        },
```


## <a name="next-steps"></a>Дополнительная информация
Ознакомьтесь со следующими статьями: 

- [Управление базовыми политиками лаборатории в Azure DevTest Labs](devtest-lab-get-started-with-lab-policies.md)
- [Часто задаваемые вопросы](devtest-lab-faq.md)
