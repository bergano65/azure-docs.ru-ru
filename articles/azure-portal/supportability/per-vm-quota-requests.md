---
title: Запросить увеличение лимитов квот vCPU по серии Azure VM
description: Как запросить увеличение лимита квоты VCPU для серии VM на портале Azure, что увеличивает общий региональный лимит VCPU на ту же сумму.
author: sowmyavenkat86
ms.author: svenkat
ms.date: 01/27/2020
ms.topic: article
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: de06375dad5999a29691435317e62585a2ea7f64
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76843759"
---
# <a name="standard-quota-increase-limits-by-vm-series"></a>Стандартная квота: Увеличение лимитов по сериям VM

Менеджер ресурсов Azure поддерживает два типа квот vCPU для виртуальных машин:

* *VMs с оплатой по мере вхром* и *зарезервированных экземплярах VM* подчиняются *стандартной квоте vCPU.*
* *Spot VMs* подлежат *спотовой квоте VCPU.*

Стандартная квота vCPU для с оплатой по мере использования и зарезервированных виртуальных машин применяется на двух уровнях для каждой подписки в каждом регионе:

* Первый уровень — это *общий региональный лимит vCPUs*во всех сериях VM.
* Второй уровень — это *лимит vCPUs серии vCPUs серии VCPUs,* например vCPUs серии Dv3.

Всякий раз, когда вы развертываете новое место VM, общее новое и существующее использование vCPU для этой серии VM не должно превышать утвержденную квоту vCPU для этой конкретной серии VM. Кроме того, общее количество новых и существующих vCPUs, развернутых во всех сериях VM, не должно превышать общую утвержденную региональную квоту VCPU для подписки. Если какая-либо из этих квот превышена, развертывание VM не допускается.

Вы можете запросить увеличение лимита квоты VCPU для серии VM с помощью портала Azure. Увеличение квоты серии VM автоматически увеличивает общий региональный лимит VCPU на ту же сумму.

