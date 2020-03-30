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
ms.date: 06/03/2019
ms.author: spelluru
ms.openlocfilehash: 29816d158cf1428727b7ff17bcc2c347f402dedf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77134533"
---
# <a name="specify-a-resource-group-for-lab-virtual-machines-in-azure-devtest-labs"></a>Выбор группы ресурсов для виртуальных машин лаборатории в Azure DevTest Labs

В определенной группе ресурсов вы можете настроить виртуальные лаборатории в качестве владельца. Эта функция поможет вам в следующих случаях.

- Нужно меньше групп ресурсов, созданных заданиями в вашей подписке.
- Работайте ли лаборатории в фиксированном наборе групп ресурсов, которые вы настраиваете.
- Нужно обойти необходимые ограничения и одобрения для создания групп ресурсов в вашей подписке Azure.
- Консолидируйте все лабораторные ресурсы в единую группу ресурсов для упрощения отслеживания этих ресурсов и применения [политик](../governance/policy/overview.md) для управления ресурсами на уровне группы ресурсов.

С помощью этой функции можно использовать скрипт для указания новой или существующей группы ресурсов в подписке Azure для всех ваших vMs-материалов лаборатории. В настоящее время Azure DevTest Labs поддерживает эту функцию через API.

> [!NOTE]
> При создании лабораторий в DevTest Labs применяются все ограничения по подписке. Думайте о лаборатории, как любой другой ресурс в вашей подписке. В случае групп ресурсов лимит составляет [980 групп ресурсов на подписку.](../azure-resource-manager/management/azure-subscription-service-limits.md#subscription-limits) 

## <a name="use-azure-portal"></a>Использование портала Azure
Выполните следующие действия, чтобы указать группу ресурсов для всех вс-бай-а, созданных в лаборатории. 

1. Войдите на [портал Azure](https://portal.azure.com).
2. Выберите **все службы** в левом навигационном меню. 
3. Выберите **DevTest Labs** из списка.
4. Из списка лабораторий выберите **лабораторию.**  
5. Выберите **конфигурацию и политики** в разделе **Настройки** в левом меню. 
6. Выберите **настройки лаборатории** в левом меню. 
7. Выберите **все виртуальные машины в одной группе ресурсов.** 
8. Выберите существующую группу ресурсов в списке выпадающих (или) выбрать **Создать новый,** введите **имя** для группы ресурсов и выберите **OK.** 

    ![Выберите группу ресурсов для всех лабораторных VM](./media/resource-group-control/select-resource-group.png)

## <a name="use-powershell"></a>Использование PowerShell 
Ниже приводится следующий пример, как использовать сценарий PowerShell для создания всех лабораторных виртуальных машин в новой группе ресурсов.

```powershell
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

Вызвать сценарий, используя следующую команду. ResourceGroup.ps1 — это файл, содержащий предыдущий скрипт:

```powershell
.\ResourceGroup.ps1 -subId <subscriptionID> -labRg <labRGNAme> -labName <LanName> -vmRg <RGName> 
```

## <a name="use-an-azure-resource-manager-template"></a>Используйте шаблон менеджера ресурсов Azure
Если для создания лаборатории используется шаблон менеджера ресурсов Azure, используйте свойство **vmCreationResourceGroupId** в разделе свойств лабораторных свойств шаблона, как показано в следующем примере:

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


## <a name="api-to-configure-a-resource-group-for-lab-vms"></a>API для настройки группы ресурсов для лабораторных VM
При использовании этого API у вас есть следующие варианты в качестве владельца лаборатории:

- Выберите **группу ресурсов лаборатории** для всех виртуальных машин.
- Выберите **существующую группу ресурсов,** не втомую, чем группа ресурсов лаборатории, для всех виртуальных машин.
- Введите новое название **группы ресурсов** для всех виртуальных машин.
- Продолжайте использовать существующее поведение, в котором создается группа ресурсов для каждого ВМ в лаборатории.
 
Этот параметр применяется к новым виртуальным машинам, созданным в лаборатории. Старые ВМ в вашей лаборатории, созданные в их собственных группах ресурсов, остаются без изменений. Среды, создаваемые в вашей лаборатории, по-прежнему остаются в своих ресурсных группах.

Использование этого API.
- Используйте версию API **2018_10_15_preview**.
- Если вы указываете новую группу ресурсов, убедитесь, что у вас есть **разрешения на написание ресурсов в группе ресурсов** в подписке. Если у вас нет разрешений на запись, создание новых виртуальных машин в указанной группе ресурсов не удастся.
- При использовании API передайте **полный идентификатор группы ресурсов**. Например: `/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroupName>`. Убедитесь, что группа ресурсов находится в той же подписке, что и лаборатория. 


## <a name="next-steps"></a>Дальнейшие действия
См. следующие статьи: 

- [Управление базовыми политиками лаборатории в Azure DevTest Labs](devtest-lab-get-started-with-lab-policies.md)
- [Часто задаваемые вопросы](devtest-lab-faq.md)
