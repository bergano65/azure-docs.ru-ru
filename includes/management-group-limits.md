---
title: Включить имя файла
description: включить файл
author: tfitzmac
ms.service: governance
ms.topic: include
ms.date: 03/26/2020
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 9b2bf8763d175bca028be1fcceda921e87eb5b23
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "80334615"
---
| Ресурс | Ограничение |
| --- | --- |
| Группы управления на каталог | 10 000 |
| Подписки на группу управления | Без ограничений. |
| Уровни иерархии групп управления | Корневой уровень плюс 6 уровня<sup>1</sup> |
| Непосредственная родительская группа управления для каждой группы управления | Один |
| [Развертывания на уровне группы управления](../articles/azure-resource-manager/templates/deploy-to-management-group.md) на расположение | 800<sup>2</sup> |

<sup>1</sup> 6 уровней не включают уровень подписки.

<sup>2</sup> Если достигнут предел в 800 развертывания, удалите из журнала развертывания, которые больше не нужны. Чтобы удалить развертывания на уровне группы управления, используйте командлет [Remove-азманажементграупдеплоймент](/powershell/module/az.resources/Remove-AzManagementGroupDeployment) или [AZ Deployment mg Delete](/cli/azure/deployment/mg?view=azure-cli-latest#az-deployment-mg-delete).
