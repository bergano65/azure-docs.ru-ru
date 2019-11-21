---
title: Руководство. Уменьшение затрат на Azure с помощью рекомендаций по оптимизации | Документация Майкрософт
description: Это руководство поможет вам уменьшить затраты на Azure при условии использования рекомендаций по оптимизации.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 10/24/2019
ms.topic: conceptual
ms.service: cost-management-billing
manager: dougeby
ms.custom: seodec18
ms.openlocfilehash: a9dbb121cab49024aaf0dc65bbac938764d9f8b2
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74229835"
---
# <a name="tutorial-optimize-costs-from-recommendations"></a>Руководство. Рекомендации по оптимизации затрат

Управление затратами Azure работает с Помощником по Azure, чтобы обеспечить рекомендации по оптимизации затрат. Помощник по Azure помогает повысить эффективность работы благодаря выявлению незадействованных или недостаточно используемых ресурсов. В этом руководстве показан пример, где вы определяете недостаточно используемые ресурсы Azure, и затем принимаете меры по сокращению затрат.

Из этого руководства вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * Просмотрите рекомендации по оптимизации, чтобы узнать о возможных недостатках использования
> * Используйте рекомендацию для изменения размера виртуальной машины на более экономичный вариант
> * Проверьте результат, чтобы убедиться в том, что размер виртуальной машины успешно изменен

## <a name="prerequisites"></a>Технические условия
Recommendations are available for a variety of scopes and Azure account types. Полный список поддерживаемых типов учетных записей см. в статье [Интерпретация данных службы "Управление затратами"](understand-cost-mgt-data.md). Для просмотра данных о расходах вы должны по крайней мере иметь доступ на чтение к одной или нескольким следующим областям. См. [основные сведения об областях и работе с ними](understand-work-scopes.md).

- Subscription
- группа ресурсов.

Ваши виртуальные машины должны быть активные в течение по меньшей мере 14 дней.

