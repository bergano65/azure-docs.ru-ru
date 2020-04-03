---
title: Создание параметра диагностики для сбора журналов и метрик в Azure
description: Создавайте диагностические настройки для переадресации журналов платформы Azure в журналы Azure Monitor, хранилище Azure или концентраторы событий Azure.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: 6ff9e62ed145b7892d2997193dae8e171ae49c98
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80585381"
---
# <a name="create-diagnostic-setting-to-collect-platform-logs-and-metrics-in-azure"></a>Создание диагностических параметров для сбора журналов платформ и метрик в Azure
[Платформы журналов](platform-logs-overview.md) в Azure, включая журналы деятельности Azure и журналы ресурсов, предоставляют подробную информацию о диагностике и аудите для ресурсов Azure и платформы Azure, от которой они зависят. В этой статье приводится подробная информация о создании и настройке диагностических настроек для отправки журналов платформ в различные пункты назначения.

> [!IMPORTANT]
> Перед созданием диагностической настройки для сбора журнала activity следует сначала отключить любую устаревшую конфигурацию. Просмотрите [журнал «Сбор azure Activity» с устаревшими настройками](diagnostic-settings-legacy.md) для получения подробной информации.

Каждый ресурс Azure требует собственной диагностической настройки, которая определяет следующее:

- Категории журналов и данных метрики, отправляемых по назначениям, указанным в параметре. Доступные категории будут отличаться для разных типов ресурсов.
- здесь указываются одно или несколько назначений для отправки журналов. Текущие назначения включают в себя рабочую область Log Analytics, Центры событий и службу хранилища Azure.
 
Один диагностический параметр может определить не более одного из пунктов назначения. Если необходимо отправить данные в места назначения нескольких типов (например, в две разные рабочие области Log Analytics), создайте несколько параметров. Каждый ресурс может иметь до 5 параметров диагностики.


> [!NOTE]
> [Метрики платформы](metrics-supported.md) собираются автоматически в [Azure Monitor Metrics.](data-platform-metrics.md) Диагностические настройки могут использоваться для сбора метрик для определенных служб Azure в журналы Azure Monitor для анализа с другими данными мониторинга с помощью [журналов.](../log-query/log-query-overview.md)

## <a name="destinations"></a>Назначения 
Платформные журналы могут быть отправлены в пункты назначения в следующей таблице. Конфигурация для каждого пункта назначения выполняется с использованием одного и того же процесса для создания диагностических настроек, описанных в этой статье. Перейти к каждой ссылке в следующей таблице для получения подробной информации об отправке данных в этот пункт назначения.

| Назначение | Описание |
|:---|:---|
| [Рабочее пространство аналитики журналов](resource-logs-collect-workspace.md) | Сбор журналов в рабочее пространство Log Analytics позволяет анализировать их с помощью других данных мониторинга, собранных Azure Monitor, с помощью мощных запросов журналов, а также использовать другие функции Azure Monitor, такие как оповещения и визуализации. |
| [Центры событий](resource-logs-stream-event-hubs.md) | Отправка журналов в концентраторы событий позволяет передавать данные на внешние системы, такие как сторонние SIEMs и другие решения для анализа журналов. |
| [Учетная запись хранения Azure](resource-logs-collect-storage.md) | Архивирование журналов в учетной записи хранения Azure полезно для аудита, статического анализа или резервного копирования. |

## <a name="create-diagnostic-settings-in-azure-portal"></a>Создание диагностических настроек на портале Azure
Настройку диагностических настроек можно на портале Azure либо из меню Azure Monitor, либо из меню для ресурса.

