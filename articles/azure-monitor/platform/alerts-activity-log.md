---
title: Создание, представление и управление оповещениями о журналах активности в Azure Monitor
description: Создавайте оповещения о журнале активности с помощью портала Azure, шаблона управления ресурсами Azure и Azure PowerShell.
ms.topic: conceptual
ms.subservice: alerts
ms.date: 06/25/2019
ms.openlocfilehash: bfbe2bc3ae3edf9285d3ec006ab0451f070cabd6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80132391"
---
# <a name="create-view-and-manage-activity-log-alerts-by-using-azure-monitor"></a>Создание, представление и управление оповещениями о журналах активности с помощью Azure Monitor  

## <a name="overview"></a>Обзор

Оповещения журнала действий создаются в тех случаях, когда происходит новое событие, записываемое в журнал действий, которое соответствует определенным условиям для оповещения.

Эти оповещения предназначены для ресурсов Azure и могут быть созданы с помощью шаблона управления ресурсами Azure. Их также можно создать, обновить или удалить на портале Azure. Как правило, создаются оповещения о журнале активности для получения уведомлений при определенных изменениях ресурсов в подписке Azure. Оповещения часто относятся к определенным группам ресурсов или ресурсам. Например, вы можете получить уведомление при удалении любой виртуальной машины в группе ресурсов образца **myProductionResourceGroup.** Или вы можете получить уведомление, если какие-либо новые роли назначены пользователю в вашей подписке.

> [!IMPORTANT]
> Оповещения в уведомлении о работоспособности службы не могут быть созданы через интерфейс для создания оповещения о журнале активности. Чтобы узнать больше о том, как создавать и использовать уведомления о работоспособности службы, [см.](alerts-activity-log-service-notifications.md)

При создании правил оповещения убедитесь:

- Подписка в области не отличается от подписки, в которой создается оповещение.
- Критериями должны быть уровень, статус, вызыватель, группа ресурсов, идентификатор ресурсов или категория событий типа ресурса, на которой настроено оповещение.
- В конфигурации оповещения JSON нет "ни одного" состояния или вложенных условий. В принципе, только одно условие "allOf" допускается без каких-либо дальнейших "allOf" или "anyOf" условиях.
- Когда категория является "административной", необходимо указать хотя бы один из предшествующих критериев в вашем предупреждении. Вы не можете создать оповещение, которое активируется каждый раз при создании события в журналах действий.

## <a name="azure-portal"></a>Портал Azure

Вы можете использовать портал Azure для создания и изменения правил оповещения о регистрации активности. Опыт интегрирован с журналом активности Azure, чтобы обеспечить беспрепятственное создание оповещения для конкретных событий, представляющих интерес.

### <a name="create-with-the-azure-portal"></a>Создание с помощью портала Azure

Выполните следующую процедуру.

1. На портале Azure выберите **Monitor** > **Alerts**.
2. Выберите **новое правило оповещения** в верхнем левом углу окна **оповещения.**

     ![Новое правило генерации оповещений](media/alerts-activity-log/AlertsPreviewOption.png)

     Откроется окно **создания правила**.

      ![Новые параметры правил оповещения](media/alerts-activity-log/create-new-alert-rule-options.png)

