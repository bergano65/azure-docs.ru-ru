---
title: Устранение ошибок при перемещении ресурсов Azure в новую группу ресурсов или подписку
description: Перемещайте ресурсы в новую группу ресурсов или новую подписку с помощью Azure Resource Manager.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: tomfitz
ms.openlocfilehash: e23d7c571a010e5bfb42e5f15368e0194272ed53
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/10/2019
ms.locfileid: "67723471"
---
# <a name="troubleshoot-moving-azure-resources-to-new-resource-group-or-subscription"></a>Устранение неполадок перемещение ресурсов Azure в новую группу ресурсов или подписку

Эта статья содержит рекомендации, чтобы способствовать разрешению проблем при перемещении ресурсов.

## <a name="upgrade-a-subscription"></a>Обновление подписки

Если вы действительно хотите обновить подписку Azure (например, переключиться с бесплатного уровня до уровня с оплатой по мере использования), необходимо преобразовать вашу подписку.

* Сведения о том, как перейти с бесплатной пробной версии или подписки Azure Microsoft Imagine на подписку с оплатой по мере использования, см. [здесь](../billing/billing-upgrade-azure-subscription.md).
* Сведения о том, как перейти с подписки с оплатой по мере использования на другое предложение, см. [здесь](../billing/billing-how-to-switch-azure-offer.md).

Если вам не удается перейти на другую подписку, [создайте запрос на поддержку Azure](../azure-supportability/how-to-create-azure-support-request.md). Выберите тип проблемы **Управление подпиской**.

## <a name="service-limitations"></a>Ограничения службы

Некоторые службы требуют Дополнительные соображения при перемещении ресурсов. Если вы перемещаете следующие службы, убедитесь, что выбран рекомендации и ограничения.

* [Службы приложений](./move-limitations/app-service-move-limitations.md)
* [Azure DevOps Services](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json)
* [Классическая модель развертывания](./move-limitations/classic-model-move-limitations.md)
* [Службы восстановления](../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json)
* [Виртуальные машины](./move-limitations/virtual-machines-move-limitations.md)
* [Виртуальные сети](./move-limitations/virtual-network-move-limitations.md)

## <a name="large-requests"></a>Больших запросов

Если это возможно, разделите большие перемещения на несколько операций перемещения. При попытке перемещения более чем 800 ресурсов за одну операцию Resource Manager немедленно возвращает ошибку. Однако перемещение менее чем 800 ресурсов может также завершиться со сбоем из-за превышения времени ожидания.

## <a name="next-steps"></a>Следующие шаги

Команды для перемещения ресурсов см. в статье [Перемещение ресурсов в новую группу ресурсов или подписку](resource-group-move-resources.md).
