---
title: Настройка рабочей области Log Analytics для Azure Monitor для виртуальных машин
description: Описание создания и настройки Log Analytics рабочей области, используемой Azure Monitor для виртуальных машин.
ms.subservice: ''
ms.topic: conceptual
ms.custom: references_regions
author: bwren
ms.author: bwren
ms.date: 12/22/2020
ms.openlocfilehash: b84f9cae848d53cf04e1b77810b347786e122c5b
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100625188"
---
# <a name="configure-log-analytics-workspace-for-azure-monitor-for-vms"></a>Настройка рабочей области Log Analytics для Azure Monitor для виртуальных машин
Azure Monitor для виртуальных машин собирает данные из одной или нескольких Log Analytics рабочих областей в Azure Monitor. Перед подключением агентов необходимо создать и настроить рабочую область. В этой статье описываются требования к рабочей области и ее настройка для Azure Monitor для виртуальных машин.

## <a name="overview"></a>Обзор
Одна подписка может использовать любое количество рабочих областей в зависимости от требований. Единственное требование к рабочей области заключается в том, что она находится в поддерживаемом расположении и настроена с помощью решения *вминсигхтс* .

После настройки рабочей области можно использовать любой из доступных параметров для установки необходимых агентов на виртуальной машине и масштабируемом наборе виртуальных машин, а также указать рабочую область для отправки их данных. Azure Monitor для виртуальных машин соберет данные из любой настроенной рабочей области в своей подписке.

> [!NOTE]
> При включении Azure Monitor для виртуальных машин на одной виртуальной машине или в масштабируемом наборе виртуальных машин с помощью портал Azure вы можете выбрать существующую рабочую область или создать новую. Решение *вминсигхтс* будет установлено в этой рабочей области, если это еще не сделано. Затем эту рабочую область можно использовать для других агентов.


## <a name="create-log-analytics-workspace"></a>Создание рабочей области Log Analytics

>[!NOTE]
>Сведения, описанные в этом разделе, также применимы к [решению сопоставление служб](service-map.md). 

Доступ к Log Analytics рабочим областям в портал Azure из меню **log Analytics рабочие области** .

