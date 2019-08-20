---
title: Устранение ошибок при перемещении ресурсов Azure в новую подписку или группу ресурсов
description: Перемещайте ресурсы в новую группу ресурсов или новую подписку с помощью Azure Resource Manager.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 08/19/2019
ms.author: tomfitz
ms.openlocfilehash: 445ee2784a74a366089a49a0e2f2f17d51ef93bf
ms.sourcegitcommit: 55e0c33b84f2579b7aad48a420a21141854bc9e3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/19/2019
ms.locfileid: "69624302"
---
# <a name="troubleshoot-moving-azure-resources-to-new-resource-group-or-subscription"></a>Устранение неполадок при перемещении ресурсов Azure в новую группу ресурсов или подписку

В этой статье содержатся рекомендации по устранению проблем при перемещении ресурсов.

## <a name="upgrade-a-subscription"></a>Обновление подписки

Если вы действительно хотите обновить подписку Azure (например, переключиться с бесплатного уровня до уровня с оплатой по мере использования), необходимо преобразовать вашу подписку.

* Сведения о том, как перейти с бесплатной пробной версии или подписки Azure Microsoft Imagine на подписку с оплатой по мере использования, см. [здесь](../billing/billing-upgrade-azure-subscription.md).
* Сведения о том, как перейти с подписки с оплатой по мере использования на другое предложение, см. [здесь](../billing/billing-how-to-switch-azure-offer.md).

Если вам не удается перейти на другую подписку, [создайте запрос на поддержку Azure](../azure-supportability/how-to-create-azure-support-request.md). Выберите тип проблемы **Управление подпиской**.

## <a name="service-limitations"></a>Ограничения службы

При перемещении ресурсов некоторые службы нуждаются в дополнительных вопросах. Если вы перемещаете следующие службы, убедитесь, что вы проверите рекомендации и ограничения.

* [Службы приложений](./move-limitations/app-service-move-limitations.md)
* [Azure DevOps Services](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json)
* [Классическая модель развертывания](./move-limitations/classic-model-move-limitations.md)
* [Сеть](./move-limitations/networking-move-limitations.md)
* [Службы восстановления](../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json)
* [Виртуальные машины](./move-limitations/virtual-machines-move-limitations.md)

## <a name="large-requests"></a>Крупные запросы

Если это возможно, разделите большие перемещения на несколько операций перемещения. При попытке перемещения более чем 800 ресурсов за одну операцию Resource Manager немедленно возвращает ошибку. Однако перемещение менее чем 800 ресурсов может также завершиться со сбоем из-за превышения времени ожидания.

## <a name="resource-not-in-succeeded-state"></a>Ресурс не находится в состоянии "неудачно"

Если появляется сообщение об ошибке, указывающее, что ресурс нельзя переместить, так как он не находится в состоянии "успех", он может быть зависимым ресурсом, блокирующим перемещение. См. [состояние зависимых ресурсов](./move-limitations/networking-move-limitations.md#state-of-dependent-resources).

## <a name="next-steps"></a>Следующие шаги

Команды для перемещения ресурсов см. в статье [Перемещение ресурсов в новую группу ресурсов или подписку](resource-group-move-resources.md).
