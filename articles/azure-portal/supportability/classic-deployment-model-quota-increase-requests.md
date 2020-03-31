---
title: классическая модель развертывания Azure
description: Классическая модель развертывания, которая теперь заменена моделью Resource Manager, обеспечивает глобальный лимит квот vCPU для виртуальных виртуальных машинистокомплектов.
author: sowmyavenkat86
ms.author: svenkat
ms.date: 01/27/2020
ms.topic: article
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: a3d5106cafc1d3bfe77f3e42e85cedb668fc4fa0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76835633"
---
# <a name="classic-deployment-model"></a>Классическая модель развертывания

Классическая модель развертывания — это модель развертывания Azure старшего поколения. Он обеспечивает глобальный лимит квот vCPU для виртуальных машин и виртуальных наборов масштабов машин. Модель классического развертывания больше не рекомендуется и теперь заменена моделью управления ресурсами.

Чтобы узнать больше об этих двух моделях развертывания и преимуществах использования ресурсного менеджера, [см.](../../azure-resource-manager/management/deployment-models.md)

При создании новой подписки ей назначается квота vCPUs по умолчанию. Всякий раз, когда новая виртуальная машина будет развернута с использованием модели Классического развертывания, сумма нового и существующего использования VCPU во всех регионах не должна превышать квоту VCPU, утвержденную для модели классического развертывания.

Чтобы узнать больше о квотах, смотрите [ограничения подписки и обслуживания Azure, квоты и ограничения.](../../azure-resource-manager/management/azure-subscription-service-limits.md)

Можно запросить увеличение лимита квоты vCPU для модели развертывания Classic. Используйте **квоты** **справки и поддержки** или использования на портале Azure.

## <a name="request-per-vm-series-vcpu-quota-increase-at-subscription-level-using-help--support"></a>Запрос на vM серии vCPU увеличение квоты на уровне подписки с помощью справки и поддержки

Следуйте инструкциям ниже, чтобы создать запрос на поддержку, используя **поддержку Справки и на** портале Azure.

1. Из меню [портала Azure](https://portal.azure.com) выберите **поддержку Справки .**

   ![Выберите справку и поддержку на портале Azure](./media/resource-manager-core-quotas-request/help-plus-support.png)

1. Выберите **новый запрос на поддержку**.

   ![Создание нового запроса на поддержку на портале Azure](./media/resource-manager-core-quotas-request/new-support-request.png)

1. В **типе выпуска**выберите **ограничения службы и подписки (квоты).**

   ![Выберите квоты в качестве типа проблемы](./media/resource-manager-core-quotas-request/select-quota-issue-type.png)

1. Выберите подписку, квоту которой вы хотите увеличить.

   ![Выберите подписку, для которой увеличить квоту](./media/resource-manager-core-quotas-request/select-subscription-support-request.png)

1. Для **типа квоты,** выберите **Вычислить -VM (cores-vCPUs) ограничение подписки увеличивается.**

   ![Выберите тип квоты для увеличения](./media/resource-manager-core-quotas-request/select-quota-type.png)

1. Выберите **Далее: Решения** для открытия **PROBLEM DETAILS**. Выберите **предоставить подробную информацию,** чтобы предоставить дополнительную информацию.

   ![Предоставьте подробную информацию, чтобы помочь вашему запросу вместе](./media/resource-manager-core-quotas-request/provide-details-link.png)

1. В **деталях квоты**выберите **Классический** и выберите **местоположение.**

   ![Добавление деталей, включая модель развертывания и местоположение](./media/resource-manager-core-quotas-request/quota-details-classic.png)

1. Для **семьи SKU**, выберите один или несколько семей SKU увеличить.

   ![Укажите семейство SKU для увеличения](./media/resource-manager-core-quotas-request/sku-family-classic.png)

1. Введите желаемые ограничения для подписки. Чтобы удалить строку, отображайте SKU из **семейства SKU** или выберите значок отбрасывательства "X". После ввода квоты для каждой семьи SKU выберите **Сохранить и продолжить** в **деталях квоты,** чтобы продолжить запрос на поддержку.

   ![Запрос новых ограничений](./media/resource-manager-core-quotas-request/new-limits-classic.png)

## <a name="request-per-vm-series-vcpu-quota-increase-at-subscription-level-using-usage--quotas"></a>Запрос на vM серии vCPU увеличение квоты на уровне подписки с использованием использования и квот

Следуйте инструкциям ниже, чтобы создать запрос на поддержку, используя **квоты «Использование»** на портале Azure.

1. На [портале Azure](https://portal.azure.com) **ищите подписки.**

   ![Перейти к подпискам на портале Azure](./media/resource-manager-core-quotas-request/search-for-subscriptions.png)

1. Выберите подписку, квоту которой вы хотите увеличить.

   ![Выберите подписку для изменения](./media/resource-manager-core-quotas-request/select-subscription-change-quota.png)

1. Выберите **Квоты использования и квоты**.

   ![Выберите использование и квоты для подписки](./media/resource-manager-core-quotas-request/select-usage-plus-quotas.png)

1. В правом верхнем углу выберите **увеличение запроса.**

   ![Выберите для увеличения квоты](./media/resource-manager-core-quotas-request/request-increase-from-subscription.png)

1. Выберите **лимит подписки Compute-VM (cores-vCPUs) по** мере увеличения **типа квоты.**

   ![Выберите тип квоты](./media/resource-manager-core-quotas-request/select-quota-type.png)

1. Выберите **Далее: Решения** для открытия **PROBLEM DETAILS**. Выберите **предоставить подробную информацию,** чтобы предоставить дополнительную информацию.

   ![Предоставьте подробную информацию для вашего запроса](./media/resource-manager-core-quotas-request/provide-details-link.png)

1. В **деталях квоты**, выберите **Классический** и **Местоположение**.

   ![Выберите детали квоты, включая модель развертывания и местоположение](./media/resource-manager-core-quotas-request/quota-details-classic.png)

1. Выберите одну или несколько семейств SKU для увеличения.

   ![Выберите семейство SKU для увеличения](./media/resource-manager-core-quotas-request/sku-family-classic.png)

1. Введите желаемые ограничения для подписки. Чтобы удалить строку, отображайте SKU из **семейства SKU** или выберите значок отбрасывательства "X". После ввода квоты для каждой семьи SKU выберите **Сохранить и продолжить** в **деталях квоты,** чтобы продолжить запрос на поддержку.

   ![Ввести новую квоту](./media/resource-manager-core-quotas-request/new-limits-classic.png)

