---
title: Руководство. Создание и управление бюджетами Azure | Документация Майкрософт
description: Это руководство помогает планировать и учитывать расходы на потребляемые услуги Azure.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 11/12/2019
ms.topic: conceptual
ms.service: cost-management-billing
manager: adwise
ms.custom: seodec18
ms.openlocfilehash: edb46bc361c515439a93d9c3d0b9987bebe4b1b1
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74229880"
---
# <a name="tutorial-create-and-manage-azure-budgets"></a>Руководство. Создание и управление бюджетами Azure

Бюджеты в службе "Управление затратами" помогают планировать и отслеживать отчетность на уровне организации. С бюджетами вы можете учитывать службы Azure, которые используете или на которые подписываетесь в течение определенного периода. Они помогают сообщать другим пользователям о своих расходах, чтобы эффективно управлять затратами и контролировать то, как они возрастают с течением времени. Когда созданные вами пороговые значения бюджета превышены, активируются только уведомления. Ни один из ваших ресурсов не затронут а потребление не остановлено. Анализируя затраты, можно использовать бюджеты для их сравнения и отслеживания.

Cost and usage data is typically available within 12-16 hours and budgets are evaluated against these costs every four hours. Email notifications are normally received within 12-16 hours.

В конце периода (ежемесячно, ежеквартально или ежегодно) бюджеты автоматически сбрасываются до той же суммы, когда вы выбираете следующую дату истечения срока. Так как сбрасывание происходит при той же сумме бюджета, вам нужно создать отдельные бюджеты в том случае если сумма бюджета в валюте отличается от последующих периодов.

Примеры в этом руководстве помогут вам при создании и редактировании бюджета для подписки по соглашению Enterprise (EA) для Azure.

