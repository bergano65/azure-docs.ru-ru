---
title: Создание, просмотр и управление действие оповещения журнала в Azure Monitor
description: Как создание оповещений журнала действий с помощью портала Azure, шаблона Azure Resource Manager и Azure PowerShell.
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/15/2018
ms.author: vinagara
ms.openlocfilehash: f25321fa5a13ed5a39a62a4115bb0bc10306d36f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66244958"
---
# <a name="create-view-and-manage-activity-log-alerts-using-azure-monitor"></a>Создание, просмотр и управление оповещениями журнала действий с помощью Azure Monitor  

## <a name="overview"></a>Обзор
Оповещения журнала действий создаются в тех случаях, когда происходит новое событие, записываемое в журнал действий, которое соответствует определенным условиям для оповещения.

Эти оповещения предназначены для ресурсов Azure, их можно создать с помощью шаблона Azure Resource Manager. Их также можно создать, обновить или удалить на портале Azure. Обычно вы создаете оповещения журнала действия, чтобы получать уведомления при внесении определенных изменений в ресурсы в вашей подписки Azure. Как правило, это конкретные группы ресурсов или ресурсы. Например, можно получать уведомления при удалении любой виртуальной машины в **myProductionResourceGroup** (пример группы ресурсов). Или может потребоваться получать уведомления при назначении каких-либо новых ролей пользователю в вашей подписке.

> [!IMPORTANT]
> Оповещения уведомлений о работоспособности службы невозможно создавать через интерфейс для создания оповещений журнала действий. Дополнительные сведения о создании и использовании уведомлений о работоспособности службы см. в статье [Создание оповещений журнала действий для уведомлений службы](alerts-activity-log-service-notifications.md).

## <a name="azure-portal"></a>Портал Azure

> [!NOTE]
> 
>  При создании правил генерации оповещений проверьте следующее.
> 
> - Подписка в области ничем не отличается от подписки, в которой создается оповещение.
> - Условия должны соответствовать уровню, состоянию, вызывающему, группе ресурсов, идентификатору ресурса, типу ресурса или категории события, на основе которых настраивается оповещение.
> - Условие anyOf и вложенные условия отсутствуют в JSON-файле конфигурации оповещений (обычно разрешается только одно условие allOf без allOf и anyOf).
> - Для категории "административная". В оповещении необходимо указать хотя бы одно из описанных выше условий. Вы не можете создать оповещение, которое активируется каждый раз при создании события в журналах действий.

### <a name="create-with-azure-portal"></a>Создание с помощью портала Azure

Выполните перечисленные ниже действия.

1. На портале Azure выберите **Монитор** > **Оповещения**.
2. Выберите **Новое правило генерации оповещений** в верхней части окна **Оповещения**.

     ![Новое правило генерации оповещений](media/alerts-activity-log/AlertsPreviewOption.png)

     Откроется окно **создания правила**.

      ![Параметры нового правила генерации оповещений](media/alerts-activity-log/create-new-alert-rule-options.png)

3. В разделе **Define Alert condition** (Определите условия оповещения) введите указанные ниже сведения и выберите **Готово**.

   - **Целевой объект оповещения.** Чтобы просмотреть и выбрать целевой объект нового оповещения, воспользуйтесь параметром **Фильтр по подписке** / **Фильтр по типу ресурсов** и выберите ресурс или группу ресурсов из отображаемого списка.

     > [!NOTE]
     > 
     > Вы можете выбрать ресурс, группу ресурсов или целую подписку в качестве сигнала журнала действий.

     **Пример представления целевого объекта оповещений**
     ![Выбор цели](media/alerts-activity-log/select-target.png)

   - В разделе **целевые условия**, нажмите кнопку **добавить условие** и все доступные сигналы для целевого объекта отображаются в том числе из разных категорий **журнал действий**; из-за Имя категории добавляются в **мониторинг службы** имя.

   - Выберите в списке, отображаются в различных операций, возможных для типа сигнала **журнал действий**.

     Вы можете выбрать временную шкалу истории журнала и соответствующую логику оповещений для целевого сигнала.

     **Снимок экрана: (Add criteria) (Добавить критерии)**

     ![Добавить критерии](media/alerts-activity-log/add-criteria.png)

     **History time** (Диапазон времени). События, доступные для выбранной операции, могут быть отображены в графике за последние 6/12/24 часа или за последнюю неделю.

     **Логика оповещений.**

     - **Уровень события**. Уровень серьезности события. _Командный_, _Информационный_, _Предупреждение_, _Ошибка_ или _Критический_.
     - **Состояние**. Состояние события. _Запущено_, _Сбой_ или _Успешно выполнено_.
     - **Кем инициировано событие**. Также называется вызывающей стороной. Здесь указывается адрес электронной почты или идентификатор Azure Active Directory пользователя, выполнившего операцию.

       Пример диаграммы сигнала с применением логики предупреждений:

       ![ выбранные критерии](media/alerts-activity-log/criteria-selected.png)

