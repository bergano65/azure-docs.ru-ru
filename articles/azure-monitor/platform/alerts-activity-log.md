---
title: Создание, просмотр и Управление оповещениями журнала действий в Azure Monitor
description: Создавайте оповещения журнала действий с помощью портал Azure, шаблона Azure Resource Manager и Azure PowerShell.
ms.topic: conceptual
ms.subservice: alerts
ms.date: 06/25/2019
ms.openlocfilehash: bfbe2bc3ae3edf9285d3ec006ab0451f070cabd6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80132391"
---
# <a name="create-view-and-manage-activity-log-alerts-by-using-azure-monitor"></a>Создание, просмотр и Управление оповещениями журнала действий с помощью Azure Monitor  

## <a name="overview"></a>Обзор

Оповещения журнала действий создаются в тех случаях, когда происходит новое событие, записываемое в журнал действий, которое соответствует определенным условиям для оповещения.

Эти оповещения предназначены для ресурсов Azure и могут быть созданы с помощью шаблона Azure Resource Manager. Их также можно создать, обновить или удалить на портале Azure. Как правило, оповещения журнала действий создаются для получения уведомлений при возникновении конкретных изменений в ресурсах в подписке Azure. Оповещения часто ограничены определенными группами ресурсов или ресурсами. Например, вы можете получать уведомления при удалении любой виртуальной машины из образца группы ресурсов **группе ресурсов myproductionresourcegroup** . Или вы можете получать уведомления о назначении новых ролей пользователю в вашей подписке.

> [!IMPORTANT]
> Оповещения о работоспособности службы не могут быть созданы через интерфейс для создания оповещений журнала действий. Дополнительные сведения о создании и использовании уведомлений о работоспособности служб см. в статье [Получение оповещений журнала действий для уведомлений о работоспособности служб](alerts-activity-log-service-notifications.md).

При создании правил генерации оповещений убедитесь в следующем:

- Подписка в области не отличается от подписки, в которой создано оповещение.
- Критерий должен быть категорией "уровень", "состояние", "вызывающий объект", "Группа ресурсов", "идентификатор ресурса" или "тип ресурса", для которого настроено предупреждение.
- В JSON конфигурации предупреждения отсутствует условие "anyOf" или вложенные условия. По сути, только одно условие "allOf" допускается без дополнительных условий "allOf" или "anyOf".
- Если категория имеет значение "Администратор", необходимо указать по крайней мере одно из указанных выше условий в предупреждении. Вы не можете создать оповещение, которое активируется каждый раз при создании события в журналах действий.

## <a name="azure-portal"></a>Портал Azure

Для создания и изменения правил генерации оповещений журнала действий можно использовать портал Azure. Взаимодействие интегрировано с журналом действий Azure, чтобы обеспечить простое создание оповещений для конкретных интересующих вас событий.

### <a name="create-with-the-azure-portal"></a>Создание с помощью портал Azure

Выполните следующую процедуру.

1. В портал Azure выберите **монитор** > **оповещения**.
2. Выберите **новое правило генерации оповещений** в левом верхнем углу окна **оповещения** .

     ![Новое правило генерации оповещений](media/alerts-activity-log/AlertsPreviewOption.png)

     Откроется окно **создания правила**.

      ![Новые параметры правила генерации оповещений](media/alerts-activity-log/create-new-alert-rule-options.png)