Watch the [How to create a budget to monitor your spending with Azure Cost Management](https://www.youtube.com/watch?v=ExIVG_Gr45A) video to see how you can create budgets in Azure to monitor spending.


Из этого руководства вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * Создание бюджета на портале Azure
> * Изменение бюджета

## <a name="prerequisites"></a>Технические условия

Бюджеты поддерживаются для различных типов учетных записей Azure. Полный список поддерживаемых типов учетных записей см. в статье [Интерпретация данных службы "Управление затратами"](understand-cost-mgt-data.md). Чтобы просмотреть данные о бюджете, вам нужен как минимум доступ на чтение для учетной записи Azure.

 Для подписок Azure EA вам необходимо иметь доступ на чтение для просмотра данных о бюджете. А для создания и администрирования бюджетов требуется разрешение уровня участника. Вы можете создать отдельные бюджеты для подписок EA и групп ресурсов. Однако вы не можете создать бюджеты для учетных записей выставления счетов EA.

The following Azure permissions, or scopes, are supported per subscription for budgets by user and group. См. [основные сведения об областях и работе с ними](understand-work-scopes.md).

- Владелец может создавать, изменять или удалять бюджеты для подписки.
- Участник и участник службы "Управление затратами" может создавать, изменять или удалять свои собственные бюджеты. Можно изменить сумму бюджета для бюджетов, созданных другими пользователями.
- Читатель и читатель службы "Управление затратами" может просматривать бюджеты, к которым имеет доступ.

Дополнительные сведения о назначении разрешений на доступ к данным службы "Управление затратами" см. в [этой статье](assign-access-acm-data.md).

## <a name="sign-in-to-azure"></a>Войдите в Azure

- Войдите на портал Azure по адресу https://portal.azure.com.

## <a name="create-a-budget-in-the-azure-portal"></a>Создание бюджета на портале Azure

Бюджет подписки Azure можно создать на месячный, квартальный или годовой период. Your navigational content in the Azure portal determines whether you create a budget for a subscription or for a management group.

To create or view a budget, open the desired scope in the Azure portal and select **Budgets** in the menu. For example, navigate to **Subscriptions**, select a subscription from the list, and then select **Budgets** in the menu. Use the **Scope** pill to switch to a different scope, like a management group, in Budgets. См. [основные сведения об областях и работе с ними](understand-work-scopes.md).

После создания бюджетов вы увидите упрощенную схему текущих затрат.

Щелкните **Добавить**.

![Example showing a list of budgets already created](./media/tutorial-acm-create-budgets/budgets01.png)

In the **Create budget** window, make sure that the scope shown is correct. Choose any filters that you want to add. Filters allow you to create budgets on specific costs, such as resource groups in a subscription or a service like virtual machines. Any filter you can use in cost analysis can also be applied to a budget.

After you've identified your scope and filters, type a budget name. Then, choose a monthly, quarterly or annual budget reset period. This reset period determines the time window that's analyzed by the budget. The cost evaluated by the budget starts at zero at the beginning of each new period. Создание квартального бюджета происходит таким же образом, как и создание ежемесячного бюджета. Разница заключается в том, что сумма ежеквартального бюджета равномерно распределяется по трем месяцам. An annual budget amount is evenly divided among all 12 months of the calendar year.

Если у вас есть подписка с оплатой по мере использования, подписка MSDN или подписка Visual Studio, ваш период выставления счетов может не совпадать с календарным месяцем. For those subscription types and resource groups, you can create a budget that's aligned to your invoice period or to calendar months. To create a budget aligned to your invoice period, select a reset period of **Billing month**, **Billing quarter**, or **Billing year**. To create a budget aligned to the calendar month, select a reset period of **Monthly**, **Quarterly**, or **Annually**.

Next, identify the expiration date when the budget becomes invalid and stops evaluating your costs.

Based on the fields chosen in the budget so far, a graph is shown to help you select a threshold to use for your budget. The suggested budget is based on the highest forecasted cost that you might incur in future periods. You can change the budget amount.

![Example showing budget creation with monthly cost data ](./media/tutorial-acm-create-budgets/monthly-budget01.png)

After you configure the budget amount, click **Next** to configure budget alerts. Для бюджета требуется указать как минимум одно пороговое значение (% бюджета) и соответствующий адрес электронной почты. При необходимости можно добавить не более пяти пороговых значений и пяти адресов электронной почты в один бюджет. When a budget threshold is met, email notifications are normally received in less than 20 hours. Дополнительные сведения об уведомлениях см. в статье [Use cost alerts to monitor usage and spending](cost-mgt-alerts-monitor-usage-spending.md) (Мониторинг использования и расходов с помощью уведомлений о затратах). In the example below, an email alert gets generated when 90% of the budget is reached. If you create a budget with the Budgets API, you can also assign roles to people to receive alerts. Assigning roles to people isn't supported in the Azure portal. For more about the Azure budgets API, see [Budgets API](/rest/api/consumption/budgets).

![Example showing alert conditions](./media/tutorial-acm-create-budgets/monthly-budget-alert.png)

После создания бюджета, отображается анализ затрат. Просмотр бюджета по отношению к вашей тенденции расходов является одним из первых шагов при начале [анализа затраты и расходов](quick-acm-cost-analysis.md).

![Пример бюджета и расходов в анализе затрат](./media/tutorial-acm-create-budgets/cost-analysis.png)

В приведенном выше примере вы создали бюджет для подписки. Тем не менее можно также создать бюджет для группы ресурсов. Если вы хотите создать бюджет для группы ресурсов, перейдите к **Управление затратами + выставление счетов** &gt; **Подписки** &gt; выберите подписку > **Группа ресурсов** > выберите группу ресурсов > **Бюджеты** > и затем **Добавить** бюджет.

## <a name="trigger-an-action-group"></a>Trigger an action group

When you create or edit a budget for a subscription or resource group scope, you can configure it to call an action group. The action group can perform a variety of different actions when your budget threshold is met. Action Groups are currently only supported for subscription and resource group scopes. For more information about Action Groups, see [Create and manage action groups in the Azure portal](../azure-monitor/platform/action-groups.md). For more information about using budget-based automation with action groups, see [Manage costs with Azure budgets](../billing/billing-cost-management-budget-scenario.md).



To create or update action groups, click **Manage action groups** while you're creating or editing a budget.

![Example of creating a budget to show Manage action groups](./media/tutorial-acm-create-budgets/manage-action-groups01.png)


Next, click **Add action group** and create the action group.


![Image of the Add action group box](./media/tutorial-acm-create-budgets/manage-action-groups02.png)

After the action group is created, close the box to return to your budget.

Configure your budget to use your action group when an individual threshold is met. Up to five different thresholds are supported.

![Example showing action group selection for an alert condition](./media/tutorial-acm-create-budgets/manage-action-groups03.png)

The following example shows budget thresholds set to 50%, 75% and 100%. Each is configured to trigger the specified actions within the designated action group.

![Example showing alert conditions configured with various action groups and type of actions](./media/tutorial-acm-create-budgets/manage-action-groups04.png)

Budget integration with action groups only works for action groups that have the common alert schema disabled. For more information about disabling the schema, see [How do I enable the common alert schema?](../azure-monitor/platform/alerts-common-schema.md#how-do-i-enable-the-common-alert-schema)

## <a name="next-steps"></a>Дальнейшие действия

Из этого руководства вы узнали, как выполнить следующие задачи:

> [!div class="checklist"]
> * Создание бюджета на портале Azure
> * Изменение бюджета

Перейдите к следующему руководству, чтобы создать повторяющийся экспорт данных управления затратами.

> [!div class="nextstepaction"]
> [Создание и управление экспортированными данными](tutorial-export-acm-data.md)
