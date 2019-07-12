---
title: Создание, просмотр и управление оповещений журнала действий в Azure Monitor
description: Создание оповещений журнала действий с помощью портала Azure, шаблона Azure Resource Manager и Azure PowerShell.
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: vinagara
ms.openlocfilehash: 71e61228fcdbd52abedbc1f1205baa60b1aea923
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/09/2019
ms.locfileid: "67705293"
---
# <a name="create-view-and-manage-activity-log-alerts-by-using-azure-monitor"></a>Создание, просмотр и управление оповещений журнала действий с помощью Azure Monitor  

## <a name="overview"></a>Обзор
Оповещения журнала действий создаются в тех случаях, когда происходит новое событие, записываемое в журнал действий, которое соответствует определенным условиям для оповещения.

Эти оповещения предназначены для ресурсов Azure и могут быть созданы с помощью шаблона Azure Resource Manager. Их также можно создать, обновить или удалить на портале Azure. Как правило вы создание оповещений журнала действий для получения уведомлений при внесении определенных изменений к ресурсам в подписке Azure. Оповещения часто ограничиваются конкретные группы ресурсов или ресурсов. Например, может потребоваться получать уведомления при любой виртуальной машины в группе ресурсов образец **myProductionResourceGroup** удаляется. Или, может потребоваться получить уведомления при назначении каких-либо новых ролей пользователю в вашей подписке.

> [!IMPORTANT]
> Невозможно создать оповещения, уведомления о работоспособности службы через интерфейс для создания оповещения журнала действий. Дополнительные сведения о том, как создать и использовать уведомления о работоспособности службы, см. в разделе [оповещений журнала действий для уведомлений о работоспособности службы](alerts-activity-log-service-notifications.md).

При создании правила генерации оповещений, проверьте следующее.

- В области подписки не отличается от подписки которой создается оповещение.
- Критерии должны быть уровень, состояние, вызывающий объект, группу ресурсов, идентификатор ресурса или категории событий типа ресурса, на котором настроено оповещение.
- Нет, условие «anyOf» или вложенные условия в конфигурации оповещения JSON. По сути без дальнейшего «allOf» или «anyOf» условия допускается только одно условие «allOf».
- Если используется категория «административная», необходимо указать хотя бы один из указанных выше условий в оповещении. Вы не можете создать оповещение, которое активируется каждый раз при создании события в журналах действий.


## <a name="azure-portal"></a>Портал Azure

Портал Azure можно использовать для создания и изменения правил генерации оповещений журнала действий. Интегрирована с журнал действий Azure, чтобы обеспечить эффективное создание оповещения для определенных событий интерес.

### <a name="create-with-the-azure-portal"></a>Создание с помощью портала Azure

Используйте следующую процедуру.

1. На портале Azure выберите **монитор** > **оповещения**.
2. Выберите **новое правило генерации оповещений** в левом верхнем углу **оповещения** окна.

     ![Новое правило генерации оповещений](media/alerts-activity-log/AlertsPreviewOption.png)

     Откроется окно **создания правила**.

      ![параметры нового правила генерации оповещений](media/alerts-activity-log/create-new-alert-rule-options.png)