Чтобы узнать больше о стандартных квотах vCPU, [Azure subscription and service limits](https://docs.microsoft.com/azure/azure-supportability/classic-deployment-model-quota-increase-requests) [см.](../../virtual-machines/windows/quotas.md)

Чтобы узнать об увеличении лимита ВКПУ [Standard quota: Increase limits by region](regional-quota-requests.md)по регионам для стандартной квоты, см.

Чтобы узнать больше об увеличении пределов [Spot quota: Increase limits for all VM series](low-priority-quota.md)спот VM vCPU, см.

Вы можете запросить увеличение стандартных лимитов квот vCPU на серию VM любым из двух способов, как описано в следующих разделах.

## <a name="request-a-standard-quota-increase-from-help--support"></a>Запрос на стандартное увеличение квот от справки и поддержки

Чтобы запросить стандартное увеличение квоты vCPU на серию VM от **справки и поддержки:**

> [!NOTE]
> Вы также можете запросить увеличение лимита квот для нескольких регионов через один случай поддержки. Для получения подробной информации см.

1. В меню [портала Azure](https://portal.azure.com) выберите **поддержку Справки .**

   ![Ссылка на справку и поддержку](./media/resource-manager-core-quotas-request/help-plus-support.png)

1. В **справке и поддержке**выберите **новый запрос поддержки.**

    ![Создание нового запроса на поддержку](./media/resource-manager-core-quotas-request/new-support-request.png)

1. Для поля **Тип проблемы** выберите **Ограничения службы и подписки (квоты)**.

   ![Выберите тип проблемы](./media/resource-manager-core-quotas-request/select-quota-issue-type.png)

1. Для **подписки**выберите подписку, квоту которой вы хотите увеличить.

   ![Выберите подписку для увеличенной квоты](./media/resource-manager-core-quotas-request/select-subscription-support-request.png)

1. Для **типа квоты,** выберите **Вычислить-VM (cores-vCPUs) ограничение подписки увеличивается.**

   ![Выберите тип квоты](./media/resource-manager-core-quotas-request/select-quota-type.png)

1. Выберите **Далее: Решения** для открытия **PROBLEM DETAILS**. Выберите **Предоставить подробную информацию** для ввода дополнительной информации.

   ![Ссылка "Предоставить подробную информацию"](./media/resource-manager-core-quotas-request/provide-details-link.png)

1. В **деталях квоты**сделайте следующие шаги:

   ![Дополнительные сведения о квотах TProvide](./media/resource-manager-core-quotas-request/quota-details-deployment-rm-locations.png)

   1. Для **модели развертывания**выберите подходящую модель.

   1. Для **местоположения**, выберите место. Для выбранного местоположения, под **типами,** в **Выберите тип,** выберите **Стандарт**.

      ![Детали квоты - типы квот](./media/resource-manager-core-quotas-request/quota-details-select-standard-type.png)

      В **разделе Типы**можно запросить как стандартные, так и спотовые типы квот из одного случая поддержки через поддержку с несколькими выборами.

      Для получения дополнительной информации об [Azure spot VMs for virtual machine scale sets](../../virtual-machine-scale-sets/use-spot.md)увеличении предельных квот на спотовые квоты см.

   1. В **Стандартной**, выберите серии SKU для увеличения квот.

      ![Детали квоты - Серия SKU](./media/resource-manager-core-quotas-request/quota-details-standard-select-series.png)

   1. Введите новые лимиты квот, которые вы хотите для этой подписки. Чтобы удалить SKU из списка, очистите флажок рядом с SKU или выберите значок отбрасывательств "X".

      ![Выберите новый лимит vCPU](./media/resource-manager-core-quotas-request/quota-details-standard-set-vcpu-limit.png)

1. Чтобы запросить увеличение квоты для более чем одного местоположения, выберите дополнительное место в **местоположении,** а затем выберите подходящий тип VM. Затем можно ввести лимит, который применяется к дополнительному местоположению.

   ![Указать дополнительные местоположения в деталях квоты](./media/resource-manager-core-quotas-request/quota-details-multiple-locations.png)

1. Выберите **Сохранить и продолжить** создание запроса поддержки.

## <a name="request-a-standard-quota-increase-from-subscriptions"></a>Запросить стандартное увеличение квот от подписки

Чтобы запросить стандартное увеличение квоты vCPU на серию VM из **серии подписок:**

> [!NOTE]
> Вы также можете запросить увеличение лимита квот для нескольких регионов через один случай поддержки. Для получения подробной информации см.

1. На [портале Azure](https://portal.azure.com) **ищите подписки.**

   ![Подписка в поиске портала Azure](./media/resource-manager-core-quotas-request/search-for-subscriptions.png)

1. Выберите подписку, квоту которой вы хотите увеличить.

   ![Подписки для выбора для изменений](./media/resource-manager-core-quotas-request/select-subscription-change-quota.png)

1. В левом стеле выберите **квоты использования и**использования.

   ![Ссылка "Использование и квоты"](./media/resource-manager-core-quotas-request/select-usage-plus-quotas.png)

1. В правом верхнем правом положении выберите **увеличение запроса.**

   ![Выберите для увеличения квоты](./media/resource-manager-core-quotas-request/request-increase-from-subscription.png)

1. Для **типа квоты,** выберите **Вычислить-VM (cores-vCPUs) ограничение подписки увеличивается.**

   ![Выберите тип квоты](./media/resource-manager-core-quotas-request/select-quota-type.png)

1. В **деталях квоты**сделайте следующие шаги:

   1. Для **модели развертывания**выберите соответствующую модель, а для **местоположения**— месторасположение.

      ![Предоставить сведения о квотах](./media/resource-manager-core-quotas-request/quota-details-deployment-rm-locations.png)

   1. Для выбранного местоположения, под **типами,** **выберите тип,** а затем выберите **Стандарт.**

      ![Выберите стандартный тип](./media/resource-manager-core-quotas-request/quota-details-select-standard-type.png)

      В **разделе Типы**можно запросить как стандартные, так и спотовые типы квот из одного случая поддержки через поддержку с несколькими выборами.

      Для получения дополнительной информации об [Azure spot VMs for virtual machine scale sets](../../virtual-machine-scale-sets/use-spot.md)увеличении предельных квот на спотовые квоты см.

   1. Для **стандартных**, выберите серии SKU, квоты которых вы хотите увеличить.

      ![Детали квоты - Серия SKU](./media/resource-manager-core-quotas-request/quota-details-standard-select-series.png)

   1. Введите новые лимиты квот, которые вы хотите для этой подписки. Чтобы удалить SKU из списка, не выберите флажок рядом с SKU или выберите значок отбрасываемого "X".

      ![Выберите новый лимит vCPU](./media/resource-manager-core-quotas-request/quota-details-standard-set-vcpu-limit.png)

1. Чтобы запросить увеличение квоты для более чем одного местоположения, выберите дополнительное место в **местоположении,** а затем выберите подходящий тип VM.

   Этот шаг предзагружает серию SKU, выбранную для более ранних локаций. Введите лимиты квот, которые необходимо применить к дополнительной серии.

   ![Выберите дополнительные места в деталях квоты](./media/resource-manager-core-quotas-request/quota-details-multiple-locations.png)

1. Выберите **Сохранить и продолжить** создание запроса поддержки.