3. В разделе **Определение условия оповещения**укажите следующие сведения и нажмите кнопку **Готово**.

   - **Цель оповещения:** Чтобы просмотреть и выбрать целевой объект для нового оповещения, используйте фильтр по**типу ресурса** **Фильтровать по подписке** / . Выберите ресурс или группу ресурсов из отображаемого списка.

     > [!NOTE]
     > 
     > Для сигнала журнала действий можно выбрать только [Azure Resource Manager](../../azure-resource-manager/management/overview.md) отслеживаемый ресурс, группу ресурсов или всю подписку. 

     **Пример представления целевого объекта оповещения**

     ![Выбор цели](media/alerts-activity-log/select-target.png)

   - В разделе **целевые критерии**выберите **Добавить условие**. Отображаются все доступные сигналы для целевого объекта, которые включают в себя различные категории **журнала действий**. Имя категории добавляется к имени **службы мониторинга** .

   - Выберите сигнал из отображенного списка различных операций, возможных для типа **журнал действий**.

     Вы можете выбрать временную шкалу истории журнала и соответствующую логику оповещений для целевого сигнала.

     **Снимок экрана: (Add criteria) (Добавить критерии)**

     ![Добавление условий](media/alerts-activity-log/add-criteria.png)

     - **Время**записи в журнал: события, доступные для выбранной операции, могут отображаться за последние 6, 12 или 24 часа или за последнюю неделю.

     - **Логика оповещения**:

       - **Уровень события**: уровень серьезности события: _verbose_, _информационное_, _предупреждение_, _Ошибка_или _критическое_.
       - **Состояние**: состояние события: " _запущено_", " _сбой_" или " _успешно_".
       - **Событие, инициированное**: также называется вызывающей стороной. Адрес электронной почты или идентификатор Azure Active Directory пользователя, выполнившего операцию.

       К этому образцу графа сигналов применена логика оповещений:

       ![Выбранные критерии](media/alerts-activity-log/criteria-selected.png)

4. В разделе **Определение сведений о предупреждении**укажите следующие сведения.

    - **Имя правила генерации оповещений**: имя нового правила генерации оповещений.
    - **Описание**: описание нового правила генерации оповещений.
    - **Сохранить оповещение в группу ресурсов**. Выберите группу ресурсов, в которой необходимо сохранить это новое правило.

5. В разделе **Группа действий** в раскрывающемся меню укажите группу действий, которую нужно назначить новому правилу генерации оповещений. Или [Создайте новую группу действий](../../azure-monitor/platform/action-groups.md) и назначьте ее новому правилу. Чтобы создать новую группу, выберите **+ Новая группа**.

6. Чтобы включить правила после их создания, выберите **Да** для параметра **включить правило при создании** .
7. Выберите **Создать правило генерации оповещений**.

    Будет создано новое правило генерации оповещений для журнала действий, а в правом верхнем углу окна появится сообщение с подтверждением.

    Вы можете включить, отключить, изменить или удалить правило. Дополнительные сведения об управлении правилами журнала действий.