3. В **соответствии с условием оповещения Define,** предоставьте следующую информацию и выберите **Done:**

   - **Цель оповещения:** Чтобы просмотреть и выбрать цель для нового оповещения, используйте **фильтр по подписке** / **Фильтр по типу ресурса.** Выберите группу ресурсов или ресурсов из отображаемого списка.

     > [!NOTE]
     > 
     > Вы можете выбрать только отслеживаемый ресурс [Azure Resource Manager,](../../azure-resource-manager/management/overview.md) группу ресурсов или всю подписку на сигнал журнала активности. 

     **Пример представления целевого объекта оповещения**

     ![Выбор цели](media/alerts-activity-log/select-target.png)

   - В соответствии с **целевыми критериями,** выберите **Критерии добавления**. Отображаются все доступные сигналы для цели, которые включают в себя сигналы из различных категорий **журнала активности.** Имя категории придатилось к названию **службы мониторинга.**

   - Выберите сигнал из отображенного списка различных операций, возможных для типа **журнал действий**.

     Вы можете выбрать временную шкалу истории журнала и соответствующую логику оповещений для целевого сигнала.

     **Снимок экрана: (Add criteria) (Добавить критерии)**

     ![Добавление условий](media/alerts-activity-log/add-criteria.png)

     - **Время истории**: События, доступные для выбранной операции, могут быть построены за последние 6, 12 или 24 часа или за последнюю неделю.

     - **Логика оповещения**:

       - **Уровень события**: Уровень серьезности события: _Verbose_, _Информационный_, _Предупреждение,_ _Ошибка_, или _Критический_.
       - **Статус**: Статус события: _Запущенный,_ _Неудачный_или _успешно_.
       - **Событие, инициированное**: Также известный как вызыватель. Адрес электронной почты или идентификатор Azure Active Directory пользователя, выполнившего операцию.

       Этот пример сигнального графика имеет логику оповещения применяется:

       ![Выбранные критерии](media/alerts-activity-log/criteria-selected.png)

4. В **приведенных данных оповещения Define**, предоставьте следующие сведения:

    - **Имя правила оповещения**: Имя для нового правила оповещения.
    - **Описание**: Описание нового правила оповещения.
    - **Сохраняйте оповещение в группе ресурсов:** Выберите группу ресурсов, где вы хотите сохранить это новое правило.

5. В разделе **Группа действий** в раскрывающемся меню укажите группу действий, которую нужно назначить новому правилу генерации оповещений. Или [создайте новую группу действий](../../azure-monitor/platform/action-groups.md) и присвоите ее новому правилу. Чтобы создать новую группу, выберите **новую группу**.

6. Для включения правил после их создания выберите **«Да»** для **правила «Включить» при создании** опции.
7. Выберите **Создать правило генерации оповещений**.

    Создается новое правило оповещения для журнала активности, и в правом верхнем углу окна появляется сообщение о подтверждении.

    Вы можете включить, отключить, изменить или удалить правило. Узнайте больше о том, как управлять правилами журнала активности.


