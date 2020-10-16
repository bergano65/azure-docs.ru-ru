---
title: включить файл
description: включить файл
author: tfitzmac
ms.service: governance
ms.topic: include
ms.date: 03/26/2020
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 204ca121045d1bd53fd507acc7ea9691bfeac12e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "88748783"
---
| Ресурс | Ограничение |
| --- | --- |
| Группы управления на арендатор Azure AD | 10 000 |
| Подписки на группу управления | Без ограничений. |
| Уровни иерархии групп управления | Корневой уровень плюс 6 уровней<sup>1</sup> |
| Непосредственная родительская группа управления на группу управления | Один |
| [Развертывания уровня группы управления](../articles/azure-resource-manager/templates/deploy-to-management-group.md) на расположение | 800<sup>2</sup> |

<sup>1</sup> 6 уровней не включают уровень подписки.

<sup>2</sup> Если вы достигли предела в 800 развертываний, удалите из журнала те развертывания, которые больше не нужны. Чтобы удалить развертывания уровня группы управления, выполните [Remove-AzManagementGroupDeployment](/powershell/module/az.resources/Remove-AzManagementGroupDeployment) или [az deployment mg delete](/cli/azure/deployment/mg?view=azure-cli-latest#az-deployment-mg-delete).
