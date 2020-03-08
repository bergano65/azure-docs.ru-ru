---
title: Различия между API-интерфейсами управления и API службы — Пакетная служба Azure | Документация Майкрософт
description: API-интерфейсы работают на разных уровнях пакетной службы Azure.
services: batch
author: LauraBrenner
manager: evansma
ms.service: batch
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: 181515c0f497af8ffadcb909c13e51a40bfbf3b0
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2020
ms.locfileid: "78672764"
---
# <a name="service-level-and-management-level-apis"></a>API уровня обслуживания и уровня управления

Пакетная служба Azure имеет два набора интерфейсов API: один для уровня обслуживания и один для уровня управления. Именование часто аналогично, но они возвращают разные результаты. Если вам нужны журналы действий, необходимо использовать API управления. API уровня обслуживания обходят уровень управления ресурсами Azure и не регистрируются в журнале.


Для управления пакетной службой и пакетной службы предусмотрены интерфейсы API для пула, например. 
- Этот API для удаления пула предназначен непосредственно для учетной записи пакетной службы: https://docs.microsoft.com/rest/api/batchservice/pool/delete 

- Этот API для удаления пула https://docs.microsoft.com/rest/api/batchmanagement/pool/delete предназначен для слоя management.azure.com.

