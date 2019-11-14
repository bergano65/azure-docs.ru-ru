---
title: Создание параметра диагностики для сбора журналов и метрик в Azure | Документация Майкрософт
description: Создайте параметры диагностики для пересылки журналов платформы Azure в журналы Azure Monitor, службу хранилища Azure или концентраторы событий Azure.
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 07/31/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: b90e5ccf38e95d33c4b5b6f3b8da0e91a4facb5a
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/13/2019
ms.locfileid: "74023744"
---
# <a name="create-diagnostic-setting-to-collect-platform-logs-and-metrics-in-azure"></a>Создание параметра диагностики для сбора журналов и метрик платформы в Azure
[Журналы платформы](resource-logs-overview.md) в Azure предоставляют подробные сведения о диагностике и аудите для ресурсов Azure и платформы Azure, от которых они зависят. В этой статье содержатся сведения о создании и настройке параметров диагностики для получения журналов платформы в разные места назначения.

Для каждого ресурса Azure требуется собственный параметр диагностики. Параметр диагностики определяет следующие параметры для этого ресурса:

- Категории журналов и данные метрик, отправляемые в назначения, определенные в параметре. Доступные категории могут различаться для разных типов ресурсов.
- Одно или несколько назначений для отправки журналов. Текущие назначения включают в себя Log Analytics рабочую область, концентраторы событий и службу хранилища Azure.
- Политика хранения данных, хранящихся в службе хранилища Azure.
 
Один параметр диагностики может определять одно из целевых объектов. Если требуется отправить данные более чем одному определенному типу назначения (например, в двух разных рабочих областях Log Analytics), создайте несколько параметров. У каждого ресурса может быть до 5 параметров диагностики.

