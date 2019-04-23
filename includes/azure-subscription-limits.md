---
title: включение файла
description: включение файла
services: billing
author: rothja
ms.service: billing
ms.topic: include
ms.date: 05/18/2018
ms.author: jroth
ms.custom: include file
ms.openlocfilehash: e8fe694cc757f5bcb9cf470f17306e8aa0028744
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59805008"
---
| Ресурс | Ограничение по умолчанию | Максимальное ограничение |
| --- | --- | --- |
| виртуальных ЦП на [подписки](../articles/billing-buy-sign-up-azure-subscription.md)<sup>1</sup> |20 |10 000 |
| [Соадминистраторы](../articles/billing-add-change-azure-subscription-administrator.md) на подписку |200 |200 |
| [Учетных записей хранения](../articles/storage/common/storage-create-storage-account.md) на подписку<sup>2</sup> |100 |100 |
| [Облачные службы](../articles/cloud-services/cloud-services-choose-me.md) на подписку |20 |200 |
| [Локальные сети](/previous-versions/azure/reference/jj157100(v=azure.100)) на подписку |10 |500 |
| Серверы DNS на подписку |9 |100 |
| Зарезервированные IP на подписку |20 |100 |
| [Территориальные группы](../articles/virtual-network/virtual-networks-migrate-to-regional-vnet.md) на подписку |256 |256 |
| Длина имени подписки (символов) | 64 | 64 |

<sup>1</sup>количество мелких экземпляров extra как один виртуальный ЦП подсчете предельного количества виртуальных процессоров, несмотря на использование частичную загрузку ядра ЦП.

<sup>2</sup>Ограничение учетной записи хранения распространяются на учетные записи хранения класса "Стандартный" и "Премиум". 

