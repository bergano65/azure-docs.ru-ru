---
title: включить файл
description: включить файл
author: tfitzmac
ms.service: governance
ms.topic: include
ms.date: 03/26/2020
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 9b2bf8763d175bca028be1fcceda921e87eb5b23
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334615"
---
| Ресурс | Ограничение |
| --- | --- |
| Группы управления в каталоге | 10 000 |
| Подписка на одну группу управления | Без ограничений. |
| Уровни иерархии групп управления | Уровень корня плюс 6 уровней<sup>1</sup> |
| Группа прямого управления родителями на одну управленческую группу | Один |
| [Развертывание группы управления](../articles/azure-resource-manager/templates/deploy-to-management-group.md) на уровне местоположения | 800<sup>2</sup> |

<sup>1</sup> 6 уровней не включают уровень подписки.

<sup>2</sup> Если вы достигнете предела 800 развертываний, удалите развертывания из истории, которые больше не нужны. Чтобы удалить развертывания группы управления, используйте [Удалить-AzManagementGroupDeployment](/powershell/module/az.resources/Remove-AzManagementGroupDeployment) или [аз развертывания мг удалить](/cli/azure/deployment/mg?view=azure-cli-latest#az-deployment-mg-delete).