4. В разделе **define alert rules details** (Определение сведений о правилах генерации оповещений) укажите следующие сведения.

    - **Имя правила генерации оповещений** — имя нового правила генерации оповещений.
    - **Описание** — описание нового рабочего правила генерации оповещений.
    - **Save alert to resource group** (Сохранить оповещение в группе ресурсов) — выберите группу ресурсов, в которой вы хотите сохранить новое правило.

5. В разделе **Группа действий** в раскрывающемся меню укажите группу действий, которую нужно назначить новому правилу генерации оповещений. Кроме того, [создайте группу действий](../../azure-monitor/platform/action-groups.md) и назначьте ее новому правилу. Чтобы создать группу, выберите **+ Создать группу**.

6. Чтобы включить правило после ее создания, выберите **Да** для параметра **Включить правило при создании**.
7. Выберите **Создать правило генерации оповещений**.

    Будет создано правило генерации оповещений для журнала действий и отобразится сообщение с подтверждением в правом верхнем углу окна.

    Вы можете включить, отключить, изменить или удалить правило. Узнайте больше об управлении правилами журнала действий.


Кроме того, является простой аналогии для понимание условий, на которых могут создаваться правила генерации оповещений для журнала действий, для просмотра или фильтровать события с помощью [журнала действий на портале Azure](activity-log-view.md#azure-portal). В Azure Monitor — журнал действий один можно фильтровать или найти необходимые события и затем создать оповещение с помощью **добавить оповещение журнала действий** кнопку; затем выполните шаги 4 и более поздних версий как указано в руководстве описано выше.
    
 ![ Добавление оповещения из журнала действий](media/alerts-activity-log/add-activity-log.png)
    

### <a name="view-and-manage-in-azure-portal"></a>Просмотр и управление на портале Azure

1. На портале Azure выберите **Монитор** > **Оповещения** и щелкните **Управление правилами** в верхнем левом углу окна.

    ![ управление правилами генерации оповещений](media/alerts-activity-log/manage-alert-rules.png)

    Откроется список доступных правил.

2. Выполните поиск правила журнала действий, которое необходимо изменить.

    ![ Поиск правил генерации оповещений журнала действий](media/alerts-activity-log/searth-activity-log-rule-to-edit.png)

    Вы можете использовать доступные фильтры: _Подписка_, _Группа ресурсов_, _Ресурс_, _Тип сигнала_ или _Состояние_, чтобы найти действующее правило, которое необходимо изменить.

   > [!NOTE]
   > 
   > Изменить можно только **описание**, **целевые критерии** и **группы действий**.

3. Выберите правило и дважды щелкните его, чтобы изменить параметры. Внесите необходимые изменения и нажмите кнопку **Сохранить**.

   ![ управление правилами генерации оповещений](media/alerts-activity-log/activity-log-rule-edit-page.png)

4. Вы можете отключить, включить или удалить правило. Выберите соответствующий параметр в верхней части окна после выбора правила, как описано на шаге 2.


## <a name="azure-resource-template"></a>Шаблон ресурсов Azure
Чтобы создать оповещение журнала действий с помощью шаблона Resource Manager, создайте ресурс типа `microsoft.insights/activityLogAlerts`. Затем следует заполнить все связанные свойства. Вот шаблон, создающий оповещение журнала действий.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "activityLogAlertName": {
      "type": "string",
      "metadata": {
        "description": "Unique name (within the Resource Group) for the Activity log alert."
      }
    },
    "activityLogAlertEnabled": {
      "type": "bool",
      "defaultValue": true,
      "metadata": {
        "description": "Indicates whether or not the alert is enabled."
      }
    },
    "actionGroupResourceId": {
      "type": "string",
      "metadata": {
        "description": "Resource Id for the Action group."
      }
    }
  },
  "resources": [   
    {
      "type": "Microsoft.Insights/activityLogAlerts",
      "apiVersion": "2017-04-01",
      "name": "[parameters('activityLogAlertName')]",      
      "location": "Global",
      "properties": {
        "enabled": "[parameters('activityLogAlertEnabled')]",
        "scopes": [
            "[subscription().id]"
        ],        
        "condition": {
          "allOf": [
            {
              "field": "category",
              "equals": "Administrative"
            },
            {
              "field": "operationName",
              "equals": "Microsoft.Resources/deployments/write"
            },
            {
              "field": "resourceType",
              "equals": "Microsoft.Resources/deployments"
            }
          ]
        },
        "actions": {
          "actionGroups":
          [
            {
              "actionGroupId": "[parameters('actionGroupResourceId')]"
            }
          ]
        }
      }
    }
  ]
}
```
Приведенный выше пример JSON можно сохранить как sampleActivityLogAlert.json, чтобы использовать его в данном пошаговом руководстве. Его можно развернуть с помощью [Azure Resource Manager на портале Azure](../../azure-resource-manager/resource-group-template-deploy-portal.md).

> [!NOTE]
> Перед тем как правило генерации оповещений журнала действий станет активным, может пройти до 5 минут.

## <a name="rest-api"></a>REST API 
[Azure Monitor — журнал действий оповещения API](https://docs.microsoft.com/rest/api/monitor/activitylogalerts) является полностью совместимы с REST API Azure Resource Manager и REST API. Поэтому он может использоваться в PowerShell с помощью командлета Resource Manager, а также в Azure CLI.

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

### <a name="deploy-resource-manager-template-with-powershell"></a>Развертывание шаблона Resource Manager с помощью PowerShell
С помощью PowerShell, чтобы развернуть пример шаблона ресурсов, показанной в предыдущем [шаблон ресурсов] (#resource-manager шаблона, используйте следующую команду:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "myRG" -TemplateFile sampleActivityLogAlert.json -TemplateParameterFile sampleActivityLogAlert.parameters.json
```

