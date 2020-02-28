---
title: Создание параметра диагностики для сбора журналов и метрик в Azure
description: Создайте параметры диагностики для пересылки журналов платформы Azure в журналы Azure Monitor, службу хранилища Azure или концентраторы событий Azure.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: fb2f9ff5af68575d9f9d29e9a6aca83d603395b3
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/27/2020
ms.locfileid: "77672418"
---
# <a name="create-diagnostic-setting-to-collect-platform-logs-and-metrics-in-azure"></a>Создание параметра диагностики для сбора журналов и метрик платформы в Azure
[Журналы платформы](platform-logs-overview.md) в Azure, в том числе журнал действий Azure и журналы ресурсов, предоставляют подробные сведения о диагностике и аудите для ресурсов Azure и платформы Azure, от которых они зависят. В этой статье приводятся сведения о создании и настройке параметров диагностики для отправки журналов платформы в разные места назначения.

> [!IMPORTANT]
> Перед созданием параметра диагностики для получения журнала действий необходимо сначала отключить все устаревшие конфигурации. Дополнительные сведения см. [в статье Получение журнала действий Azure с устаревшими параметрами](diagnostic-settings-legacy.md) .

Для каждого ресурса Azure требуется собственный параметр диагностики, который определяет следующее:

- Категории журналов и данные метрик, отправляемые в назначения, определенные в параметре. Доступные категории могут различаться для разных типов ресурсов.
- здесь указываются одно или несколько назначений для отправки журналов. Текущие назначения включают в себя Log Analytics рабочую область, концентраторы событий и службу хранилища Azure.
 
Один параметр диагностики может определять не более одного из этих назначений. Если требуется отправить данные более чем одному определенному типу назначения (например, в двух разных рабочих областях Log Analytics), создайте несколько параметров. У каждого ресурса может быть до 5 параметров диагностики.


> [!NOTE]
> [Метрики платформы](metrics-supported.md) собираются автоматически для [Azure Monitor метрик](data-platform-metrics.md). Параметры диагностики можно использовать для сбора метрик для определенных служб Azure в журналы Azure Monitor для анализа с другими данными мониторинга с помощью [запросов журналов](../log-query/log-query-overview.md).

## <a name="destinations"></a>Назначения 
Журналы платформы могут быть отправлены в назначения, указанные в следующей таблице. Конфигурация для каждого назначения выполняется с помощью того же процесса для создания параметров диагностики, описанных в этой статье. Чтобы получить дополнительные сведения об отправке данных в это место назначения, используйте ссылку в следующей таблице.

| Назначение | Описание |
|:---|:---|
| [Рабочая область Log Analytics](resource-logs-collect-workspace.md) | Сбор журналов в рабочую область Log Analytics позволяет анализировать их с помощью других данных мониторинга, собираемых Azure Monitor используя мощные запросы журналов, а также использовать другие функции Azure Monitor, такие как оповещения и визуализации. |
| [Центры событий](resource-logs-stream-event-hubs.md) | Отправка журналов в концентраторы событий позволяет передавать данные в внешние системы, такие как сторонние решения Siem и другие решения log Analytics. |
| [Учетная запись хранения Azure](resource-logs-collect-storage.md) | Архивация журналов в учетную запись хранения Azure полезна для аудита, статического анализа или резервного копирования. |

## <a name="create-diagnostic-settings-in-azure-portal"></a>Создание параметров диагностики в портал Azure
Параметры диагностики можно настроить в портал Azure в меню Azure Monitor или в меню для ресурса.

