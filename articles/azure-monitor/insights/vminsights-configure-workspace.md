---
title: Настройка рабочей области Log Analytics для Azure Monitor для виртуальных машин
description: Описание создания и настройки Log Analytics рабочей области, используемой Azure Monitor для виртуальных машин.
ms.subservice: ''
ms.topic: conceptual
ms.custom: references_regions
author: bwren
ms.author: bwren
ms.date: 07/27/2020
ms.openlocfilehash: 10c4f0ba4bfc88017304c228ca1afce4ba863118
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87328456"
---
# <a name="configure-log-analytics-workspace-for-azure-monitor-for-vms"></a>Настройка рабочей области Log Analytics для Azure Monitor для виртуальных машин
Azure Monitor для виртуальных машин собирает данные из одной или нескольких Log Analytics рабочих областей в Azure Monitor. Перед подключением агентов необходимо создать и настроить рабочую область. В этой статье описываются требования к рабочей области и ее настройка для Azure Monitor для виртуальных машин.

## <a name="overview"></a>Обзор
Одна подписка может использовать любое количество рабочих областей в зависимости от требований. Единственное требование к рабочей области — это то, что оно находится в поддерживаемом расположении и настроено с помощью решения *вминсигхтс* .

После настройки рабочей области можно использовать любой из доступных параметров для установки необходимых агентов на виртуальной машине и VMMS, а также указать рабочую область для отправки их данных. Azure Monitor для виртуальных машин соберет данные из любой настроенной рабочей области в своей подписке.

> [!NOTE]
> При включении Azure Monitor для виртуальных машин на одной виртуальной машине или VMMS с помощью портал Azure вы можете выбрать существующую рабочую область или создать новую. Решение *вминсигхтс* будет установлено в этой рабочей области, если это еще не сделано. Затем эту рабочую область можно использовать для других агентов.


## <a name="create-log-analytics-workspace"></a>Создание рабочей области Log Analytics

>[!NOTE]
>Сведения, описанные в этом разделе, также применимы к [решению сопоставление служб](service-map.md). 

Доступ к Log Analytics рабочим областям в портал Azure из меню **log Analytics рабочие области** .

[![Рабочие области Анлитикс журналов](media/vminsights-configure-workspace/log-analytics-workspaces.png)](media/vminsights-configure-workspace/log-analytics-workspaces.png#lightbox)

Новую рабочую область Log Analytics можно создать одним из следующих способов. Инструкции по определению количества рабочих областей, которые следует использовать в вашей среде, а также о проектировании стратегии доступа см. в разделе [проектирование развертывания журналов Azure Monitor](../platform/design-logs-deployment.md) .


* [Портал Azure](../../azure-monitor/learn/quick-create-workspace.md)
* [Azure CLI](../../azure-monitor/learn/quick-create-workspace-cli.md)
* [PowerShell](../../azure-monitor/learn/quick-create-workspace-posh.md)
* [Azure Resource Manager](../../azure-monitor/platform/template-workspace-configuration.md)

## <a name="supported-regions"></a>Поддерживаемые регионы

Azure Monitor для виртуальных машин поддерживает рабочие области Log Analytics в следующих регионах, хотя вы можете отслеживать виртуальные машины в любом регионе. Сами виртуальные машины не ограничиваются регионами, поддерживаемыми рабочей областью Log Analytics.

- центрально-западная часть США
- западная часть США
- западная часть США 2
- Центрально-южная часть США
- East US
- восточная часть США 2
- Центральная часть США
- Центрально-северная часть США
- US Gov AZ
- US Gov ва
- Центральная Канада
- южная часть Соединенного Королевства
- Северная Европа
- Западная Европа
- Восточная Азия
- Юго-Восточная Азия
- Центральная Индия
- Восточная Япония
- Восточная Австралия
- Юго-Восточная часть Австралии

## <a name="role-based-access-control"></a>Управление доступом на основе ролей
Чтобы включить функции и получить доступ к ним в Azure Monitor для виртуальных машин, необходимо иметь [роль участника log Analytics](../platform/manage-access.md#manage-access-using-azure-permissions) в рабочей области. Для просмотра данных о производительности, работоспособности и сопоставлении необходимо иметь [роль "читатель мониторинга](../platform/roles-permissions-security.md#built-in-monitoring-roles) " для виртуальной машины Azure. Дополнительные сведения о том, как управлять доступом к рабочей области Log Analytics, см. в статье [Управление рабочими областями](../platform/manage-access.md).

## <a name="add-vminsights-solution-to-workspace"></a>Добавление решения Вминсигхтс в рабочую область
Прежде чем Log Analytics рабочую область можно будет использовать с Azure Monitor для виртуальных машин, необходимо установить решение *вминсигхтс* . Методы настройки рабочей области описаны в следующих разделах.

> [!NOTE]
> При добавлении решения *вминсигхтс* в рабочую область все существующие виртуальные машины, подключенные к рабочей области, начнут отсылать данные в инсигхтсметрикс. Данные для других типов данных не будут собираться, пока вы не добавите Dependency Agent к этим существующим виртуальным машинам, подключенным к рабочей области.

### <a name="azure-portal"></a>Портал Azure
Существует три варианта настройки существующей рабочей области из портал Azure.

Чтобы настроить одну рабочую область, выберите **другие параметры адаптации** , а затем **Настройте рабочую область**. Выберите подписку и рабочую область и нажмите кнопку **настроить**.

[![Настройка рабочей области](media/vminsights-enable-at-scale-policy/configure-workspace.png)](media/vminsights-enable-at-scale-policy/configure-workspace.png#lightbox)

Чтобы настроить несколько рабочих областей, выберите вкладку **Конфигурация рабочей области** в меню **виртуальные машины** в портал Azure. **Monitor** Установите значения фильтра, чтобы отобразить список существующих рабочих областей. Установите флажок рядом с каждой рабочей областью, чтобы включить ее, а затем нажмите кнопку **настроить выбранные** .

[![Конфигурация рабочей области](media/vminsights-enable-at-scale-policy/workspace-configuration.png)](media/vminsights-enable-at-scale-policy/workspace-configuration.png#lightbox)


При включении Azure Monitor для виртуальных машин на одной виртуальной машине или VMMS с помощью портал Azure вы можете выбрать существующую рабочую область или создать новую. Решение *вминсигхтс* будет установлено в этой рабочей области, если это еще не сделано. Затем эту рабочую область можно использовать для других агентов.

[![Включение отдельной виртуальной машины на портале](media/vminsights-enable-single-vm/enable-vminsights-vm-portal.png)](media/vminsights-enable-single-vm/enable-vminsights-vm-portal.png#lightbox)


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
- См. раздел [нацеленность решений мониторинга в Azure Monitor (Предварительная версия)](solution-targeting.md) , чтобы ограничить объем данных, отправляемых из решения в рабочую область.