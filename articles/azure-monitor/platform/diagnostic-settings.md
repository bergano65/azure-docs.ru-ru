---
title: Использование параметров диагностики для сбора метрик и журналов платформы в Azure
description: Отправляйте Azure Monitor метрики платформы и журналы для Azure Monitor журналов, службы хранилища Azure или концентраторов событий Azure с помощью параметра диагностики.
author: bwren
ms.author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 04/15/2020
ms.subservice: logs
ms.openlocfilehash: edb34b1456efae4d06465cfa2e64e546f621c6da
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81681191"
---
# <a name="create-diagnostic-setting-to-collect-resource-logs-and-metrics-in-azure"></a>Создание параметра диагностики для сбора журналов и метрик ресурсов в Azure

[Журналы платформы](platform-logs-overview.md) в Azure, в том числе журнал действий Azure и журналы ресурсов, предоставляют подробные сведения о диагностике и аудите для ресурсов Azure и платформы Azure, от которых они зависят. [Метрики платформы](data-platform-metrics.md) собираются по умолчанию и обычно хранятся в базе данных метрик Azure Monitor.

В этой статье приводятся сведения о создании и настройке параметров диагностики для отправки метрик платформы и журналов платформы в разные места назначения.

> [!IMPORTANT]
> Перед созданием параметра диагностики для получения журнала действий необходимо сначала отключить все устаревшие конфигурации. Дополнительные сведения см. [в статье Получение журнала действий Azure с устаревшими параметрами](diagnostic-settings-legacy.md) .

Для каждого ресурса Azure требуется собственный параметр диагностики, который определяет следующие критерии:

- Категории журналов и данных метрики, отправляемых по назначениям, указанным в параметре. Доступные категории будут отличаться для разных типов ресурсов.
- здесь указываются одно или несколько назначений для отправки журналов. Текущие назначения включают в себя рабочую область Log Analytics, Центры событий и службу хранилища Azure.

Один параметр диагностики может определять не более одного из этих назначений. Если необходимо отправить данные в места назначения нескольких типов (например, в две разные рабочие области Log Analytics), создайте несколько параметров. Каждый ресурс может иметь до 5 параметров диагностики.

> [!NOTE]
> [Метрики платформы](metrics-supported.md) собираются автоматически для [Azure Monitor метрик](data-platform-metrics.md). Параметры диагностики можно использовать для сбора метрик для определенных служб Azure в журналы Azure Monitor для анализа с другими данными мониторинга с помощью [запросов журналов](../log-query/log-query-overview.md).

## <a name="destinations"></a>Назначения

Журналы и метрики платформы могут быть отправлены в назначения, приведенные в следующей таблице. Чтобы получить дополнительные сведения об отправке данных в это место назначения, используйте ссылку в следующей таблице.

| Назначение | Описание |
|:---|:---|
| [Рабочая область Log Analytics](resource-logs-collect-workspace.md) | Сбор журналов и метрик в рабочую область Log Analytics позволяет анализировать их с помощью других данных мониторинга, собираемых Azure Monitor используя мощные запросы журналов, а также использовать другие функции Azure Monitor, такие как оповещения и визуализации. |
| [Концентраторы событий](resource-logs-stream-event-hubs.md) | Отправка журналов и метрик в концентраторы событий позволяет передавать данные в внешние системы, такие как сторонние решения Siem и другие решения log Analytics. |
| [Учетная запись хранения Azure](resource-logs-collect-storage.md) | Архивирование журналов и метрик в учетную запись хранения Azure полезно для аудита, статического анализа или резервного копирования. По сравнению с журналами Azure Monitor и Log Analytics рабочей областью Служба хранилища Azure менее затратна, а журналы могут храниться в неопределенном сроке. |

## <a name="create-diagnostic-settings-in-azure-portal"></a>Создание параметров диагностики в портал Azure

Параметры диагностики можно настроить в портал Azure в меню Azure Monitor или в меню для ресурса.