1. Настройка параметров диагностики в портал Azure зависит от ресурса.

   - Для одного ресурса щелкните **параметры диагностики** в разделе **монитор** в меню ресурса.

        ![Параметры диагностики](media/diagnostic-settings/menu-resource.png)

    - Для одного или нескольких ресурсов щелкните **параметры диагностики** в разделе **параметры** в меню Azure Monitor, а затем щелкните ресурс.
    
        ![Параметры диагностики](media/diagnostic-settings/menu-monitor.png)

    - Для журнала действий щелкните **Журнал действий** в меню **Azure Monitor** , а затем **параметры диагностики**. Убедитесь, что отключены все устаревшие конфигурации для журнала действий. Дополнительные сведения см. в разделе [Отключение существующих параметров](diagnostic-settings-legacy.md#disable-existing-settings) .

        ![Параметры диагностики](media/diagnostic-settings/menu-activity-log.png)

2. Если параметров для выбранного ресурса не существует, вам будет предложено создать параметр. Щелкните **Добавить параметр диагностики**.

   ![Добавление параметра диагностики — имеющиеся параметры отсутствуют](media/diagnostic-settings/add-setting.png)

   Если в ресурсе есть параметры, вы увидите список уже настроенных параметров. Нажмите кнопку **Добавить параметр диагностики** , чтобы добавить новый параметр или **изменить параметр** , чтобы изменить существующий. Каждый параметр может иметь не более одного из целевых типов.

   ![Добавление параметра диагностики — имеющиеся параметры](media/diagnostic-settings/edit-setting.png)

3. Присвойте параметру имя, если его еще нет.
4. Установите флажок для каждого назначения, чтобы отправить журналы. Нажмите кнопку **настроить** , чтобы указать параметры, как описано в следующей таблице.

    | Параметр | Описание |
    |:---|:---|
    | Рабочая область Log Analytics | Имя рабочей области. |
    | Учетная запись хранения | Имя учетной записи хранения. |
    | Пространство имен концентратора событий | Пространство имен, в котором создается концентратор событий (если это первый журнал потоковой передачи) или потоковая передача (если уже есть ресурсы, которые используют потоковую передачу категории журнала в это пространство имен).
    | Имя концентратора событий | При необходимости укажите имя концентратора событий для отправки всех данных в параметре. Если имя не указано, для каждой категории журнала создается концентратор событий. При отправке нескольких категорий может потребоваться указать имя, чтобы ограничить число создаваемых концентраторов событий. Дополнительные сведения см. в статье [квоты и ограничения концентраторов событий Azure](../../event-hubs/event-hubs-quotas.md) . |
    | Имя политики концентратора событий | Определяет разрешения, которые имеет механизм потоковой передачи. |

    ![Добавление параметра диагностики — имеющиеся параметры](media/diagnostic-settings/setting-details.png)

5. Установите флажок для каждой из категорий данных, отправляемых в указанные места назначения. Список категорий будет различным для каждой службы Azure.

   > [!NOTE]
   > Отправка многомерных метрик с помощью параметров диагностики сейчас не поддерживается. Метрики с измерениями экспортируются как преобразованные в плоскую структуру одномерные метрики, агрегированные по значениям измерений.
   >
   > *Пример.* Метрику "Входящие сообщения" в концентраторе событий можно изучить и вывести в виде диаграммы на уровне очереди. Но при экспорте с помощью параметров диагностики метрика будет представлена в виде всех входящих сообщений для всех очередей концентратора событий.

6. Нажмите кнопку **Сохранить**.

Через несколько секунд новый параметр появится в списке параметров для этого ресурса, а журналы будут передаваться в указанное место назначения по мере создания данных о событиях. Обратите внимание, что между созданием события и его [отображением в log Analytics рабочей области](data-ingestion-time.md)может потребоваться до пятнадцать минут.



## <a name="create-diagnostic-settings-using-powershell"></a>Создание параметров диагностики с помощью PowerShell
Используйте командлет [Set-аздиагностиксеттинг](https://docs.microsoft.com/powershell/module/az.monitor/set-azdiagnosticsetting) , чтобы создать параметр диагностики с [Azure PowerShell](powershell-quickstart-samples.md). Описание его параметров см. в документации по этому командлету.

> [!IMPORTANT]
> Этот метод нельзя использовать для журнала действий Azure. Вместо этого используйте [параметр создать диагностику в Azure Monitor используя шаблон диспетчер ресурсов](diagnostic-settings-template.md) , чтобы создать шаблон диспетчер ресурсов и развернуть его с помощью PowerShell.

Ниже приведен пример командлета PowerShell для создания параметра диагностики с использованием всех трех назначений.


```powershell
Set-AzDiagnosticSetting -Name KeyVault-Diagnostics -ResourceId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault -Category AuditEvent -MetricCategory AllMetrics -Enabled $true -StorageAccountId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount -WorkspaceId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/myworkspace  -EventHubAuthorizationRuleId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhub/authorizationrules/RootManageSharedAccessKey
```


## <a name="create-diagnostic-settings-using-azure-cli"></a>Создание параметров диагностики с помощью Azure CLI
Используйте команду [AZ Monitor диагностики-Settings Create](https://docs.microsoft.com/cli/azure/monitor/diagnostic-settings?view=azure-cli-latest#az-monitor-diagnostic-settings-create) , чтобы создать параметр диагностики с [Azure CLI](https://docs.microsoft.com/cli/azure/monitor?view=azure-cli-latest). Описание параметров см. в документации по этой команде.

> [!IMPORTANT]
> Этот метод нельзя использовать для журнала действий Azure. Вместо этого используйте [параметр создать диагностику в Azure Monitor используя шаблон диспетчер ресурсов](diagnostic-settings-template.md) , чтобы создать шаблон диспетчер ресурсов и развернуть его с помощью интерфейса командной строки.

Ниже приведен пример команды CLI для создания параметра диагностики с использованием всех трех назначений.



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

### <a name="configure-diagnostic-settings-using-rest-api"></a>Настройка параметров диагностики с помощью REST API
Сведения о создании или обновлении параметров диагностики с помощью [REST API Azure Monitor](https://docs.microsoft.com/rest/api/monitor/)см. в разделе [параметры диагностики](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings) .


### <a name="configure-diagnostic-settings-using-resource-manager-template"></a>Настройка параметров диагностики с помощью шаблона диспетчер ресурсов
См. раздел [Создание параметра диагностики в Azure Monitor использование шаблона диспетчер ресурсов](diagnostic-settings-template.md) для создания или обновления параметров диагностики с помощью шаблона диспетчер ресурсов.

## <a name="next-steps"></a>Следующие шаги

* [Подробнее о журналах платформы Azure](platform-logs-overview.md)