3. В разделе **определение условия оповещения**, укажите следующие сведения и выберите **сделать**:

   - **Целевой объект оповещения:** Чтобы просмотреть и выбрать целевой объект нового оповещения, используйте **фильтр по подписке** / **фильтр по типу ресурсов**. Выберите ресурс или группу ресурсов из отображаемого списка.

     > [!NOTE]
     > 
     > Можно выбрать ресурс, группу ресурсов или целую подписку для сигнал журнала действий.

     **Пример представления целевого объекта оповещения**

     ![Выбор цели](media/alerts-activity-log/select-target.png)

   - В разделе **целевые условия**выберите **добавить условие**. Отображаются все доступные сигналы для целевого объекта, в, в том числе из разных категорий **журнал действий**. Имя категории добавляется к **мониторинг службы** имя.

   - Выберите сигнал из отображенного списка различных операций, возможных для типа **журнал действий**.

     Вы можете выбрать временную шкалу истории журнала и соответствующую логику оповещений для целевого сигнала.

     **Снимок экрана: (Add criteria) (Добавить критерии)**

     ![Добавление условий](media/alerts-activity-log/add-criteria.png)

     - **History time** (Диапазон времени). За последние 6, 12 или 24 часа или за последнюю неделю, можно отобразить события, доступные для выбранной операции.

     - **Логика оповещений.**

       - **Уровень события**: Уровень серьезности события: Командный, _Информационный_, _Предупреждение_, _Ошибка_ или _Критический_.
       - **Состояние**. Состояние события: Запущено, _Сбой_ или _Успешно выполнено_.
       - **Кем инициировано событие**. Также называется вызывающей стороной. Адрес электронной почты или идентификатор Azure Active Directory пользователя, выполнившего операцию.

       Этот пример диаграммы сигнала имеет применением логики предупреждений:

       ![Выбранные критерии](media/alerts-activity-log/criteria-selected.png)

4. В разделе **определение сведений об оповещении**, укажите следующие сведения:

    - **Имя правила генерации оповещений**: Имя для нового правила генерации оповещений.
    - **Описание** Описание для нового правила генерации оповещений.
    - **Сохраните оповещение в группе ресурсов**: Выберите группу ресурсов, где вы хотите сохранить новое правило.

5. В разделе **Группа действий** в раскрывающемся меню укажите группу действий, которую нужно назначить новому правилу генерации оповещений. Или, [Создание новой группы действий](../../azure-monitor/platform/action-groups.md) и назначьте его к новому правилу. Чтобы создать новую группу, выберите **+ новая группа**.

6. Чтобы включить правила, после их создания, выберите **Да** для **включить правило при создании** параметр.
7. Выберите **создать правило генерации оповещений**.

    Создается новое правило генерации оповещений журнала действий, и появится сообщение с подтверждением в правом верхнем углу окна.

    Вы можете включить, отключить, изменить или удалить правило. Дополнительные сведения об управлении правил журнала действий.


