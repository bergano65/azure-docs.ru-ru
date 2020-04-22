---
title: Используйте диагностические настройки для сбора метрик платформи и журналов и в Azure
description: Отправка метрик и журналов платформы Azure Monitor в журналы Azure Monitor, хранилище Azure или концентраторы событий Azure с помощью диагностических настроек.
author: bwren
ms.author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 04/15/2020
ms.subservice: logs
ms.openlocfilehash: edb34b1456efae4d06465cfa2e64e546f621c6da
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81681191"
---
# <a name="create-diagnostic-setting-to-collect-resource-logs-and-metrics-in-azure"></a>Создание диагностических параметров для сбора журналов ресурсов и метрик в Azure

[Платформы в](platform-logs-overview.md) Azure, включая журналы деятельности Azure и журналы ресурсов, предоставляют подробную информацию о диагностике и аудите для ресурсов Azure и платформы Azure, от которой они зависят. [Метрики платформы](data-platform-metrics.md) собираются по умолчанию и обычно хранятся в базе данных метрик Azure Monitor.

В этой статье приводится подробная информация о создании и настройке диагностических настроек для отправки метрик платформы и журналов платформ в различные пункты назначения.

> [!IMPORTANT]
> Перед созданием диагностической настройки для сбора журнала activity следует сначала отключить любую устаревшую конфигурацию. Просмотрите [журнал «Сбор azure Activity» с устаревшими настройками](diagnostic-settings-legacy.md) для получения подробной информации.

Каждый ресурс Azure требует собственной диагностической настройки, которая определяет следующие критерии:

- Категории журналов и данных метрики, отправляемых по назначениям, указанным в параметре. Доступные категории будут отличаться для разных типов ресурсов.
- здесь указываются одно или несколько назначений для отправки журналов. Текущие назначения включают в себя рабочую область Log Analytics, Центры событий и службу хранилища Azure.

Один диагностический параметр может определить не более одного из пунктов назначения. Если необходимо отправить данные в места назначения нескольких типов (например, в две разные рабочие области Log Analytics), создайте несколько параметров. Каждый ресурс может иметь до 5 параметров диагностики.

> [!NOTE]
> [Метрики платформы](metrics-supported.md) собираются автоматически в [Azure Monitor Metrics.](data-platform-metrics.md) Диагностические настройки могут использоваться для сбора метрик для определенных служб Azure в журналы Azure Monitor для анализа с другими данными мониторинга с помощью [журналов.](../log-query/log-query-overview.md)

## <a name="destinations"></a>Назначения

Платформные журналы и метрики могут быть отправлены в пункты назначения в следующей таблице. Перейти к каждой ссылке в следующей таблице для получения подробной информации об отправке данных в этот пункт назначения.

| Назначение | Описание |
|:---|:---|
| [Рабочая область Log Analytics](resource-logs-collect-workspace.md) | Сбор журналов и метрик в рабочее пространство Log Analytics позволяет анализировать их с помощью других данных мониторинга, собранных Azure Monitor, с помощью мощных запросов журналов, а также использовать другие функции Azure Monitor, такие как оповещения и визуализации. |
| [Центры событий](resource-logs-stream-event-hubs.md) | Отправка журналов и метрик в концентраторы событий позволяет передавать данные на внешние системы, такие как сторонние SIEMs и другие решения для анализа журналов. |
| [Учетная запись хранения Azure](resource-logs-collect-storage.md) | Архивирование журналов и метрик в учетной записи хранения Azure полезно для аудита, статического анализа или резервного копирования. По сравнению с журналами Azure Monitor и рабочим пространством для анализа журналов, хранилище Azure обходится дешевле, и журналы могут храниться там бесконечно. |

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

   При наличии существующих параметров на ресурсе можно увидеть список уже настроенных настроек. Либо нажмите **«Добавить диагностическую настройку»,** чтобы добавить новую настройку, или **отсеивайте настройки** для отсвасывания существующей. Каждый параметр может иметь не более одного из каждого типа назначения.

   ![Добавление параметра диагностики — имеющиеся параметры](media/diagnostic-settings/edit-setting.png)

3. Дайте параметру имя, если оно еще не имеет.

    ![Добавление диагностических параметров](media/diagnostic-settings/setting-new-blank.png)

