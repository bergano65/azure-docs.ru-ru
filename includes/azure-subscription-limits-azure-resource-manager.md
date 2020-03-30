---
title: включить файл
description: включить файл
services: azure-resource-manager
author: tfitzmac
ms.service: cost-management-billing
ms.topic: include
ms.date: 03/26/2020
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 94fd7e692be31ba247e3342246d3940ed08ef9b7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334980"
---
| Ресурс | Ограничение |
| --- | --- |
| Подписка на арендатора active каталога Azure | Без ограничений. |
| [Коадминистраторы](../articles/cost-management-billing/manage/add-change-subscription-administrator.md) за подписку |Без ограничений. |
| [Группы ресурсов](../articles/azure-resource-manager/management/overview.md) на подписку |980 |
| Размер запроса API-запроса на aPI-запроса API |4 194 304 байта. |
| Теги за подписку<sup>1</sup> |50 |
| Уникальные расчеты тегов за подписку<sup>1</sup> | 10 000 |
| [Развертывание на уровне подписки](../articles/azure-resource-manager/templates/deploy-to-subscription.md) на место | 800<sup>2</sup> |

<sup>1</sup> Вы можете применить до 50 тегов непосредственно к подписке. Однако подписка может содержать неограниченное количество тегов, применяемых к группам ресурсов и ресурсам в рамках подписки. Количество тегов на ресурс или группу ресурсов ограничено 50. Менеджер ресурсов возвращает [список уникальных имен и значений тегов](/rest/api/resources/tags) в подписке только тогда, когда количество тегов составляет 10 000 или меньше. Вы все еще можете найти ресурс по тегу, когда число превышает 10000.  

<sup>2</sup> Если вы достигнете предела 800 развертываний, удалите развертывания из истории, которые больше не нужны. Чтобы удалить развертывания уровня подписки, используйте [удалить-AzDeployment](/powershell/module/az.resources/Remove-AzDeployment) или [аз-развертывания суб-удалить](/cli/azure/deployment/sub?view=azure-cli-latest#az-deployment-sub-delete).
