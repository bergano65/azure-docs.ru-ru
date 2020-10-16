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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "80334980"
---
| Ресурс | Ограничение |
| --- | --- |
| Количество подписок на клиент Azure Active Directory | Без ограничений. |
| [Соадминистраторы](../articles/cost-management-billing/manage/add-change-subscription-administrator.md) на подписку |Без ограничений. |
| Количество [групп ресурсов](../articles/azure-resource-manager/management/overview.md) на подписку |980 |
| Размер запроса API Azure Resource Manager |4 194 304 байт. |
| Количество тегов на подписку<sup>1</sup> |50 |
| Уникальный тег вычисления для каждой подписки<sup>1</sup> | 10 000 |
| Количество [развертываний на уровне подписки](../articles/azure-resource-manager/templates/deploy-to-subscription.md) на расположение | 800<sup>2</sup> |

<sup>1</sup> Вы можете применить до 50 тегов непосредственно к подписке. Но подписка может содержать неограниченное количество тегов, применяемых к группам ресурсов и ресурсам в рамках подписки. Количество тегов на ресурс или группу ресурсов ограничено значением 50. Resource Manager возвращает [список уникальных имен и значений тегов](/rest/api/resources/tags) в подписке, только если число тегов равно 10 000 или меньше. Вы по-прежнему можете найти ресурс по тегу, если это число превышает 10 000.  

<sup>2</sup> Если вы достигли предела в 800 развертываний, удалите из журнала те развертывания, которые больше не нужны. Удалить развертывания на уровне подписки можно с помощью [Remove-AzDeployment](/powershell/module/az.resources/Remove-AzDeployment) или [az deployment sub delete](/cli/azure/deployment/sub?view=azure-cli-latest#az-deployment-sub-delete).
