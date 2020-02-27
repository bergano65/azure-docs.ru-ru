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
ms.openlocfilehash: b7db49d09d2292c08ce33ce86b1b7f427ef75fbc
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/26/2020
ms.locfileid: "77618496"
---
| Ресурс | Ограничение по умолчанию | Максимальное ограничение |
| --- | --- | --- |
| Подписки на Azure Active Directory клиент | Без ограничений. | Без ограничений. |
| [Соадминистраторов](../articles/cost-management-billing/manage/add-change-subscription-administrator.md) на подписку |Без ограничений. |Без ограничений. |
| [Групп ресурсов](../articles/azure-resource-manager/management/overview.md) на подписку |980 |980 |
| Размер запроса API Azure Resource Manager |4 194 304 байт. |4 194 304 байт. |
| Теги на подписку<sup>1</sup> |Без ограничений. |Без ограничений. |
| Вычисление уникальных тегов на подписку<sup>1</sup> | 10 000 | 10 000 |
| [Развертывания на уровне подписки](../articles/azure-resource-manager/templates/deploy-to-subscription.md) на расположение | 800<sup>2</sup> | 800 |

<sup>1</sup> Можно применить неограниченное количество тегов на подписку. Число тегов на ресурс или группу ресурсов ограничено 50. Диспетчер ресурсов возвращает [список уникальных имен и значений тегов](/rest/api/resources/tags) в подписке, только если число тегов 10 000 или меньше. По-прежнему можно найти ресурс по тегу, если число превышает 10 000.  

<sup>2</sup> Если достигнут предел в 800 развертывания, удалите из журнала развертывания, которые больше не нужны. Чтобы удалить развертывания уровня подписки, используйте командлет [Remove-аздеплоймент](/powershell/module/az.resources/Remove-AzDeployment) или [AZ Deployment Delete](/cli/azure/deployment?view=azure-cli-latest#az-deployment-delete).