[![Рабочие области Анлитикс журналов](media/vminsights-configure-workspace/log-analytics-workspaces.png)](media/vminsights-configure-workspace/log-analytics-workspaces.png#lightbox)

Новую рабочую область Log Analytics можно создать одним из следующих способов. Инструкции по определению количества рабочих областей, которые следует использовать в вашей среде, а также о проектировании стратегии доступа см. в разделе [проектирование развертывания журналов Azure Monitor](../platform/design-logs-deployment.md) .


* [Портал Azure](../../azure-monitor/learn/quick-create-workspace.md)
* [Azure CLI](../../azure-monitor/learn/quick-create-workspace-cli.md)
* [PowerShell](../platform/powershell-workspace-configuration.md)
* [Azure Resource Manager](../samples/resource-manager-workspace.md)

## <a name="supported-regions"></a>Поддерживаемые регионы
Azure Monitor для виртуальных машин поддерживает рабочую область Log Analytics в любом из [регионов, поддерживаемых log Analytics](https://azure.microsoft.com/global-infrastructure/services/?products=monitor&regions=all) за исключением следующих:

- Центрально-Западная Германия
- Республика Корея, центральный регион

>[!NOTE]
>Вы можете отслеживать виртуальные машины Azure в любом регионе. Сами виртуальные машины не ограничиваются регионами, поддерживаемыми Log Analytics рабочей областью.

## <a name="azure-role-based-access-control"></a>Управление доступом на основе ролей в Azure
Чтобы включить функции и получить доступ к ним в Azure Monitor для виртуальных машин, необходимо иметь [роль участника log Analytics](../platform/manage-access.md#manage-access-using-azure-permissions) в рабочей области. Для просмотра данных о производительности, работоспособности и сопоставлении необходимо иметь [роль "читатель мониторинга](../platform/roles-permissions-security.md#built-in-monitoring-roles) " для виртуальной машины Azure. Дополнительные сведения о том, как управлять доступом к рабочей области Log Analytics, см. в статье [Управление рабочими областями](../platform/manage-access.md).

## <a name="add-vminsights-solution-to-workspace"></a>Добавление решения Вминсигхтс в рабочую область
Прежде чем Log Analytics рабочую область можно будет использовать с Azure Monitor для виртуальных машин, необходимо установить решение *вминсигхтс* . Методы настройки рабочей области описаны в следующих разделах.

> [!NOTE]
> При добавлении решения *вминсигхтс* в рабочую область все существующие виртуальные машины, подключенные к рабочей области, начнут отсылать данные в инсигхтсметрикс. Данные для других типов данных не будут собираться, пока вы не добавите Dependency Agent к этим существующим виртуальным машинам, подключенным к рабочей области.

### <a name="azure-portal"></a>Портал Azure
Существует три варианта настройки существующей рабочей области с помощью портал Azure. Каждая составляющая формулы описана ниже.

Чтобы настроить одну рабочую область, перейдите к параметру **виртуальные машины** в меню **Azure Monitor** , выберите **другие параметры адаптации**, а затем **Настройте рабочую область**. Выберите подписку и рабочую область и нажмите кнопку **настроить**.

[![Настройка рабочей области](../vm/media/vminsights-enable-policy/configure-workspace.png)](../vm/media/vminsights-enable-policy/configure-workspace.png#lightbox)

Чтобы настроить несколько рабочих областей, выберите вкладку **Конфигурация рабочей области** в меню **виртуальные машины** в портал Azure.  Установите значения фильтра, чтобы отобразить список существующих рабочих областей. Установите флажок рядом с каждой рабочей областью, чтобы включить ее, а затем нажмите кнопку **настроить выбранные**.

[![Конфигурация рабочей области](../vm/media/vminsights-enable-policy/workspace-configuration.png)](../vm/media/vminsights-enable-policy/workspace-configuration.png#lightbox)


При включении Azure Monitor для виртуальных машин на одной виртуальной машине или в масштабируемом наборе виртуальных машин с помощью портал Azure вы можете выбрать существующую рабочую область или создать новую. Решение *вминсигхтс* будет установлено в этой рабочей области, если это еще не сделано. Затем эту рабочую область можно использовать для других агентов.

[![Включение отдельной виртуальной машины на портале](../vm/media/vminsights-enable-portal/enable-vminsights-vm-portal.png)](../vm/media/vminsights-enable-portal/enable-vminsights-vm-portal.png#lightbox)


### <a name="resource-manager-template"></a>Шаблон Resource Manager
Шаблоны Azure Resource Manager для Azure Monitor для виртуальных машин приведены в файле архива (ZIP-файл), который можно [скачать из нашего репозитория GitHub](https://aka.ms/VmInsightsARMTemplates). Сюда входит шаблон с именем **конфигуреворкспаце** , который настраивает log Analytics рабочей области для Azure Monitor для виртуальных машин. Этот шаблон развертывается с помощью любого из стандартных методов, включая примеры команд PowerShell и CLI, приведенные ниже. 

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli
az deployment group create --name ConfigureWorkspace --resource-group my-resource-group --template-file CreateWorkspace.json  --parameters workspaceResourceId='/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/my-resource-group/providers/microsoft.operationalinsights/workspaces/my-workspace' workspaceLocation='eastus'

```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```powershell
New-AzResourceGroupDeployment -Name ConfigureWorkspace -ResourceGroupName my-resource-group -TemplateFile ConfigureWorkspace.json -workspaceResourceId /subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/my-resource-group/providers/microsoft.operationalinsights/workspaces/my-workspace -location eastus
```

---



## <a name="next-steps"></a>Дальнейшие действия
- См. Дополнительные сведения о подключении [агентов для Azure Monitor для виртуальных машин](vminsights-enable-overview.md) подключения агентов к Azure Monitor для виртуальных машин.
- См. раздел [нацеленность решений мониторинга в Azure Monitor (Предварительная версия)](../insights/solution-targeting.md) , чтобы ограничить объем данных, отправляемых из решения в рабочую область.
