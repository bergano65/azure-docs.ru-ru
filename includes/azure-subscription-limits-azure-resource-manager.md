---
title: включить файл
description: включить файл
services: billing
author: rothja
ms.service: cost-management-billing
ms.topic: include
ms.date: 07/22/2019
ms.author: jroth
ms.custom: include file
ms.openlocfilehash: d94937a738034904413eac8b256121f14221d1ac
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/29/2020
ms.locfileid: "76845964"
---
| Ресурс | Ограничение по умолчанию | Максимальное ограничение |
| --- | --- | --- |
| Число ВМ на [подписку](../articles/billing-buy-sign-up-azure-subscription.md) |25 000<sup>1</sup> на регион. |25 000. США за регион. |
| Общее количество ядер виртуальных машин на [подписку](../articles/billing-buy-sign-up-azure-subscription.md) |20<sup>1</sup> на регион. | Обратитесь в службу поддержки. |
| Общее число ядер виртуальной машины Azure на [подписку](../articles/billing-buy-sign-up-azure-subscription.md) |20<sup>1</sup> на регион. | Обратитесь в службу поддержки. |
| Виртуальная машина на ряд, например Dv2 и F, количество ядер на [подписку](../articles/billing-buy-sign-up-azure-subscription.md) |20<sup>1</sup> на регион. | Обратитесь в службу поддержки. |
| [Учетные записи хранения](../articles/storage/common/storage-account-create.md) на подписку в каждом регионе |250 |250 |
| [Групп доступности](../articles/virtual-machines/windows/manage-availability.md#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy) на подписку |2 000. США за регион. |2 000. США за регион. |
| [Территориальные группы](../articles/virtual-network/virtual-networks-migrate-to-regional-vnet.md) на подписку |Н/д<sup>3</sup> |Н/д<sup>3</sup> |
| [Облачные службы](../articles/cloud-services/cloud-services-choose-me.md) на подписку |Н/д<sup>3</sup> |Н/д<sup>3</sup> |
| [Групп ресурсов](../articles/azure-resource-manager/management/overview.md) на подписку |980 |980 |
| Размер запроса API Azure Resource Manager |4 194 304 байт. |4 194 304 байт. |
| Теги на подписку<sup>2</sup> |Без ограничений. |Без ограничений. |
| Вычисление уникальных тегов на подписку<sup>2</sup> | 10 000 | 10 000 |
| [Развертывания на уровне подписки](../articles/azure-resource-manager/templates/deploy-to-subscription.md) на расположение | 800<sup>4</sup> | 800 |
| Подписки на Azure Active Directory клиент | Без ограничений. | Без ограничений. |
| [Соадминистраторов](../articles/cost-management-billing/manage/add-change-subscription-administrator.md) на подписку |Без ограничений. |Без ограничений. |

<sup>1</sup> Ограничения по умолчанию зависят от типа категории предложения, например бесплатной пробной версии и оплаты по мере использования, а также ряда, таких как Dv2, F и G. Например, значение по умолчанию для подписок Соглашение Enterprise — 350.

<sup>2</sup> Можно применить неограниченное количество тегов на подписку. Число тегов на ресурс или группу ресурсов ограничено 50. Диспетчер ресурсов возвращает [список уникальных имен и значений тегов](/rest/api/resources/tags) в подписке, только если число тегов 10 000 или меньше. По-прежнему можно найти ресурс по тегу, если число превышает 10 000.  

<sup>3</sup> Эти функции больше не требуются для групп ресурсов Azure и диспетчер ресурсов.

<sup>4</sup> Если достигнут предел в 800 развертывания, удалите из журнала развертывания, которые больше не нужны. Чтобы удалить развертывания уровня подписки, используйте командлет [Remove-аздеплоймент](/powershell/module/az.resources/Remove-AzDeployment) или [AZ Deployment Delete](/cli/azure/deployment?view=azure-cli-latest#az-deployment-delete).

> [!NOTE]
> Общее количество ядер виртуальных машин ограничено региональными ограничениями. Кроме того, у них есть ограничение для отдельных серий по размеру, например Dv2 и F. Эти ограничения применяются отдельно. Например, рассмотрим подписку с ограничением до 30 ядер виртуальных машин с восточной части США, 30 ядер серии A и 30 ядер серии D. Эта подписка может развернуть 30 виртуальных машин a1 или 30 виртуальных машин D1 или сочетание двух, не превышающих 30 ядер. Примером сочетания может быть 10 виртуальных машин a1 и 20 виртуальных машин D1.  
> <!-- -->
> 
> 