Простой аналогии для понимание условий, на которых можно создать правила генерации оповещений в журнал действий — для просмотра или фильтровать события с помощью [журнала действий на портале Azure](activity-log-view.md#azure-portal). В **монитор Azure — журнал действий** экрана, можно фильтровать или найти необходимые события и затем создать оповещение с помощью **добавить оповещение журнала действий** кнопки. Затем выполните шаги с 4 по 7, поскольку ранее показано.
    
 ![Добавление оповещения от журнала действий](media/alerts-activity-log/add-activity-log.png)
    

### <a name="view-and-manage-in-the-azure-portal"></a>Просмотр и управление ими на портале Azure

1. На портале Azure выберите **монитор** > **оповещения**. Выберите **Управление правилами генерации оповещений** в левом верхнем углу окна.

    ![Управление правилами генерации оповещений](media/alerts-activity-log/manage-alert-rules.png)

    Откроется список доступных правил.

2. Поиск правила журнала действий для изменения.

    ![Поиск правил генерации оповещений журнала действий](media/alerts-activity-log/searth-activity-log-rule-to-edit.png)

    Можно использовать доступные фильтры _подписки_, _группы ресурсов_, _ресурсов_, _типа сигнала_, или _состояния_ , чтобы найти правило действия, которое требуется изменить.

   > [!NOTE]
   > 
   > Можно изменить только **описание**, **целевые условия**, и **группы действий**.

3. Выберите правило и дважды щелкните, чтобы изменить параметры. Внесите необходимые изменения, а затем выберите **Сохранить**.

   ![Управление правилами генерации оповещений](media/alerts-activity-log/activity-log-rule-edit-page.png)

4. Можно включить, отключить или удалить правило. Выберите соответствующий параметр в верхней части окна после выбора правила, как описано в шаге 2.


## <a name="azure-resource-manager-template"></a>Шаблон Azure Resource Manager
Чтобы создать оповещение журнала действий с помощью шаблона Azure Resource Manager, создайте ресурс типа `microsoft.insights/activityLogAlerts`. Затем следует заполнить все связанные свойства. Ниже приведен шаблон, создающий оповещение журнала действий.

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
Приведенный выше образец JSON можно сохранить как, например, sampleActivityLogAlert.json для целей этого пошагового руководства и может быть развернуто с помощью [Azure Resource Manager на портале Azure](../../azure-resource-manager/resource-group-template-deploy-portal.md).

> [!NOTE]
> Может занять до 5 минут для нового правила оповещения журнала действий станет активным.

## <a name="rest-api"></a>REST API 
[API оповещений журнала действий для Azure Monitor](https://docs.microsoft.com/rest/api/monitor/activitylogalerts) — это REST API. Она полностью совместима с REST API Azure Resource Manager. Его можно использовать с помощью PowerShell, используя командлет Resource Manager или Azure CLI.

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

### <a name="deploy-the-resource-manager-template-with-powershell"></a>Развертывание шаблона Resource Manager с помощью PowerShell
С помощью PowerShell, чтобы развернуть пример шаблона Resource Manager, показанный в предыдущем [шаблона Azure Resource Manager](#azure-resource-manager-template) используйте следующую команду:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "myRG" -TemplateFile sampleActivityLogAlert.json -TemplateParameterFile sampleActivityLogAlert.parameters.json
```

где sampleActivityLogAlert.parameters.json содержит значения, указанные для параметров, необходимых для создания правила генерации оповещений.

### <a name="use-activity-log-powershell-cmdlets"></a>Использование журнала действий командлеты PowerShell

Для оповещений журнала действий предусмотрены специальные командлеты PowerShell.

- [Set-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Set-AzActivityLogAlert): Создает новое оповещение журнала действий или обновляет существующие оповещения журнала действий.
- [Get-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Get-AzActivityLogAlert): Получает один или несколько действий ресурсы оповещения журнала.
- [Enable-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Enable-AzActivityLogAlert): Включает существующие оповещения журнала действий и задает его теги.
- [Disable-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Disable-AzActivityLogAlert): Отключает оповещения для существующего журнала действий и задает его теги.
- [Remove-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Remove-AzActivityLogAlert): Удаление оповещения журнала действий.

## <a name="azure-cli"></a>Инфраструктура CLI Azure

Команды Azure CLI, представленные в наборе [az monitor activity-log alert](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert), доступны для управления правилами генерации оповещений для журнала действий.

Чтобы создать новое правило генерации оповещений журнала действий, используйте следующие команды в указанном порядке:

1. [az monitor activity-log alert create](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-create). Создание нового ресурса правила генерации оповещений журнала действий.
1. [az monitor activity-log alert scope](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert/scope). Добавление области для созданного правила оповещения журнала действий.
1. [az monitor activity-log alert action-group](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert/action-group). Добавьте группу действий для правила оповещения журнала действий.

Чтобы получить один ресурс правила генерации оповещений журнала действий, используйте команду Azure CLI [show оповещения журнала активности монитора az](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-show
). Чтобы просмотреть все ресурсы для правила генерации оповещений журнала действий в группе ресурсов, используйте [список оповещений журнала активности монитора az](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-list).
Ресурсы правила генерации оповещений журнала действий можно удалить с помощью команды Azure CLI [Удаление оповещения журнала активности монитора az](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-delete).

## <a name="next-steps"></a>Следующие шаги

- Дополнительные сведения о [веб-перехватчика схемы для журналов действий](../../azure-monitor/platform/activity-log-alerts-webhook.md).
- Чтение [Обзор журналов действий](../../azure-monitor/platform/activity-log-alerts.md).
- Дополнительные сведения о группах действий см. в статье [Создание групп действий и управление ими на портале Azure](../../azure-monitor/platform/action-groups.md).  
- Дополнительные сведения об уведомлениях о работоспособности службы см. в [этой статье](../../azure-monitor/platform/service-notifications.md).