4. **Подробности категории (что на маршрут)** - Проверьте поле для каждой категории данных, которые вы хотите отправить в пункты назначения, указанные позже. Список категорий варьируется для каждой службы Azure.

     - **AllMetrics** направляет метрики платформы ресурса в хранилище журналов Azure, но в виде журнала. Эти метрики обычно отправляются только в базу данных временных рядов измерений Azure Monitor. Отправка их в магазин журналов Azure Monitor Logs (который можно найти через Log Analytics), чтобы интегрировать их в запросы, которые ищут в других журналах. Эта опция может быть доступна не для всех типов ресурсов. При поддержке [мониторинга Azure Monitor перечисляет,](metrics-supported.md) какие метрики собираются для каких типов ресурсов.

       > [!NOTE]
       > Отправка многомерных метрик с помощью параметров диагностики сейчас не поддерживается. Метрики с измерениями экспортируются как преобразованные в плоскую структуру одномерные метрики, агрегированные по значениям измерений.
       >
       > *Например:* метрика 'IOReadBytes' на блокчейне может быть изучена и наметиться на уровне одного узла. Однако при экспорте через диагностические настройки экспортируемая метрика представляет собой как все прочитанные байты для всех узлов.

     - **В журналах перечислены** различные категории, доступные в зависимости от типа ресурса. Проверьте все категории, которые вы хотели бы направить к месту назначения.

5. **Детали назначения** - Проверьте поле для каждого пункта назначения. При проверке каждой коробки, как представляется, параметры позволяют добавлять дополнительную информацию.

      ![Отправка в журнал Analytics или концентраторы событий](media/diagnostic-settings/send-to-log-analytics-event-hubs.png)

    1. **Аналитика журнала** - Введите подписку и рабочее пространство.  Если у вас нет рабочего пространства, необходимо [создать ее перед началом работы.](../learn/quick-create-workspace.md)

    1. **Центры событий** - Укажите следующие критерии:
       - Подписка, частью которой является центр событий
       - Пространство имен концентратора событий - Если у вас его еще нет, вам нужно [создать его](../../event-hubs/event-hubs-create.md)
       - Имя концентратора событий (необязательно) для отправки всех данных. Если не указано имя, для каждой категории журнала создается концентратор событий. Если вы отправляете несколько категорий, можно указать имя, чтобы ограничить количество созданных концентраторов событий. Ознакомьтесь с [квотами и лимитами концентратов событий Azure.](../../event-hubs/event-hubs-quotas.md)
       - Политика концентратора событий (необязательно) Политика определяет разрешения, которыми обладает механизм потоковой передачи. Для получения дополнительной [информации](../../event-hubs/event-hubs-features.md#publisher-policy)см.

    1. **Хранение** - Выберите подписку, учетную запись хранения и политику удержания.

        ![Отправить на хранение](media/diagnostic-settings/storage-settings-new.png)

        > [!TIP]
        > Рассмотрите возможность установки политики удержания до 0 и ручного удаляния данных из хранилища с помощью запланированного задания, чтобы избежать возможной путаницы в будущем.
        >
        > Во-первых, если вы используете хранилище для архивирования, вы обычно хотите, чтобы ваши данные вокруг более 365 дней. Во-вторых, если вы выбираете политику удержания, превышающее 0, дата истечения срока действия прилагается к журналам во время хранения. Вы не можете изменить дату для этих журналов после хранения.
        >
        > Например, если вы установите политику удержания *для WorkflowRuntime* до 180 дней, а затем через 24 часа установите ее до 365 дней, журналы, хранящиеся в течение этих первых 24 часов, будут автоматически удалены через 180 дней, в то время как все последующие журналы этого типа будут автоматически удалены через 365 дней. Изменение политики удержания позже не делает первые 24 часа журналов оставаться вокруг в течение 365 дней.

6. Выберите команду **Сохранить**.

Через несколько мгновений новая настройка отображается в списке настроек для этого ресурса, и журналы передаются в указанные пункты назначения по мере создания новых данных событий. Это может занять до 15 минут между тем, когда событие излучается и когда он [появляется в рабочей области журнала Analytics](data-ingestion-time.md).

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

- [Узнайте больше о журналах платформы Azure](platform-logs-overview.md)
