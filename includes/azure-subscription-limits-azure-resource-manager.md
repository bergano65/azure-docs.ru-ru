---
title: Включить имя файла
description: включить файл
services: azure-resource-manager
author: tfitzmac
ms.service: cost-management-billing
ms.topic: include
ms.date: 03/26/2020
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 94fd7e692be31ba247e3342246d3940ed08ef9b7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80334980"
---
| Ресурс | Ограничение |
| --- | --- |
| Подписки на Azure Active Directory клиент | Без ограничений. |
| [Соадминистраторов](../articles/cost-management-billing/manage/add-change-subscription-administrator.md) на подписку |Без ограничений. |
| [Групп ресурсов](../articles/azure-resource-manager/management/overview.md) на подписку |980 |
| Размер запроса API Azure Resource Manager |4 194 304 байт. |
| Теги на подписку<sup>1</sup> |50 |
| Вычисление уникальных тегов на подписку<sup>1</sup> | 10 000 |
| [Развертывания на уровне подписки](../articles/azure-resource-manager/templates/deploy-to-subscription.md) на расположение | 800<sup>2</sup> |

<sup>1</sup> К подписке можно напрямую применить до 50 тегов. Однако подписка может содержать неограниченное количество тегов, применяемых к группам ресурсов и ресурсам в рамках подписки. Число тегов на ресурс или группу ресурсов ограничено 50. Диспетчер ресурсов возвращает [список уникальных имен и значений тегов](/rest/api/resources/tags) в подписке, только если число тегов 10 000 или меньше. По-прежнему можно найти ресурс по тегу, если число превышает 10 000.  

<sup>2</sup> Если достигнут предел в 800 развертывания, удалите из журнала развертывания, которые больше не нужны. Чтобы удалить развертывания уровня подписки, используйте командлет [Remove-аздеплоймент](/powershell/module/az.resources/Remove-AzDeployment) или [AZ Deployment redelete](/cli/azure/deployment/sub?view=azure-cli-latest#az-deployment-sub-delete).