## <a name="sign-in-to-azure"></a>Войдите в Azure
Войдите на портал Azure по адресу [https://portal.azure.com](https://portal.azure.com/).

## <a name="view-cost-optimization-recommendations"></a>Просмотр рекомендаций по оптимизации затрат

To view cost optimization recommendations for a subscription, open the desired scope in the Azure portal and select **Advisor recommendations**.

To view recommendations for a management group, open the desired scope in the Azure portal and select **Cost analysis** in the menu. Use the **Scope** pill to switch to a different scope, such as a management group. Select **Advisor recommendations** in the menu. См. [основные сведения об областях и работе с ними](understand-work-scopes.md).

![Рекомендации Помощника по управлению затратами, показанные на портале Azure](./media/tutorial-acm-opt-recommendations/advisor-recommendations.png)

В списке рекомендаций указаны неэффективность использования или рекомендации по покупке, которые помогут вам сэкономить деньги. В категории **Potential yearly savings** (Потенциальная ежегодная экономия) указанно общую сумму, которую вы можете сэкономить, если завершите работу всех своих виртуальных машин, которые соответствуют правилам рекомендации, или ликвидируете их. Если вы не хотите завершать их работу, то можете изменить их размер с более экономичным номером SKU для виртуальных машин.

Категории **Impact** (Влияние) и **Potential yearly savings** (Потенциальная ежегодная экономия) должны помочь определить рекомендации, которые позволят больше сэкономить.

High impact recommendations include:
- [Buy reserved virtual machine instances to save money over pay-as-you-go costs](../advisor/advisor-cost-recommendations.md#buy-reserved-virtual-machine-instances-to-save-money-over-pay-as-you-go-costs)
- [Optimize virtual machine spend by resizing or shutting down underutilized instances](../advisor/advisor-cost-recommendations.md#optimize-virtual-machine-spend-by-resizing-or-shutting-down-underutilized-instances)
- [Use Standard Storage to store Managed Disks snapshots](../advisor/advisor-cost-recommendations.md#use-standard-snapshots-for-managed-disks)

Medium impact recommendations include:
- [Delete Azure Data Factory pipelines that are failing](../advisor/advisor-cost-recommendations.md#delete-azure-data-factory-pipelines-that-are-failing)
- [Reduce costs by eliminating un-provisioned ExpressRoute circuits](../advisor/advisor-cost-recommendations.md#reduce-costs-by-eliminating-unprovisioned-expressroute-circuits)
- [Reduce costs by deleting or reconfiguring idle virtual network gateways](../advisor/advisor-cost-recommendations.md#reduce-costs-by-deleting-or-reconfiguring-idle-virtual-network-gateways)

## <a name="act-on-a-recommendation"></a>Выполнение рекомендации

Azure Advisor monitors your virtual machine usage for seven days and then identifies underutilized virtual machines. Виртуальные машины, у которых использование ресурсов ЦП составляет не больше 5 %, а использование ресурсов сети составляет не больше 7 МБ на протяжении 4 или более дней, будут считаться недостаточно нагруженными.

Параметр использования ЦП на 5 % или меньше настроен по умолчанию, но вы можете его изменить. Дополнительные сведения об изменении параметра см. в статье [Приступая к работе с Azure Advisor](../advisor/advisor-get-started.md#configure-low-usage-vm-recommendation).

Хотя в некоторых сценариях низкая загрузка может возникать намеренно, часто затраты можно сократить, изменив размер виртуальных машин на более экономичный вариант. Фактическая экономия зависит от выбранного размера. Давайте рассмотрим пример изменения размера виртуальной машины.

В списке рекомендаций выберите рекомендацию **Задайте подходящий размер недогруженных виртуальных машин или завершите их работу**. В списке виртуальных машин выберите ту виртуальную машину, размер которой необходимо изменить, и щелкните ее имя. Сведения о виртуальной машине отображаются таким образом, что можно проверить метрики использования. Вы можете сохранить значение **потенциальной ежегодной экономии**, если завершаете работу виртуальной машины или удаляете ее. Изменение размера виртуальной машины поможет сэкономить деньги, но не весь объем потенциальной ежегодной экономии.

![Пример сведений о рекомендации](./media/tutorial-acm-opt-recommendations/recommendation-details.png)

В сведениях о виртуальной машине проверьте ее использование, чтобы подтвердить то, что она подходящий кандидат на изменение размера.

![Пример сведений об использовании виртуальной машины за определенный период времени](./media/tutorial-acm-opt-recommendations/vm-details.png)

Обратите внимание на текущий размер виртуальной машины. После проверки на необходимость изменения размера виртуальной машины, закройте сведения о виртуальной машине, чтобы просмотреть список виртуальных машин.

In the list of candidates to shut down or resize, select **Resize *&lt;FromVirtualMachineSKU&gt;* to *&lt;ToVirtualMachineSKU&gt;***.
![Пример рекомендации с параметром "Измените размер виртуальной машины"](./media/tutorial-acm-opt-recommendations/resize-vm.png)

После этого вы увидите список доступных параметров для изменения размера. Выберите тот, который обеспечит вашему сценарию самую лучшую производительность и экономичность. In the following example, the option chosen resizes from **Standard_D8s_v3** to **Standard_D2s_v3**.

![Пример списка доступных размеров виртуальных машин, в котором можно выбрать размер](./media/tutorial-acm-opt-recommendations/choose-size.png)

After you choose a suitable size, click **Resize** to start the resize action.

Для изменения размера активную виртуальную машину необходимо перезапустить. Если виртуальная машина находится в рабочей среде, то операцию изменения размера рекомендуется запускать после окончания рабочего дня. Планирование перезапуска может уменьшить сбои, вызванные кратковременной недоступностью.

## <a name="verify-the-action"></a>Проверка действия

Если изменения размера виртуальной машины выполнено успешно, то отображается уведомление Azure.

![Уведомление об успешном изменении размера виртуальной машины](./media/tutorial-acm-opt-recommendations/resized-notification.png)

## <a name="next-steps"></a>Дальнейшие действия

Из этого руководства вы узнали, как выполнить следующие задачи:

> [!div class="checklist"]
> * Просмотрите рекомендации по оптимизации, чтобы узнать о возможных недостатках использования
> * Используйте рекомендацию для изменения размера виртуальной машины на более экономичный вариант
> * Проверьте результат, чтобы убедиться в том, что размер виртуальной машины успешно изменен

Если вы еще не прочитали статью c рекомендациями по управлению затратами, то в ней вы сможете найти эффективные указания и принципы, которые помогут ими управлять.

> [!div class="nextstepaction"]
> [Cost Management best practices](cost-mgt-best-practices.md) (Рекомендации по управлению затратами)
