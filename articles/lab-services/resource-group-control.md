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
ms.openlocfilehash: 574cc0c41ce645c71302178afcf6e7deaec69d8e
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/04/2019
ms.locfileid: "66476083"
---
# <a name="specify-a-resource-group-for-lab-virtual-machines-in-azure-devtest-labs"></a>Выбор группы ресурсов для виртуальных машин лаборатории в Azure DevTest Labs

В определенной группе ресурсов вы можете настроить виртуальные лаборатории в качестве владельца. Эта функция поможет вам в следующих случаях.

- Нужно меньше групп ресурсов, созданных заданиями в вашей подписке.
- Иметь своей лаборатории, которые работают в фиксированный набор групп ресурсов, которые можно настроить.
- Нужно обойти необходимые ограничения и одобрения для создания групп ресурсов в вашей подписке Azure.
- Объединить в одну группу ресурсов для упрощения отслеживания этих ресурсов и установки все ресурсы лаборатории [политики](../governance/policy/overview.md) для управления ресурсами на уровне группы ресурсов.

С помощью этой функции можно использовать сценарий для указания новой или существующей группе ресурсов в подписке Azure для всех виртуальных машин лаборатории. В настоящее время Azure DevTest Labs поддерживает эту функцию с помощью API.

> [!NOTE]
> При создании лаборатории в DevTest Labs, действуют все ограничения подписки. Считать лаборатории любой другой ресурс в вашей подписке. При наличии групп ресурсов, это значение равно [980 групп ресурсов на одну подписку](../azure-subscription-service-limits.md#subscription-limits---azure-resource-manager). 

## <a name="use-azure-portal"></a>Использование портала Azure
Выполните следующие действия, чтобы указать группу ресурсов для всех виртуальных машин, созданных в лаборатории. 

1. Войдите на [портале Azure](https://portal.azure.com).
2. Выберите **все службы** в левом меню навигации. 
3. Выберите **DevTest Labs** из списка.
4. Из списка лабораторий выберите ваш **лаборатории**.  
5. Выберите **конфигурация и политики** в **параметры** раздел, в меню слева. 
6. Выберите **параметры лаборатории** в меню слева. 
7. Выберите **все виртуальные машины в одной группе ресурсов**. 
8. Выберите существующую группу ресурсов в раскрывающемся списке (или) выберите **создать**, введите **имя** для группы ресурсов и выберите **ОК**. 

    ![Выберите группу ресурсов для всех виртуальных машин в лаборатории](./media/resource-group-control/select-resource-group.png)

## <a name="use-powershell"></a>Использование PowerShell 
Приведенный ниже показано, как использовать сценарий PowerShell для создания всех виртуальных машин лаборатории в группу ресурсов.

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

Вызовите сценарий с помощью следующей команды. ResourceGroup.ps1 — это файл, содержащий приведенный выше сценарий:

```powershell
.\ResourceGroup.ps1 -subId <subscriptionID> -labRg <labRGNAme> -labName <LanName> -vmRg <RGName> 
```

## <a name="use-an-azure-resource-manager-template"></a>Использовать шаблон Azure Resource Manager
Если вы используете шаблон Azure Resource Manager для создания лаборатории, используйте **vmCreationResourceGroupId** свойства в разделе свойств лабораторного шаблона, как показано в следующем примере:

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


## <a name="api-to-configure-a-resource-group-for-lab-vms"></a>API, чтобы настроить группу ресурсов для виртуальных машин лаборатории
При использовании этого API в качестве владельца лаборатории доступны следующие варианты:

- Выберите **группы ресурсов лаборатории** для всех виртуальных машин.
- Выберите **существующую группу ресурсов** отличный от группы ресурсов лаборатории для всех виртуальных машин.
- Введите **новую группу ресурсов** имя для всех виртуальных машин.
- Продолжайте использовать существующее поведение, в котором создается группа ресурсов для каждой виртуальной Машины в лаборатории.
 
Этот параметр применяется к новым виртуальным машинам, созданным в лаборатории. Более старые виртуальные машины в лаборатории, которые были созданы в собственные группы ресурсов не затрагиваются. Среды, созданные в лаборатории по-прежнему остаются в собственные группы ресурсов.

Использование этого API.
- Используйте API версии **2018_10_15_preview**.
- Если указать группу ресурсов, убедитесь, что у вас есть **разрешения на запись о группах ресурсов** в вашей подписке. Создание новых виртуальных машин в указанной группе ресурсов завершится ошибкой при отсутствии разрешения на запись.
- При использовании API передайте **полный идентификатор группы ресурсов**. Например, `/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroupName>`. Убедитесь, что группа ресурсов находится в той же подписке, что и лаборатория. 


## <a name="next-steps"></a>Дальнейшие действия
Ознакомьтесь со следующими статьями: 

- [Управление базовыми политиками лаборатории в Azure DevTest Labs](devtest-lab-get-started-with-lab-policies.md)
- [Часто задаваемые вопросы](devtest-lab-faq.md)