Простая аналогия для понимания условий, на которых могут быть созданы правила оповещения в журнале активности, — это изучение или фильтрация событий с помощью [журнала активности на портале Azure.](activity-log-view.md#azure-portal) На экране **журнала Azure Monitor - Activity log** вы можете отфильтровать или найти необходимое событие, а затем создать оповещение с помощью кнопки оповещения журнала **Add.** Затем выполните шаги 4-7, как показано ранее.
    
 ![Добавление оповещения из журнала активности](media/alerts-activity-log/add-activity-log.png)
    

### <a name="view-and-manage-in-the-azure-portal"></a>Просмотр и управление на портале Azure

1. На портале Azure выберите **Monitor** > **Alerts**. Выберите **правила оповещения Управления** в верхнем левом углу окна.

    ![Управление правилами генерации оповещений](media/alerts-activity-log/manage-alert-rules.png)

    Откроется список доступных правил.

2. Поиск правила журнала активности для изменения.

    ![Правила оповещения о поисковых действиях](media/alerts-activity-log/searth-activity-log-rule-to-edit.png)

    Вы можете использовать доступные фильтры, _подписку,_ _группу ресурсов,_ _ресурс,_ _тип сигнала_или _статус,_ чтобы найти правило активности, которое вы хотите отсеить.

   > [!NOTE]
   > 
   > Вы можете отсеивать только **Описание,** **Целевые критерии**и **группы действий.**

3. Выберите правило и дважды щелкните, чтобы отсеивать параметры правил. Внести необходимые изменения, а затем выберите **Сохранить**.

   ![Управление правилами генерации оповещений](media/alerts-activity-log/activity-log-rule-edit-page.png)

4. Вы можете включить, отключить или удалить правило. Выберите подходящий вариант в верхней части окна после выбора правила, описанного в шаге 2.


## <a name="azure-resource-manager-template"></a>Шаблон Azure Resource Manager
Для создания правила оповещения о журнале активности с помощью шаблона `microsoft.insights/activityLogAlerts`Azure Resource Manager создадимый ресурс типа . Затем следует заполнить все связанные свойства. Вот шаблон, который создает правило оповещения журнала активности:

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
Предыдущий образец JSON можно сохранить, например, как, например, примерActivityLogAlert.json для этой пошаговой работы и может быть развернут с помощью [менеджера ресурсов Azure на портале Azure.](../../azure-resource-manager/templates/deploy-portal.md)

Следующие поля — это параметры, которые можно использовать в шаблоне менеджера ресурсов Azure для полей условий: Обратите внимание, что «Здоровье ресурсов», «Советник» и «Здоровье обслуживания» имеют дополнительные поля свойств для своих специальных полей. 
1. resourceId: Идентификатор ресурса влияемого ресурса в событии журнала активности, на который должно быть сгенерировано оповещение.
2. категория: Категория в событии журнала активности. Например: Административный, ServiceHealth, ResourceHealth, Autoscale, Безопасность, Рекомендация, Политика.
3. адрес электронной почты или идентификатор active Directory Azure пользователя, выполнявшего работу события журнала активности.
4. уровень: Уровень активности в событии журнала активности, на которое должно быть сгенерировано оповещение. Например: Критическая, Ошибка, Предупреждение, Информационный, Verbose.
5. имя операции: Название операции в событии журнала активности. Например: Microsoft.Resources/deployments/write
6. группа ресурсов: Название группы ресурсов для влияемого ресурса в событии журнала активности.
7. resourceProvider: [Поставщики ресурсов Azure и объяснение типов.](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fazure-resource-manager%2Fmanagement%2Fresource-providers-and-types&data=02%7C01%7CNoga.Lavi%40microsoft.com%7C90b7c2308c0647c0347908d7c9a2918d%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637199572373543634&sdata=4RjpTkO5jsdOgPdt%2F%2FDOlYjIFE2%2B%2BuoHq5%2F7lHpCwQw%3D&reserved=0) Для списка, который отображает ресурсы для служб Azure, можно узнать о [поставщиках ресурсов для служб Azure.](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fazure-resource-manager%2Fmanagement%2Fazure-services-resource-providers&data=02%7C01%7CNoga.Lavi%40microsoft.com%7C90b7c2308c0647c0347908d7c9a2918d%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637199572373553639&sdata=0ZgJPK7BYuJsRifBKFytqphMOxMrkfkEwDqgVH1g8lw%3D&reserved=0)
8. статус: Строка, описывающая состояние операции в событии действия. Например: Запущенный, В прогрессе, удалось, не удалось, активный, решен
9. subStatus: Обычно код состояния HTTP соответствующего вызова REST, но также может включать другие строки, описывающие субстатус.   Например: OK (HTTP Status Code: 200), Создан (КОД статуса HTTP: 201), Принятый (код статуса HTTP: 202), Нет контента (код состояния HTTP: 204), Bad Request (HTTP Status Code: 400), Не найдено (код статуса HTTP: 404), Конфликт (код статуса HTTP: 409), Внутренний сервер Ошибка (код статуса HTTP: 500), недоступная служба (код состояния HTTP: 503), Тайм-аут шлюза (код состояния HTTP: 504).
10. resourceType: Тип ресурса, который был затронут событием. Например: Microsoft.Resources/развертывание

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
Более подробную информацию о полях журнала деятельности вы можете найти [здесь](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fazure-monitor%2Fplatform%2Factivity-log-schema&data=02%7C01%7CNoga.Lavi%40microsoft.com%7C90b7c2308c0647c0347908d7c9a2918d%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637199572373563632&sdata=6QXLswwZgUHFXCuF%2FgOSowLzA8iOALVgvL3GMVhkYJY%3D&reserved=0).



> [!NOTE]
> Для активного действия нового правила оповещения о журнале активности может потребоваться до 5 минут.

## <a name="rest-api"></a>REST API 
API журналов [активности мониторинга Azure является](https://docs.microsoft.com/rest/api/monitor/activitylogalerts) API REST. Он полностью совместим с API REST-менеджером ресурсов Azure. Он может быть использован через PowerShell с помощью cmdlet менеджера ресурсов или Azure CLI.

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

### <a name="deploy-the-resource-manager-template-with-powershell"></a>Развертывание шаблона управления ресурсами с помощью PowerShell
Чтобы использовать PowerShell для развертывания шаблона «Менеджер ресурсов», показанного в предыдущем разделе [шаблона управления ресурсами Azure,](#azure-resource-manager-template) используйте следующую команду:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "myRG" -TemplateFile sampleActivityLogAlert.json -TemplateParameterFile sampleActivityLogAlert.parameters.json
```

где образецActivityLogAlert.parameters.json содержит значения, предусмотренные для параметров, необходимых для создания правил оповещения.

### <a name="use-activity-log-powershell-cmdlets"></a>Использование журнала активности PowerShell cmdlets

Для оповещений журнала действий предусмотрены специальные командлеты PowerShell.

- [Set-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Set-AzActivityLogAlert): Создает новое оповещение журнала активности или обновляет существующее оповещение журнала активности.
- [Get-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Get-AzActivityLogAlert): Получает один или несколько ресурсов оповещения о деятельности.
- [Включить-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Enable-AzActivityLogAlert): Включает существующее оповещение журнала активности и устанавливает его теги.
- [Отстраните от Системы-AzActivityLogAlert:](https://docs.microsoft.com/powershell/module/az.monitor/Disable-AzActivityLogAlert)отстраняет существующее оповещение журнала активности и устанавливает его теги.
- [Удалить-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Remove-AzActivityLogAlert): Удаляет оповещение журнала активности.

## <a name="azure-cli"></a>Azure CLI

Команды Azure CLI, представленные в наборе [az monitor activity-log alert](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert), доступны для управления правилами генерации оповещений для журнала действий.

Чтобы создать новое правило оповещения о журнале активности, используйте следующие команды в этом порядке:

1. [Аз мониторинг ас-мониторинг ажурных журналов создания:](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-create)Создание нового ресурса правила оповещения журнала активности.
1. [az monitor activity-log alert scope:](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert/scope)Добавление области для созданного правила оповещения о журнале активности.
1. [az monitor activity-log alert action-group:](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert/action-group)Добавление группы действий в правило оповещения о журнале активности.

Для получения одного ресурса правила оповещения о журнале активности используйте шоу оповещения о наблюдении за [активностью](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-show
)Azure CLI. Для просмотра всех ресурсов правил оповещения о защите активности в группе ресурсов используйте [список оповещения о отслеживании активности az](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-list)monitor.
Ресурсы правила оповещения о журнале активности могут быть удалены с помощью удаления системы оповещения о [отслеживании активности](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-delete)Az- Log Az, отслеживая действия.

## <a name="next-steps"></a>Дальнейшие действия

- Узнайте о [схеме webhook для журналов активности.](../../azure-monitor/platform/activity-log-alerts-webhook.md)
- Прочитайте [обзор журналов активности.](../../azure-monitor/platform/activity-log-alerts.md)
- Подробнее о [группах действий.](../../azure-monitor/platform/action-groups.md)  
- Узнайте об [уведомлениях службы о работоспособности](../../azure-monitor/platform/service-notifications.md).
