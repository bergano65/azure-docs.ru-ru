---
title: включить файл
description: включить файл
services: billing
author: rothja
ms.service: cost-management-billing
ms.topic: include
ms.date: 05/18/2018
ms.author: jroth
ms.custom: include file
ms.openlocfilehash: f49d91a2dffbd02067efd82b88963b5cb6eaa8b4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "76021270"
---
| Ресурс | Ограничение по умолчанию | Максимальное ограничение |
| --- | --- | --- |
| Число виртуальных ЦП на [подписку](../articles/billing-buy-sign-up-azure-subscription.md)<sup>1</sup> |20 |10 000 |
| [Коадминистраторы](../articles/cost-management-billing/manage/add-change-subscription-administrator.md) за подписку |200 |200 |
| [Учетных записей хранения](../articles/storage/common/storage-create-storage-account.md) на подписку<sup>2</sup> |100 |100 |
| [Облачные службы](../articles/cloud-services/cloud-services-choose-me.md) на подписку |20 |200 |
| [Локальные сети](/previous-versions/azure/reference/jj157100(v=azure.100)) на подписку |10 |500 |
| Серверы DNS на подписку |9 |100 |
| Зарезервированные IP на подписку |20 |100 |
| [Территориальные группы](../articles/virtual-network/virtual-networks-migrate-to-regional-vnet.md) на подписку |256 |256 |
| Длина названия подписки (символы) | 64 | 64 |

<sup>1</sup> Дополнительные небольшие экземпляры считаются одним vCPU к пределу vCPU, несмотря на использование частичного ядра процессора.

<sup>2</sup>Ограничение учетной записи хранения распространяются на учетные записи хранения класса "Стандартный" и "Премиум". 