1. Место настройки диагностических настроек на портале Azure зависит от ресурса.

   - Для одного ресурса нажмите **Параметры диагностики** под **монитором** в меню ресурса.

        ![Параметры диагностики](media/diagnostic-settings/menu-resource.png)

    - Для одного или нескольких ресурсов нажмите **параметры Диагностики** в **меню «Настройки»** в меню Azure Monitor, а затем нажмите на ресурс.
    
        ![Параметры диагностики](media/diagnostic-settings/menu-monitor.png)

    - Для журнала activity нажмите **журнал Activity** в меню **Azure Monitor,** а затем **в настройках Диагностики.** Убедитесь, что вы отогивайте любую устаревшую конфигурацию для журнала activity. [Отогивайте существующие настройки](/azure/azure-monitor/platform/activity-log-collect#collecting-activity-log) для получения подробной информации.

        ![Параметры диагностики](media/diagnostic-settings/menu-activity-log.png)

2. Если параметров для выбранного ресурса не существует, вам будет предложено создать параметр. Щелкните **Добавить параметр диагностики**.

   ![Добавление параметра диагностики — имеющиеся параметры отсутствуют](media/diagnostic-settings/add-setting.png)

   При наличии существующих параметров на ресурсе будет увидеть уже настроенный список настроек. Либо нажмите **«Добавить диагностическую настройку»,** чтобы добавить новую настройку, или **отсеивайте настройки** для отсвасывания существующей. Каждый параметр может иметь не более одного из каждого типа назначения.

   ![Добавление параметра диагностики — имеющиеся параметры](media/diagnostic-settings/edit-setting.png)

3. Дайте параметру имя, если оно еще не имеет.
4. Проверьте поле для каждого пункта назначения, чтобы отправить журналы. Нажмите **Нанастройка,** чтобы указать их настройки, описанные в следующей таблице.

    | Параметр | Описание |
    |:---|:---|
    | Рабочая область Log Analytics | Название рабочего пространства. |
    | Учетная запись хранения | Имя учетной записи хранилища. |
    | Пространство имен концентратора событий | Пространство имен, в котором создается концентратор событий (если это ваш первый раз потоковое журналы) или потоковое (если уже есть ресурсы, которые потоковой передачи этой категории журнала в это пространство имен).
    | Имя концентратора событий | Дополнительно укажите имя концентратора событий для отправки всех данных в настройках. Если не указано имя, для каждой категории журнала создается концентратор событий. Если вы отправляете несколько категорий, можно указать имя, чтобы ограничить количество созданных концентраторов событий. Ознакомьтесь с [квотами и лимитами концентратов событий Azure.](../../event-hubs/event-hubs-quotas.md) |
    | Имя политики концентратора событий | Определяет разрешения, которыми обладает механизм потоковой передачи. |

    ![Добавление параметра диагностики — имеющиеся параметры](media/diagnostic-settings/setting-details.png)

5. Проверьте поле для каждой из категорий данных для отправки в указанные пункты назначения. Список категорий будет меняться для каждой службы Azure.

   > [!NOTE]
   > Отправка многомерных метрик с помощью параметров диагностики сейчас не поддерживается. Метрики с измерениями экспортируются как преобразованные в плоскую структуру одномерные метрики, агрегированные по значениям измерений.
   >
   > *Пример.* Метрику "Входящие сообщения" в концентраторе событий можно изучить и вывести в виде диаграммы на уровне очереди. Но при экспорте с помощью параметров диагностики метрика будет представлена в виде всех входящих сообщений для всех очередей концентратора событий.

6. Нажмите **Сохранить**.

Через несколько мгновений новая настройка отображается в списке настроек для этого ресурса, и журналы передаются в указанные пункты назначения по мере создания новых данных событий. Обратите внимание, что может быть до пятнадцати минут между тем, когда событие излучается и когда он [появляется в рабочей области журнала Analytics](data-ingestion-time.md).



## <a name="create-diagnostic-settings-using-powershell"></a>Создание диагностических настроек с помощью PowerShell
Используйте [cmdlet Set-AzDiagnosticSetting](https://docs.microsoft.com/powershell/module/az.monitor/set-azdiagnosticsetting) для создания диагностической настройки с [azure PowerShell.](powershell-quickstart-samples.md) Для описания его параметров можно ознакомиться с документацией для этого cmdlet.

> [!IMPORTANT]
> Этот метод нельзя использовать для журнала деятельности Azure. Вместо этого используйте [создание диагностических параметров в Azure Monitor с помощью шаблона «Менеджер ресурсов»](diagnostic-settings-template.md) для создания шаблона «Менеджер ресурсов» и развертывания его с помощью PowerShell.

Ниже приводится пример PowerShell cmdlet для создания диагностической настройки, используя все три направления.


```powershell
Set-AzDiagnosticSetting -Name KeyVault-Diagnostics -ResourceId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault -Category AuditEvent -MetricCategory AllMetrics -Enabled $true -StorageAccountId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount -WorkspaceId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/myworkspace  -EventHubAuthorizationRuleId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhub/authorizationrules/RootManageSharedAccessKey
```


## <a name="create-diagnostic-settings-using-azure-cli"></a>Создание диагностических настроек с помощью Azure CLI
Используйте [диагностические настройки монитора az для создания](https://docs.microsoft.com/cli/azure/monitor/diagnostic-settings?view=azure-cli-latest#az-monitor-diagnostic-settings-create) диагностической настройки с [Azure CLI.](https://docs.microsoft.com/cli/azure/monitor?view=azure-cli-latest) Ознакомиться с документацией этой команды можно с описанием ее параметров.

> [!IMPORTANT]
> Этот метод нельзя использовать для журнала деятельности Azure. Вместо этого используйте [создание диагностических параметров в Azure Monitor с помощью шаблона «Менеджер ресурсов»](diagnostic-settings-template.md) для создания шаблона «Менеджер ресурсов» и развертывания его с помощью CLI.

Ниже приводится пример команды CLI для создания диагностической настройки, используя все три направления.



```azurecli
az monitor diagnostic-settings create  \
--name KeyVault-Diagnostics \
--resource /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault \
--logs    '[{"category": "AuditEvent","enabled": true}]' \
--metrics '[{"category": "AllMetrics","enabled": true}]' \
--storage-account /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount \
--workspace /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/myworkspace \
--event-hub-rule /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhub/authorizationrules/RootManageSharedAccessKey
```

### <a name="configure-diagnostic-settings-using-rest-api"></a>Настройка диагностических настроек с помощью REST API
Просмотрите [диагностические настройки](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings) для создания или обновления диагностических настроек с помощью [API Azure Monitor REST.](https://docs.microsoft.com/rest/api/monitor/)


### <a name="configure-diagnostic-settings-using-resource-manager-template"></a>Настройка диагностических настроек с помощью шаблона «Менеджер ресурсов»
Просрвините [диагностическую настройку в Azure Monitor с помощью шаблона «Менеджер ресурсов»](diagnostic-settings-template.md) для создания или обновления диагностических настроек с помощью шаблона «Менеджер ресурсов».

## <a name="next-steps"></a>Следующие шаги

* [Узнайте больше о журналах платформы Azure](platform-logs-overview.md)