Простая аналогия для понимания условий, в которых можно создать правила генерации оповещений в журнале действий, является изучение или Фильтрация событий с помощью [журнала действий в портал Azure](activity-log-view.md#azure-portal). На экране **Azure Monitor — журнал действий** можно отфильтровать или найти необходимое событие, а затем создать оповещение с помощью кнопки **Добавить оповещение журнала действий** . Затем выполните шаги с 4 по 7, как показано выше.
    
 ![Добавить оповещение из журнала действий](media/alerts-activity-log/add-activity-log.png)
    

### <a name="view-and-manage-in-the-azure-portal"></a>Просмотр портал Azure и управление ими

1. В портал Azure выберите **монитор** > **оповещения**. Выберите **Управление правилами оповещений** в левом верхнем углу окна.

    ![Управление правилами генерации оповещений](media/alerts-activity-log/manage-alert-rules.png)

    Откроется список доступных правил.

2. Найдите правило журнала действий, которое необходимо изменить.

    ![Поиск правил оповещений журнала действий](media/alerts-activity-log/searth-activity-log-rule-to-edit.png)

    Для поиска правила действия, которое требуется изменить, можно использовать доступные фильтры, _подписку_, _группу ресурсов_, _ресурс_, _Тип сигнала_или _состояние_.

   > [!NOTE]
   > 
   > Можно изменять только **Описание**, **целевые критерии**и **группы действий**.

3. Выберите правило и дважды щелкните его, чтобы изменить параметры правила. Внесите необходимые изменения и нажмите кнопку **сохранить**.

   ![Управление правилами генерации оповещений](media/alerts-activity-log/activity-log-rule-edit-page.png)

4. Вы можете включить, отключить или удалить правило. Выберите соответствующий параметр в верхней части окна после выбора правила, как описано в шаге 2.


## <a name="azure-resource-manager-template"></a>Шаблон Azure Resource Manager
Чтобы создать правило генерации оповещений журнала действий с помощью шаблона Azure Resource Manager, создайте ресурс типа `microsoft.insights/activityLogAlerts`. Затем следует заполнить все связанные свойства. Ниже приведен шаблон, создающий правило оповещения журнала действий.

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
Предыдущий пример JSON можно сохранить как, например, в Самплеактивитилогалерт. JSON, в рамках этого пошагового руководства, и его можно развернуть с помощью [Azure Resource Manager в портал Azure](../../azure-resource-manager/templates/deploy-portal.md).

Следующие поля — это параметры, которые можно использовать в шаблоне Azure Resource Manager для полей условий: Обратите внимание, что "Работоспособность ресурсов", "Advisor" и "работоспособность службы" имеют дополнительные поля свойств для своих особых полей. 
1. resourceId — идентификатор ресурса затронутого ресурса в событии журнала действий, в котором должно создаваться предупреждение.
2. Категория: Категория в событии журнала действий. Например: административные, ServiceHealth, Ресаурцехеалс, Автомасштабирование, безопасность, рекомендация, политика.
3. вызывающий: адрес электронной почты или Azure Active Directory идентификатор пользователя, выполнившего операцию события журнала действий.
4. уровень: уровень действия в событии журнала действий, по которому должно создаваться предупреждение. Например: критическая, ошибка, предупреждение, информационное, подробное.
5. Имя_операции: имя операции в событии журнала действий. Например: Microsoft. Resources/deployments/Write
6. resourceGroup: имя группы ресурсов для затронутого ресурса в событии журнала действий.
7. resourceProvider. [Описание поставщиков ресурсов и типов Azure](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fazure-resource-manager%2Fmanagement%2Fresource-providers-and-types&data=02%7C01%7CNoga.Lavi%40microsoft.com%7C90b7c2308c0647c0347908d7c9a2918d%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637199572373543634&sdata=4RjpTkO5jsdOgPdt%2F%2FDOlYjIFE2%2B%2BuoHq5%2F7lHpCwQw%3D&reserved=0). Список поставщиков ресурсов для служб Azure см. в статье [поставщики ресурсов для служб Azure](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fazure-resource-manager%2Fmanagement%2Fazure-services-resource-providers&data=02%7C01%7CNoga.Lavi%40microsoft.com%7C90b7c2308c0647c0347908d7c9a2918d%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637199572373553639&sdata=0ZgJPK7BYuJsRifBKFytqphMOxMrkfkEwDqgVH1g8lw%3D&reserved=0).
8. Status: строка, описывающая состояние операции в событии действия. Например: запущено, выполняется, успешно, не пройдено, активно, разрешено
9. подсостояние: обычно код состояния HTTP соответствующего вызова RESTFUL, но также может включать другие строки, описывающие подсостояние.   Например: ОК (код состояния HTTP: 200), создано (код состояния HTTP: 201), принято (код состояния HTTP: 202), нет содержимого (код состояния HTTP: 204), неверный запрос (код состояния HTTP: 400), не найден (код состояния HTTP: 404), конфликт (код состояния HTTP: 409), внутренняя ошибка сервера (код состояния HTTP: 500), служба недоступна (код состояния HTTP: 503), время ожидания шлюза (код состояния HTTP: 504).
10. resourceType — тип ресурса, затронутого событием. Например: Microsoft. Resources/deployments

Пример:

```json
"condition": {
          "allOf": [
            {
              "field": "category",
              "equals": "Administrative"
            },
            {
              "field": "resourceType",
              "equals": "Microsoft.Resources/deployments"
            }
          ]
        }

```
Дополнительные сведения о полях журнала действий можно найти [здесь](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fazure-monitor%2Fplatform%2Factivity-log-schema&data=02%7C01%7CNoga.Lavi%40microsoft.com%7C90b7c2308c0647c0347908d7c9a2918d%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637199572373563632&sdata=6QXLswwZgUHFXCuF%2FgOSowLzA8iOALVgvL3GMVhkYJY%3D&reserved=0).



> [!NOTE]
> Для того, чтобы новое правило генерации оповещений журнала действий стало активным, может потребоваться до 5 минут.

## <a name="rest-api"></a>REST API 
[Azure Monitor API оповещений журнала действий](https://docs.microsoft.com/rest/api/monitor/activitylogalerts) является REST API. Он полностью совместим с REST APIом Azure Resource Manager. Его можно использовать через PowerShell с помощью командлета диспетчер ресурсов или Azure CLI.

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

### <a name="deploy-the-resource-manager-template-with-powershell"></a>Развертывание шаблона диспетчер ресурсов с помощью PowerShell
Чтобы использовать PowerShell для развертывания примера диспетчер ресурсов шаблона, показанного в предыдущем разделе [шаблона Azure Resource Manager](#azure-resource-manager-template) , используйте следующую команду:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "myRG" -TemplateFile sampleActivityLogAlert.json -TemplateParameterFile sampleActivityLogAlert.parameters.json
```

где Самплеактивитилогалерт. parameters. JSON содержит значения, указанные для параметров, необходимых для создания правила генерации оповещений.

### <a name="use-activity-log-powershell-cmdlets"></a>Использование командлетов PowerShell для журнала действий

Для оповещений журнала действий предусмотрены специальные командлеты PowerShell.

- [Set-азактивитилогалерт](https://docs.microsoft.com/powershell/module/az.monitor/Set-AzActivityLogAlert): создает новое оповещение журнала действий или обновляет существующее оповещение журнала действий.
- [Get-азактивитилогалерт](https://docs.microsoft.com/powershell/module/az.monitor/Get-AzActivityLogAlert): получает один или несколько ресурсов оповещений журнала действий.
- [Enable-азактивитилогалерт](https://docs.microsoft.com/powershell/module/az.monitor/Enable-AzActivityLogAlert): включает существующее оповещение журнала действий и задает его Теги.
- [Disable-азактивитилогалерт](https://docs.microsoft.com/powershell/module/az.monitor/Disable-AzActivityLogAlert): отключает существующее оповещение журнала действий и задает его Теги.
- [Remove-азактивитилогалерт](https://docs.microsoft.com/powershell/module/az.monitor/Remove-AzActivityLogAlert): удаляет оповещение журнала действий.

## <a name="azure-cli"></a>Azure CLI

Команды Azure CLI, представленные в наборе [az monitor activity-log alert](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert), доступны для управления правилами генерации оповещений для журнала действий.

Чтобы создать новое правило генерации оповещений журнала действий, используйте следующие команды в следующем порядке:

1. [AZ Monitor активности — журнал оповещение Create](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-create): создание нового ресурса правила генерации оповещений журнала действий.
1. [AZ Monitor активности — журнал оповещений](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert/scope): Добавление области действия для созданного правила оповещения журнала действий.
1. [AZ Monitor Activity — действие предупреждения журнала — группа](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert/action-group): добавьте группу действий в правило оповещения журнала действий.

Чтобы получить один ресурс правила генерации оповещений журнала действий, используйте команду Azure CLI [AZ Monitor активности — журнал оповещений](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-show
). Чтобы просмотреть все ресурсы правил генерации оповещений журнала действий в группе ресурсов, используйте команду [AZ Monitor активности — журнал оповещений](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-list).
Ресурсы правила генерации оповещений журнала действий можно удалить с помощью команды Azure CLI [AZ Monitor активности — журнал оповещений Delete](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-delete).

## <a name="next-steps"></a>Дальнейшие шаги

- Сведения о [схеме веб-перехватчика для журналов действий](../../azure-monitor/platform/activity-log-alerts-webhook.md).
- Ознакомьтесь с [обзором журналов действий](../../azure-monitor/platform/activity-log-alerts.md).
- Дополнительные сведения о [группах действий](../../azure-monitor/platform/action-groups.md).  
- Сведения об [уведомлениях о работоспособности служб](../../azure-monitor/platform/service-notifications.md).
