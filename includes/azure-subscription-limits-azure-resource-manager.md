---
title: включить файл
description: включить файл
services: azure-resource-manager
author: tfitzmac
ms.service: cost-management-billing
ms.topic: include
ms.date: 02/10/2020
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 32d39493e526a4b95369e2998d3f9ade9f2964dc
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/11/2020
ms.locfileid: "77133725"
---
| Ресурс | Ограничение по умолчанию | Максимальное ограничение |
| --- | --- | --- |
| [Групп ресурсов](../articles/azure-resource-manager/management/overview.md) на подписку |980 |980 |
| Размер запроса API Azure Resource Manager |4 194 304 байт. |4 194 304 байт. |
| Теги на подписку<sup>1</sup> |Без ограничений. |Без ограничений. |
| Вычисление уникальных тегов на подписку<sup>1</sup> | 10 000 | 10 000 |
| [Развертывания на уровне подписки](../articles/azure-resource-manager/templates/deploy-to-subscription.md) на расположение | 800<sup>2</sup> | 800 |
| Подписки на Azure Active Directory клиент | Без ограничений. | Без ограничений. |
| [Соадминистраторов](../articles/cost-management-billing/manage/add-change-subscription-administrator.md) на подписку |Без ограничений. |Без ограничений. |

<sup>1</sup> Можно применить неограниченное количество тегов на подписку. Число тегов на ресурс или группу ресурсов ограничено 50. Диспетчер ресурсов возвращает [список уникальных имен и значений тегов](/rest/api/resources/tags) в подписке, только если число тегов 10 000 или меньше. По-прежнему можно найти ресурс по тегу, если число превышает 10 000.  

<sup>2</sup> Если достигнут предел в 800 развертывания, удалите из журнала развертывания, которые больше не нужны. Чтобы удалить развертывания уровня подписки, используйте командлет [Remove-аздеплоймент](/powershell/module/az.resources/Remove-AzDeployment) или [AZ Deployment Delete](/cli/azure/deployment?view=azure-cli-latest#az-deployment-delete).