1. Настройка параметров диагностики в портал Azure зависит от ресурса.

   - Для одного ресурса щелкните **параметры диагностики** в разделе **монитор** в меню ресурса.

        ![Параметры диагностики](media/diagnostic-settings/menu-resource.png)

   - Для одного или нескольких ресурсов щелкните **параметры диагностики** в разделе **параметры** в меню Azure Monitor, а затем щелкните ресурс.

      ![Параметры диагностики](media/diagnostic-settings/menu-monitor.png)

   - Для журнала действий щелкните **Журнал действий** в меню **Azure Monitor** , а затем **параметры диагностики**. Убедитесь, что отключены все устаревшие конфигурации для журнала действий. Дополнительные сведения см. в разделе [Отключение существующих параметров](/azure/azure-monitor/platform/activity-log-collect#collecting-activity-log) .

        ![Параметры диагностики](media/diagnostic-settings/menu-activity-log.png)

2. Если параметров для выбранного ресурса не существует, вам будет предложено создать параметр. Щелкните **Добавить параметр диагностики**.

   ![Добавление параметра диагностики — имеющиеся параметры отсутствуют](media/diagnostic-settings/add-setting.png)

   Если в ресурсе есть параметры, отобразится список уже настроенных параметров. Нажмите кнопку **Добавить параметр диагностики** , чтобы добавить новый параметр или **изменить параметр** , чтобы изменить существующий. Каждый параметр может иметь не более одного из целевых типов.

   ![Добавление параметра диагностики — имеющиеся параметры](media/diagnostic-settings/edit-setting.png)

3. Присвойте параметру имя, если его еще нет.

    ![Добавить параметр диагностики](media/diagnostic-settings/setting-new-blank.png)

4. **Сведения о категории (что нужно маршрутизировать)** . Установите флажок для каждой категории данных, которые вы хотите отправить в назначения позже. Список категорий различается для каждой службы Azure.

     - **Аллметрикс** направляет метрики платформы ресурса в хранилище журналов Azure, но в форме журнала. Эти метрики обычно отправляются только в базу данных временных рядов метрик Azure Monitor. Их можно отправить в хранилище журналов Azure Monitor (которое можно найти с помощью Log Analytics), чтобы интегрировать их в запросы, которые выполняют поиск по другим журналам. Этот параметр может быть недоступен для всех типов ресурсов. Если она поддерживается, [Azure Monitor поддерживаемые метрики](metrics-supported.md) указывают, какие метрики собираются для типов ресурсов.

       > [!NOTE]
       > Отправка многомерных метрик с помощью параметров диагностики сейчас не поддерживается. Метрики с измерениями экспортируются как преобразованные в плоскую структуру одномерные метрики, агрегированные по значениям измерений.
       >
       > *Например*: метрику "иореадбитес" на блокчейн можно исследовать и распланировать на уровне каждого узла. Однако при экспорте с помощью параметров диагностики метрика экспорта представляется как все считанные байты для всех узлов.

     - В **журналах** перечислены различные категории, доступные в зависимости от типа ресурса. Проверьте все категории, которые вы хотите перенаправить в назначение.

5. **Сведения о назначении** . Установите флажок для каждого назначения. При установке каждого флажка отображаются параметры, позволяющие добавлять дополнительные сведения.

      ![Отправка в Log Analytics или концентраторы событий](media/diagnostic-settings/send-to-log-analytics-event-hubs.png)

    1. **Log Analytics** введите подписку и рабочую область.  Если у вас нет рабочей области, необходимо создать ее, [прежде чем продолжать работу](../learn/quick-create-workspace.md).

    1. **Концентраторы событий** — укажите следующие критерии.
       - Подписка, частью которой является концентратор событий
       - Пространство имен концентратора событий. Если вы еще не создали его, необходимо [создать его](../../event-hubs/event-hubs-create.md) .
       - Имя концентратора событий (необязательно) для отправки всех данных. Если имя не указано, для каждой категории журнала создается концентратор событий. При отправке нескольких категорий может потребоваться указать имя, чтобы ограничить число создаваемых концентраторов событий. Дополнительные сведения см. в статье [квоты и ограничения концентраторов событий Azure](../../event-hubs/event-hubs-quotas.md) .
       - Политика концентратора событий (необязательно) определяет разрешения, которые использует механизм потоковой передачи. Дополнительные сведения см. в разделе [event-Hubs-Features](../../event-hubs/event-hubs-features.md#publisher-policy).

    1. **Хранилище** — выберите подписку, учетную запись хранения и политику хранения.

        ![Отправить в хранилище](media/diagnostic-settings/storage-settings-new.png)

        > [!TIP]
        > Попробуйте задать для политики хранения значение 0 и вручную удалить данные из хранилища с помощью запланированного задания, чтобы избежать возможной путаницы в будущем.
        >
        > Во – первых, если вы используете хранилище для архивирования, обычно требуется, чтобы данные сохранятся более 365 дней. Во-вторых, при выборе политики хранения, которая больше 0, Дата окончания срока действия прикрепляется к журналам во время хранения. Вы не можете изменить дату этих журналов после их сохранения.
        >
        > Например, если установить политику хранения для *WorkflowRuntime* в 180 дней, а затем на 24 часа позднее задать значение 365 дней, журналы, хранящиеся в течение первых 24 часов, будут автоматически удалены через 180 дней, а все последующие журналы этого типа будут автоматически удалены по истечении 365 дней. Изменение политики хранения в дальнейшем не приводит к появления первых 24 часов журналов в течение 365 дней.

6. Нажмите кнопку **Сохранить**.

Через несколько секунд новый параметр появится в списке параметров для этого ресурса, а журналы будут передаваться в указанное место назначения по мере создания данных о событиях. При порождении события и его [появлении в log Analytics рабочей области](data-ingestion-time.md)может потребоваться до 15 минут.

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

## <a name="next-steps"></a>Дальнейшие шаги

- [Подробнее о журналах платформы Azure](platform-logs-overview.md)