> [!NOTE]
> Журнал действий может перенаправляться в те же назначения, что и другие журналы платформы, но не настроены с параметрами диагностики. Дополнительные сведения см. [в статье Общие сведения о журналах платформы в Azure](platform-logs-overview.md#destinations) .

> [!NOTE]
> [Метрики платформы](metrics-supported.md) собираются автоматически для [Azure Monitor метрик](data-platform-metrics.md). Параметры диагностики можно использовать для сбора метрик для определенных служб Azure в журналы Azure Monitor для анализа с другими данными мониторинга с помощью [запросов журналов](../log-query/log-query-overview.md).

## <a name="destinations"></a>Destinations 
Журналы платформы могут быть отправлены в назначения, указанные в следующей таблице. Конфигурация для каждого назначения выполняется с помощью того же процесса для создания параметров диагностики, описанных в этой статье. Чтобы получить дополнительные сведения об отправке данных в это место назначения, используйте ссылку в следующей таблице.

| Место назначения | ОПИСАНИЕ |
|:---|:---|
| [Рабочая область Log Analytics](resource-logs-collect-workspace.md) | Сбор журналов в рабочую область Log Analytics позволяет анализировать их с помощью других данных мониторинга, собираемых Azure Monitor используя мощные запросы журналов, а также использовать другие функции Azure Monitor, такие как оповещения и визуализации. |
| [Центры событий](resource-logs-stream-event-hubs.md) | Отправка журналов в концентраторы событий позволяет передавать данные в внешние системы, такие как сторонние решения Siem и другие решения log Analytics. |
| [Учетная запись хранения Azure.](resource-logs-collect-storage.md) | Архивация журналов в учетную запись хранения Azure полезна для аудита, статического анализа или резервного копирования. |


> [!IMPORTANT]
> Учетные записи Azure Data Lake Storage 2-го поколения в настоящее время не поддерживаются в качестве места назначения для параметров диагностики, хотя они могут быть указаны в портал Azure в качестве допустимого параметра.

## <a name="create-diagnostic-settings-in-azure-portal"></a>Создание параметров диагностики в портал Azure
Параметры диагностики можно настроить в портал Azure в меню Azure Monitor или в меню для ресурса.

1. В меню Azure Monitor в портал Azure щелкните **параметры диагностики** в разделе **Параметры** , а затем щелкните ресурс.

    ![Параметры диагностики](media/diagnostic-settings/menu-monitor.png)

    Или из меню ресурсов в портал Azure щелкните **параметры диагностики** в разделе **монитор**.

    ![Параметры диагностики](media/diagnostic-settings/menu-resource.png)

2. Если параметров для выбранного ресурса не существует, вам будет предложено создать параметр. Щелкните **Включить диагностику**.

   ![Добавление параметра диагностики — имеющиеся параметры отсутствуют](media/diagnostic-settings/add-setting.png)

   Если в ресурсе есть параметры, вы увидите список уже настроенных параметров. Нажмите кнопку **Добавить параметр диагностики** , чтобы добавить новый параметр или **изменить параметр** , чтобы изменить существующий. Каждый параметр может иметь не более одного из целевых типов.

   ![Добавление параметра диагностики — имеющиеся параметры](media/diagnostic-settings/edit-setting.png)

3. Присвойте параметру имя, если его еще нет.
4. Установите флажок для каждого назначения, чтобы отправить журналы. Нажмите кнопку **настроить** , чтобы указать параметры, как описано в следующей таблице.

    | Настройка | ОПИСАНИЕ |
    |:---|:---|
    | Рабочая область Log Analytics | Имя рабочей области. |
    | Учетная запись хранения | Имя учетной записи хранения. |
    | Пространство имен концентратора событий | Пространство имен, в котором создается концентратор событий (если это первый журнал потоковой передачи) или потоковая передача (если уже есть ресурсы, которые используют потоковую передачу категории журнала в это пространство имен).
    | Имя концентратора событий | При необходимости укажите имя концентратора событий для отправки всех данных в параметре. Если имя не указано, для каждой категории журнала создается концентратор событий. При отправке нескольких категорий может потребоваться указать имя, чтобы ограничить число создаваемых концентраторов событий. Дополнительные сведения см. в статье [квоты и ограничения концентраторов событий Azure](../../event-hubs/event-hubs-quotas.md) . |
    | Имя политики концентратора событий | Определяет разрешения, которые имеет механизм потоковой передачи. |

    ![Добавление параметра диагностики — имеющиеся параметры](media/diagnostic-settings/setting-details.png)

5. Установите флажок для каждой из категорий данных, отправляемых в указанные места назначения. Если вы выбрали параметр **архивации в учетную запись хранения**, потребуется также указать срок [хранения](resource-logs-collect-storage.md#data-retention).



> [!NOTE]
> Отправка многомерных метрик с помощью параметров диагностики сейчас не поддерживается. Метрики с измерениями экспортируются как преобразованные в плоскую структуру одномерные метрики, агрегированные по значениям измерений.
>
> *Пример.* Метрику "Входящие сообщения" в концентраторе событий можно изучить и вывести в виде диаграммы на уровне очереди. Но при экспорте с помощью параметров диагностики метрика будет представлена в виде всех входящих сообщений для всех очередей концентратора событий.

4. Выберите команду **Сохранить**.

Через несколько секунд новый параметр появится в списке параметров для этого ресурса, а журналы будут передаваться в указанное место назначения по мере создания данных о событиях. Обратите внимание, что между созданием события и его [отображением в log Analytics рабочей области](data-ingestion-time.md)может потребоваться до пятнадцать минут.



## <a name="create-diagnostic-settings-using-powershell"></a>Создание параметров диагностики с помощью PowerShell
Используйте командлет [Set-аздиагностиксеттинг](https://docs.microsoft.com/powershell/module/az.monitor/set-azdiagnosticsetting) , чтобы создать параметр диагностики с [Azure PowerShell](powershell-quickstart-samples.md). Описание его параметров см. в документации по этому командлету.

Ниже приведен пример командлета PowerShell для создания параметра диагностики с использованием всех трех назначений.


```powershell
Set-AzDiagnosticSetting -Name KeyVault-Diagnostics -ResourceId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault -Category AuditEvent -MetricCategory AllMetrics -Enabled $true -StorageAccountId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount -RetentionEnabled $true -RetentionInDays 7 -WorkspaceId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/myworkspace  -EventHubAuthorizationRuleId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhub/authorizationrules/RootManageSharedAccessKey
```


## <a name="create-diagnostic-settings-using-azure-cli"></a>Создание параметров диагностики с помощью Azure CLI
Используйте команду [AZ Monitor диагностики-Settings Create](https://docs.microsoft.com/cli/azure/monitor/diagnostic-settings?view=azure-cli-latest#az-monitor-diagnostic-settings-create) , чтобы создать параметр диагностики с [Azure CLI](https://docs.microsoft.com/cli/azure/monitor?view=azure-cli-latest). Описание параметров см. в документации по этой команде.

Ниже приведен пример команды CLI для создания параметра диагностики с использованием всех трех назначений.



```azurecli
az monitor diagnostic-settings create  \
--name KeyVault-Diagnostics \
--resource /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault \
--logs    '[{"category": "AuditEvent","enabled": true,"retentionPolicy": {"days": 7,"enabled": true}}]' \
--metrics '[{"category": "AllMetrics","enabled": true,"retentionPolicy": {"days": 7,"enabled": true}}]' \
--storage-account /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount \
--workspace /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/myworkspace \
--event-hub-rule /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhub/authorizationrules/RootManageSharedAccessKey
```

### <a name="configure-diagnostic-settings-using-rest-api"></a>Настройка параметров диагностики с помощью REST API
Сведения о создании или обновлении параметров диагностики с помощью [REST API Azure Monitor](https://docs.microsoft.com/rest/api/monitor/)см. в разделе [параметры диагностики](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings) .


### <a name="configure-diagnostic-settings-using-resource-manager-template"></a>Настройка параметров диагностики с помощью шаблона диспетчер ресурсов
См. раздел [Автоматическое включение параметров диагностики при создании ресурсов с помощью шаблона диспетчер ресурсов](diagnostic-settings-template.md) для создания или обновления параметров диагностики с помощью шаблона диспетчер ресурсов.

## <a name="next-steps"></a>Дополнительная информация

* [Подробнее о журналах платформы Azure](resource-logs-overview.md)