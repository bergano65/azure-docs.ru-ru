---
title: Планирование управления затратами на Cognitive Services Azure
description: Узнайте, как планировать и управлять затратами на Cognitive Services Azure с помощью анализа затрат в портал Azure.
author: erhopf
ms.author: erhopf
ms.custom: subject-cost-optimization
ms.service: cognitive-services
ms.topic: how-to
ms.date: 12/15/2020
ms.openlocfilehash: 61eb7d06773428074940d153b01d23b13468795d
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/26/2021
ms.locfileid: "98788830"
---
# <a name="plan-and-manage-costs-for-azure-cognitive-services"></a>Планирование и управление затратами на Cognitive Services Azure

В этой статье описывается планирование и управление затратами на Cognitive Services Azure. Сначала вы используете калькулятор цен Azure, чтобы помочь спланировать Cognitive Servicesные затраты, прежде чем добавлять ресурсы для службы для оценки затрат. После добавления ресурсов Azure ознакомьтесь с оценочными затратами. После начала использования ресурсов Cognitive Services (например, речи, Компьютерное зрение, LUIS, Анализ текста, переводчиков и т. д.) используйте функции управления затратами для установки бюджетов и отслеживания затрат. Вы также можете просматривать прогнозные затраты и выявление тенденций расходов для выявления областей, в которых может потребоваться выполнить действия. Затраты на Cognitive Services — это только часть ежемесячных затрат в счете Azure. Несмотря на то, что в этой статье объясняется, как спланировать затраты для Cognitive Services и управлять ими, вы оплачиваете все службы и ресурсы Azure, используемые в вашей подписке Azure, включая сторонние службы.

## <a name="prerequisites"></a>Предварительные требования

Анализ затрат в службе "Управление затратами" поддерживает большинство типов учетных записей Azure, но не все из них. Полный список поддерживаемых типов учетных записей см. в статье [Understand Cost Management data](../cost-management-billing/costs/understand-cost-mgt-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) (Интерпретация данных службы "Управление затратами"). Для просмотра данных о затратах требуется по крайней мере доступ на чтение для учетной записи Azure. Дополнительные сведения о назначении доступа к данным службы "Управление затратами" Azure см. в [этой статье](../cost-management-billing/costs/assign-access-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

<!--Note for Azure service writer: If you have other prerequisites for your service, insert them here -->

## <a name="estimate-costs-before-using-cognitive-services"></a>Оценка затрат перед использованием Cognitive Services

Используйте [Калькулятор цен Azure](https://azure.microsoft.com/pricing/calculator/) , чтобы оценить затраты перед добавлением Cognitive Services.

:::image type="content" source="media/cognitive-services-pricing-calculator.png" alt-text="Калькулятор цен Azure для Cognitive Services" border="true":::

Когда вы добавляете новые ресурсы в рабочую область, вернитесь к этому калькулятору и добавьте в него тот же ресурс, чтобы обновить оценки затрат.

Дополнительные сведения см. на странице [цен на Cognitive Services Azure](https://azure.microsoft.com/pricing/details/cognitive-services/).

## <a name="understand-the-full-billing-model-for-cognitive-services"></a>Сведения о модели полного выставления счетов для Cognitive Services

Cognitive Services работает в инфраструктуре Azure, которая выполняет [накопление затрат](https://azure.microsoft.com/pricing/details/cognitive-services/) при развертывании нового ресурса. Важно понимать, что дополнительная инфраструктура может начислять затраты. Необходимо управлять этими затратами при внесении изменений в развернутые ресурсы. 

### <a name="costs-that-typically-accrue-with-cognitive-services"></a>Затраты, которые обычно начисляются с помощью Cognitive Services

Как правило, после развертывания ресурса Azure затраты определяются ценовой категорией и вызовы API, которые вы вносите в конечную точку. Если используемая служба имеет уровень обязательства, при переходе на выделенные вызовы на вашем уровне может взиматься плата.

При использовании этих служб могут быть вычислены дополнительные затраты:

#### <a name="qna-maker"></a>QnA Maker

При создании ресурсов для QnA Maker также могут быть созданы ресурсы для других служб Azure. К ним относятся следующие:

- [Служба приложений Azure (для среды выполнения)](https://azure.microsoft.com/pricing/details/app-service/)
- [Когнитивный поиск Azure (для данных)](https://azure.microsoft.com/pricing/details/search/)
 
### <a name="costs-that-might-accrue-after-resource-deletion"></a>Затраты, которые могут быть начислены после удаления ресурса

#### <a name="qna-maker"></a>QnA Maker

После удаления QnA Maker ресурсов могут продолжать существовать следующие ресурсы. Они продолжают начислять затраты до тех пор, пока вы не удалите их.

- [Служба приложений Azure (для среды выполнения)](https://azure.microsoft.com/pricing/details/app-service/)
- [Когнитивный поиск Azure (для данных)](https://azure.microsoft.com/pricing/details/search/)

### <a name="using-azure-prepayment-credit-with-cognitive-services"></a>Использование предварительной оплаты Azure с Cognitive Services

Вы можете платить за Cognitive Servicesную оплату с предоплатой Azure (ранее называлось денежным обязательством). Однако вы не можете использовать предварительную оплату Azure для оплаты за продукты и услуги сторонних производителей, включая данные из Azure Marketplace.

## <a name="create-budgets"></a>Создание бюджетов

Вы можете создавать [бюджеты](../cost-management-billing/costs/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) для управления затратами и создавать [оповещения](../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) , которые автоматически уведомляют заинтересованных лиц о трате аномалий и чрезмерной трате рисков. Оповещения основываются на расходах по сравнению с пороговыми значениями бюджета и затрат. Бюджеты и оповещения создаются для подписок Azure и групп ресурсов, поэтому они полезны в рамках общей стратегии мониторинга затрат. 

Бюджеты можно создавать с помощью фильтров для конкретных ресурсов или служб в Azure, если требуется дополнительная детализация в мониторинге. Фильтры помогают предотвратить случайное создание новых ресурсов, которые избавляет вас от дополнительных денег. Дополнительные сведения о параметрах фильтра при создании бюджета см. в разделе [Параметры группы и фильтра](../cost-management-billing/costs/group-filter.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="export-cost-data"></a>Экспорт данных о затратах

Вы также можете [экспортировать данные о затратах](../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) в учетную запись хранения. Это полезно, если вам или другим пользователям требуется выполнить дополнительный анализ данных за затраты. Например, группы финансового отдела могут анализировать данные с помощью Excel или Power BI. Вы можете экспортировать затраты на ежедневное, еженедельное или ежемесячное расписание и задать настраиваемый диапазон дат. Для извлечения наборов данных о затратах рекомендуется экспортировать данные о затратах.

<!--
## Other ways to manage and reduce costs for Cognitive Services

Work with Dean to complete this section in 2021.

-->

## <a name="next-steps"></a>Дальнейшие действия

- Узнайте [, как оптимизировать инвестиции в облако с помощью службы "Управление затратами Azure](../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)".
- Дополнительные сведения об управлении затратами с помощью [анализа затрат](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Узнайте, как [предотвратить непредвиденные затраты](../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Пройдите курс обучения по [управлению затратами](/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) .