где sampleActivityLogAlert.parameters.json содержит значения, указанные для параметров, необходимых для создания правила генерации оповещений.

### <a name="use-activity-log-powershell-cmdlets"></a>Использование журнала действий командлеты PowerShell

Для оповещений журнала действий предусмотрены специальные командлеты PowerShell.

- [SET-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Set-AzActivityLogAlert?view=azps-1.3.0) : Создает новый или обновить существующие оповещения журнала действий.
- [Get-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Get-AzActivityLogAlert?view=azps-1.3.0) : Получает один или несколько действий ресурсы оповещения журнала.
- [Enable-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Enable-AzActivityLogAlert?view=azps-1.3.0) : Включает существующие оповещения журнала действий и задает его теги.
- [Disable-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Disable-AzActivityLogAlert?view=azps-1.3.0) : Отключает оповещения для существующего журнала действий и задает его теги.
- [Remove-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Remove-AzActivityLogAlert?view=azps-1.3.0)    : Удаление оповещения журнала действий.

## <a name="cli"></a>Интерфейс командной строки

Команды Azure CLI, представленные в наборе [az monitor activity-log alert](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert), доступны для управления правилами генерации оповещений для журнала действий.

Чтобы создать новое правило генерации оповещений для журнала действий, вы можете выполнить действия в следующем порядке.

1. [az monitor activity-log alert create](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-create). Создание нового ресурса правила генерации оповещений для журнала действий.
1. [az monitor activity-log alert scope](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert/scope). Добавление области для созданного правила генерации оповещений для журнала действий.
1. [az monitor activity-log alert action-group](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert/action-group). Добавление группы действий для правила генерации оповещений для журнала действий.

Для получения одного ресурса правила генерации оповещений для журнала действий, можете использовать команду Azure CLI [az monitor activity-log alert show](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-show
). Чтобы просмотреть все ресурсы правила генерации оповещений для журнала активности в группе ресурсов используйте команду [az monitor activity-log alert list](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-list).
Для удаления ресурсов правила генерации оповещений для журнала действий, можете использовать команду Azure CLI [az monitor activity-log alert delete](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-delete).

## <a name="next-steps"></a>Дальнейшие действия

- [Веб-перехватчики для оповещений журнала действий Azure](../../azure-monitor/platform/activity-log-alerts-webhook.md)
- [Создание оповещений журнала действий (классических)](../../azure-monitor/platform/activity-log-alerts.md) 
- Дополнительные сведения о группах действий см. в статье [Создание групп действий и управление ими на портале Azure](../../azure-monitor/platform/action-groups.md).  
- Дополнительные сведения об уведомлениях о работоспособности службы см. в [этой статье](../../azure-monitor/platform/service-notifications.md).
